```go
// BlockChain represents the canonical chain given a database with a genesis
// block. The Blockchain manages chain imports, reverts, chain reorganisations.
//
// Importing blocks in to the block chain happens according to the set of rules
// defined by the two stage Validator. Processing of blocks is done using the
// Processor which processes the included transaction. The validation of the state
// is done in the second part of the Validator. Failing results in aborting of
// the import.
//
// The BlockChain also helps in returning blocks from **any** chain included
// in the database as well as blocks that represents the canonical chain. It's
// important to note that GetBlock can return any block and does not need to be
// included in the canonical one where as GetBlockByNumber always represents the
// canonical chain.
type BlockChain struct {
    chainConfig *params.ChainConfig // Chain & network configuration
    cacheConfig *CacheConfig        // Cache configuration for pruning

    db     ethdb.Database // Low level persistent database to store final content in
    snaps  *snapshot.Tree // Snapshot tree for fast trie leaf access
    triegc *prque.Prque   // Priority queue mapping block numbers to tries to gc
    gcproc time.Duration  // Accumulates canonical block processing for trie dumping

    // txLookupLimit is the maximum number of blocks from head whose tx indices
    // are reserved:
    //  * 0:   means no limit and regenerate any missing indexes
    //  * N:   means N block limit [HEAD-N+1, HEAD] and delete extra indexes
    //  * nil: disable tx reindexer/deleter, but still index new blocks
    txLookupLimit uint64

    hc            *HeaderChain
    rmLogsFeed    event.Feed
    chainFeed     event.Feed
    chainSideFeed event.Feed
    chainHeadFeed event.Feed
    logsFeed      event.Feed
    blockProcFeed event.Feed
    scope         event.SubscriptionScope
    genesisBlock  *types.Block

    chainmu sync.RWMutex // blockchain insertion lock

    currentBlock     atomic.Value // Current head of the block chain
    currentFastBlock atomic.Value // Current head of the fast-sync chain (may be above the block chain!)

    stateCache    state.Database // State database to reuse between imports (contains state cache)
    bodyCache     *lru.Cache     // Cache for the most recent block bodies
    bodyRLPCache  *lru.Cache     // Cache for the most recent block bodies in RLP encoded format
    receiptsCache *lru.Cache     // Cache for the most recent receipts per block
    blockCache    *lru.Cache     // Cache for the most recent entire blocks
    txLookupCache *lru.Cache     // Cache for the most recent transaction lookup data.
    futureBlocks  *lru.Cache     // future blocks are blocks added for later processing

    quit          chan struct{}  // blockchain quit channel
    wg            sync.WaitGroup // chain processing wait group for shutting down
    running       int32          // 0 if chain is running, 1 when stopped
    procInterrupt int32          // interrupt signaler for block processing

    engine     consensus.Engine
    validator  Validator  // Block and state validator interface
    prefetcher Prefetcher // Block state prefetcher interface
    processor  Processor  // Block transaction processor interface
    vmConfig   vm.Config

    shouldPreserve     func(*types.Block) bool        // Function used to determine whether should preserve the given block.
    terminateInsert    func(common.Hash, uint64) bool // Testing hook used to terminate ancient receipt chain insertion.
    writeLegacyJournal bool                           // Testing flag used to flush the snapshot journal in legacy format.
}
```

blockchain.go中实现的BlockChain结构及方法是核心实现

如何判断是否主链？

新插入区块时会判断当前区块所在的分支是否已经成为主链了。判断的代码在BlockChain.writeBlockWithState中

如何调整成为主链?

调整主要通过BlockChain.reorg和BlockChain.insert实现的

state的实现方式?

以太坊的state对象是以太坊账户数据的“状态机”，记录着每一个区块下所有账户的相关信息。而state对象的底层实现是trie.

trie同时拥有默克尔树和字典树的功能，但本质上，trie就是一棵树，且trie的key就是以太坊的账户地址，而value而是账户地址对应的信息，也就是说账户地址的信息存储在树的叶子结节中。因此如果一个账户信息发生了改变，那么所有这个叶子到根结点这个路径上的结点都会发生改变。

blockchain的主要功能是维护区块链的状态, 包括区块的验证,插入和状态查询.

一些关键字段：

* hc            \*HeaderChain        // 只包含了区块头的区块链
* genesisBlock  \*types.Block        // 创世区块
* currentBlock     \*types.Block // Current head of the block chain 当前的区块头
* currentFastBlock \*types.Block // 当前的快速同步的区块头.
* engine    consensus.Engine    // 共识引擎
* processor Processor // block processor interface  // 区块处理器接口
* validator Validator // block and state validator interface // 区块和状态验证器接口
* vmConfig  vm.Config //虚拟机的配置
* badBlocks \*lru.Cache // Bad block cache  错误区块的缓存.

一些关键方法：

* NewBlockChain 使用数据库里面的可用信息构造了一个初始化好的区块链. 同时初始化了以太坊默认的 验证器和处理器 \(Validator and Processor\)
  * loadLastState, 加载数据库里面的最新的我们知道的区块链状态. 
  * update 定时处理future blocks.
  * Reset 方法 重置区块链.
  * SetHead将本地链回卷到新的头部。 在给定新header之上的所有内容都将被删除，新的header将被设置。
* InsertChain,插入区块链, 插入区块链尝试把给定的区块插入到规范的链条,或者是创建一个分叉. 如果发生错误,那么会返回错误发生时候的index和具体的错误信息.
* insertChain方法会执行区块链插入,并收集事件信息.
* WriteBlockAndState,把区块写入区块链.
* reorgs方法是在新的链的总难度大于本地链的总难度的情况下，需要用新的区块链来替换本地的区块链为规范链。



