发起转账交易：

personal.unlockAccount\(eth.accounts\[0\]\)

eth.sendTransaction

账号

```
// Account is the Ethereum consensus representation of accounts.
// These objects are stored in the main account trie.
type Account struct {
    Nonce    uint64
    Balance  *big.Int
    Root     common.Hash // merkle root of the storage trie
    CodeHash []byte
}
```

以太坊世界状态的改变是通过操作 stateObject 进行的。

交易

    type Transaction struct {
        data txdata
        // caches
        hash atomic.Value
        size atomic.Value
        from atomic.Value
    }

    type txdata struct {
        AccountNonce uint64          `json:"nonce"    gencodec:"required"`
        Price        *big.Int        `json:"gasPrice" gencodec:"required"`
        GasLimit     uint64          `json:"gas"      gencodec:"required"`
        Recipient    *common.Address `json:"to"       rlp:"nil"` // nil means contract creation
        Amount       *big.Int        `json:"value"    gencodec:"required"`
        Payload      []byte          `json:"input"    gencodec:"required"`

        // Signature values
        V *big.Int `json:"v" gencodec:"required"`
        R *big.Int `json:"r" gencodec:"required"`
        S *big.Int `json:"s" gencodec:"required"`

    }

交易进入mempool时的验证, validateTx，promoteExecutables

```
// TxPool contains all currently known transactions. Transactions
// enter the pool when they are received from the network or submitted
// locally. They exit the pool when they are included in the blockchain.
//
// The pool separates processable transactions (which can be applied to the
// current state) and future transactions. Transactions move between those
// two states over time as they are received and processed.
type TxPool struct {
    config       TxPoolConfig
    chainconfig  *params.ChainConfig
    chain        blockChain
    gasPrice     *big.Int
    txFeed       event.Feed
    scope        event.SubscriptionScope
    chainHeadCh  chan ChainHeadEvent
    chainHeadSub event.Subscription
    signer       types.Signer
    mu           sync.RWMutex

    currentState  *state.StateDB      // Current state in the blockchain head
    pendingState  *state.ManagedState // Pending state tracking virtual nonces
    currentMaxGas uint64              // Current gas limit for transaction caps

    locals  *accountSet // Set of local transaction to exempt from eviction rules
    journal *txJournal  // Journal of local transaction to back up to disk

    pending map[common.Address]*txList   // All currently processable transactions
    queue   map[common.Address]*txList   // Queued but non-processable transactions
    beats   map[common.Address]time.Time // Last heartbeat from each known account
    all     *txLookup                    // All transactions to allow lookups
    priced  *txPricedList                // All transactions sorted by price

    wg sync.WaitGroup // for shutdown sync

    homestead bool
}
```

交易在虚拟机中执行

```
// Message is a fully derived transaction and implements core.Message
//
// NOTE: In a future PR this will be removed.
type Message struct {
    to         *common.Address
    from       common.Address
    nonce      uint64
    amount     *big.Int
    gasLimit   uint64
    gasPrice   *big.Int
    data       []byte
    checkNonce bool
}
```

Transaction结构在虚拟机中执行之前都会转换为 Message

```
// AsMessage returns the transaction as a core.Message.
//
// AsMessage requires a signer to derive the sender.
//
// XXX Rename message to something less arbitrary?
func (tx *Transaction) AsMessage(s Signer) (Message, error) {
    msg := Message{
        nonce:      tx.data.AccountNonce,
        gasLimit:   tx.data.GasLimit,
        gasPrice:   new(big.Int).Set(tx.data.Price),
        to:         tx.data.Recipient,
        amount:     tx.data.Amount,
        data:       tx.data.Payload,
        checkNonce: true,
    }

    var err error
    msg.from, err = Sender(s, tx)
    return msg, err
}
```

core/state\_processor.go中的 Process 函数。

调用链：

ApplyTransaction -&gt; ApplyMessage -&gt; NewStateTransition -&gt; TransitionDb

evm.Create 根据参数创建智能合约， evm.Call 根据交易信息在虚拟机中执行这笔交易。

**以太坊中一笔交易的执行流程大致为：**

客户端构造交易 -&gt; 通过p2p网络广播交易 -&gt; 矿工节点收到交易 -&gt; 将交易反序列化为 Transaction 结构 -&gt; 将交易放到mempool -&gt; 矿工挖矿 -&gt; 在EVM中执行这笔交易 -&gt; 交易执行结果写入stateDB

