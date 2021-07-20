交易池，数据结构：

```go
// TxPool contains all currently known transactions. Transactions
// enter the pool when they are received from the network or submitted
// locally. They exit the pool when they are included in the blockchain.
//
// The pool separates processable transactions (which can be applied to the
// current state) and future transactions. Transactions move between those
// two states over time as they are received and processed.
type TxPool struct {
    config      TxPoolConfig
    chainconfig *params.ChainConfig
    chain       blockChain
    gasPrice    *big.Int
    txFeed      event.Feed
    scope       event.SubscriptionScope
    signer      types.Signer
    mu          sync.RWMutex

    istanbul bool // Fork indicator whether we are in the istanbul stage.

    currentState  *state.StateDB // Current state in the blockchain head
    pendingNonces *txNoncer      // Pending state tracking virtual nonces
    currentMaxGas uint64         // Current gas limit for transaction caps

    locals  *accountSet // Set of local transaction to exempt from eviction rules
    journal *txJournal  // Journal of local transaction to back up to disk

    pending map[common.Address]*txList   // All currently processable transactions
    queue   map[common.Address]*txList   // Queued but non-processable transactions
    beats   map[common.Address]time.Time // Last heartbeat from each known account
    all     *txLookup                    // All transactions to allow lookups
    priced  *txPricedList                // All transactions sorted by price

    chainHeadCh     chan ChainHeadEvent
    chainHeadSub    event.Subscription
    reqResetCh      chan *txpoolResetRequest
    reqPromoteCh    chan *accountSet
    queueTxEventCh  chan *types.Transaction
    reorgDoneCh     chan chan struct{}
    reorgShutdownCh chan struct{}  // requests shutdown of scheduleReorgLoop
    wg              sync.WaitGroup // tracks loop, scheduleReorgLoop
}
```

交易提交到txpool中后，还需要广播出去，一方面通知EVM执行该交易，另一方面要把交易信息广播给其他结点。

```go
// promoteTx adds a transaction to the pending (processable) list of transactions
// and returns whether it was inserted or an older was better.
//
// Note, this method assumes the pool lock is held!
func (pool *TxPool) promoteTx(addr common.Address, hash common.Hash, tx *types.Transaction) bool {
    // Try to insert the transaction into the pending queue
    if pool.pending[addr] == nil {
        pool.pending[addr] = newTxList(true)
    }
    list := pool.pending[addr]

    inserted, old := list.Add(tx, pool.config.PriceBump)
    if !inserted {
        // An older transaction was better, discard this
        pool.all.Remove(hash)
        pool.priced.Removed(1)
        pendingDiscardMeter.Mark(1)
        return false
    }
    // Otherwise discard any previous transaction and mark this
    if old != nil {
        pool.all.Remove(old.Hash())
        pool.priced.Removed(1)
        pendingReplaceMeter.Mark(1)
    } else {
        // Nothing was replaced, bump the pending counter
        pendingGauge.Inc(1)
    }
    // Set the potentially new pending nonce and notify any subsystems of the new tx
    pool.pendingNonces.set(addr, tx.Nonce()+1)

    // Successful promotion, bump the heartbeat
    pool.beats[addr] = time.Now()
    return true
}
```

方法解析：

* reset - 初始化交易池后会调用 reset 方法，这个方法会检索区块链当前状态，确保交易池里的内容与区块链状态是匹配的。
* NewTxPool - 在 NewTxPool 方法里，如果本地可以发起交易，并且配置的 Journal 目录不为空，那么从指定的目录加载交易日志。
* loop - loop\(\) 用来接收区块链的事件，负责处理超时的交易和定时写交易日志。
* addTx - addTx 将交易放入交易池中，pool.add\(tx, local\) 会返回一个 bool 类型，如果为 true，则表明这笔交易合法并且交易之前不存在于交易池，这时候调用 promoteExecutables，可以将可处理的交易变成待处理。
* **pool.add\(tx, local\)** - 首先，根据交易哈希值，确定交易池中是否已经有这笔交易，如果有，则退出。接下来调用 validateTx 验证交易是否合法。
* validateTx - validateTx 有很多使用 if 语句的条件判断
* enqueueTx - 将交易放入交易池
* **pool.promoteExecutables** - 该方法的作用是将所有可以处理的交易放入 pending 区，并且移除所有非法交易。

交易池的交易大致分为两种:

* 一种是提交了但还不能执行的，放在 queue 里等待能够被执行（比如 nonce 太高）
* 还有就是等待执行的，放在 pending 里面等待执行。

交易池的数据来源主要来自：

* 本地提交，也就是第三方应用通过调用本地以太坊节点的RPC服务所提交的交易；
* 远程同步，是指通过广播同步的形式，将其他以太坊节点的交易数据同步至本地节点;





