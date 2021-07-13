worker is the main object which takes care of submitting new work to consensus engine and gathering the sealing result.

```go
type worker struct {
    config      *Config
    chainConfig *params.ChainConfig
    engine      consensus.Engine
    eth         Backend
    chain       *core.BlockChain

    // Feeds
    pendingLogsFeed event.Feed

    // Subscriptions
    mux          *event.TypeMux
    txsCh        chan core.NewTxsEvent
    txsSub       event.Subscription
    chainHeadCh  chan core.ChainHeadEvent
    chainHeadSub event.Subscription
    chainSideCh  chan core.ChainSideEvent
    chainSideSub event.Subscription

    // Channels
    newWorkCh          chan *newWorkReq
    taskCh             chan *task
    resultCh           chan *types.Block
    startCh            chan struct{}
    exitCh             chan struct{}
    resubmitIntervalCh chan time.Duration
    resubmitAdjustCh   chan *intervalAdjust

    current      *environment                 // An environment for current running cycle.
    localUncles  map[common.Hash]*types.Block // A set of side blocks generated locally as the possible uncle blocks.
    remoteUncles map[common.Hash]*types.Block // A set of side blocks as the possible uncle blocks.
    unconfirmed  *unconfirmedBlocks           // A set of locally mined blocks pending canonicalness confirmations.

    mu       sync.RWMutex // The lock used to protect the coinbase and extra fields
    coinbase common.Address
    extra    []byte

    pendingMu    sync.RWMutex
    pendingTasks map[common.Hash]*task

    snapshotMu    sync.RWMutex // The lock used to protect the block snapshot and state snapshot
    snapshotBlock *types.Block
    snapshotState *state.StateDB

    // atomic status counters
    running int32 // The indicator whether the consensus engine is running or not.
    newTxs  int32 // New arrival transaction count since last sealing work submitting.

    // noempty is the flag used to control whether the feature of pre-seal empty
    // block is enabled. The default value is false(pre-seal is enabled by default).
    // But in some special scenario the consensus engine will seal blocks instantaneously,
    // in this case this feature will add all empty blocks into canonical chain
    // non-stop and no real transaction will be included.
    noempty uint32

    // External functions
    isLocalBlock func(block *types.Block) bool // Function used to determine whether the specified block is mined by local miner.

    // Test hooks
    newTaskHook  func(*task)                        // Method to call upon receiving a new sealing task.
    skipSealHook func(*task) bool                   // Method to decide whether skipping the sealing.
    fullTaskHook func()                             // Method to call before pushing the full sealing task.
    resubmitHook func(time.Duration, time.Duration) // Method to call upon updating resubmitting interval.
}
```

上游：eth 是个接口

上游：chain

下游：engine 也是个接口

上游配置：chainConfig

自己的配置：config

根据资料，由以下 4 个方法开启 4 个线程处理挖矿相关操作：

* newWorkLoop
* mainLoop
* taskLoop
* resultLoop



