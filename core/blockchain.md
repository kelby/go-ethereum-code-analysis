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

