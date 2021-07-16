每当p2p模块发现一个新的节点并连接完成时，就会调用p2p.Protocol.Run函数。而此函数的注册是在NewProtocolManager中完成的

ProtocolManager.handle方法对每个新到来的连接进行处理。

“握手”完成后:

* 一是告诉peers对象（一个peerSet类型的对象，用来管理所有的peer）和downloader对象有新的节点连接进来了
* 二是与新的节点同步自己所有pending状态的交易（syncTransactions）

所谓的“白名单区块”是你认为的可信任的区块，这些区块的高度和哈希被写在配置文件里的Whitelist字段中。

ProtocolManager.handleMsg, 用来处理接收到的消息的。从对方连接中读取消息，根据消息码的不同进行处理

```go
// handlerConfig is the collection of initialization parameters to create a full
// node network handler.
type handlerConfig struct {
    Database        ethdb.Database            // Database for direct sync insertions
    Chain           *core.BlockChain          // Blockchain to serve data from
    TxPool          txPool                    // Transaction pool to propagate from
    Network         uint64                    // Network identifier to adfvertise
    Sync            downloader.SyncMode       // Whether to fast or full sync
    BloomCache      uint64                    // Megabytes to alloc for fast sync bloom
    EventMux        *event.TypeMux            // Legacy event mux, deprecate for `feed`
    Checkpoint      *params.TrustedCheckpoint // Hard coded checkpoint for sync challenges
    Whitelist       map[uint64]common.Hash    // Hard coded whitelist for sync challenged
    DirectBroadcast bool
}

type handler struct {
    networkID  uint64
    forkFilter forkid.Filter // Fork ID filter, constant across the lifetime of the node

    fastSync        uint32 // Flag whether fast sync is enabled (gets disabled if we already have blocks)
    snapSync        uint32 // Flag whether fast sync should operate on top of the snap protocol
    acceptTxs       uint32 // Flag whether we're considered synchronised (enables transaction processing)
    directBroadcast bool

    checkpointNumber uint64      // Block number for the sync progress validator to cross reference
    checkpointHash   common.Hash // Block hash for the sync progress validator to cross reference

    database ethdb.Database
    txpool   txPool
    chain    *core.BlockChain
    maxPeers int

    downloader   *downloader.Downloader
    stateBloom   *trie.SyncBloom
    blockFetcher *fetcher.BlockFetcher
    txFetcher    *fetcher.TxFetcher
    peers        *peerSet

    eventMux      *event.TypeMux
    txsCh         chan core.NewTxsEvent
    txsSub        event.Subscription
    minedBlockSub *event.TypeMuxSubscription

    whitelist map[uint64]common.Hash

    // channels for fetcher, syncer, txsyncLoop
    txsyncCh chan *txsync
    quitSync chan struct{}

    chainSync *chainSyncer
    wg        sync.WaitGroup
    peerWG    sync.WaitGroup
}
```

对外重要API：

* BroadcastBlock
* BroadcastTransactions

通过调用 `peer.AsyncSend-*` 实现广播。

