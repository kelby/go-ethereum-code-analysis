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



