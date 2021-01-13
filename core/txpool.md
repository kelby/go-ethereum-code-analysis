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



