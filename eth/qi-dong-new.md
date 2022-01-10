config.SyncMode 检查同步模式 =&gt; downloader

config.Miner.GasPrice 设置最小矿工费率

config.TrieCleanCache

config.SnapshotCache

config.TrieDirtyCache

ethashConfig

stack.OpenDatabaseWithFreezer

core.SetupGenesisBlockWithOverride =&gt; core

pruner.RecoverPruning recover state =&gt; pruner

consensus.NewMerger =&gt; consensus

```go
eth := &Ethereum{
        config:            config,
        merger:            merger,
        chainDb:           chainDb,
        eventMux:          stack.EventMux(),
        accountManager:    stack.AccountManager(),
        engine:            ethconfig.CreateConsensusEngine(stack, chainConfig, &ethashConfig, config.Miner.Notify, config.Miner.Noverify, chainDb),
        closeBloomHandler: make(chan struct{}),
        networkID:         config.NetworkId,
        gasPrice:          config.Miner.GasPrice,
        etherbase:         config.Miner.Etherbase,
        bloomRequests:     make(chan chan *bloombits.Retrieval),
        bloomIndexer:      core.NewBloomIndexer(chainDb, params.BloomBitsBlocks, params.BloomConfirms),
        p2pServer:         stack.Server(),
        shutdownTracker:   shutdowncheck.NewShutdownTracker(chainDb),
    }
```

ethconfig.CreateConsensusEngine =&gt; ethconfig

core.NewBloomIndexer =&gt; core

shutdowncheck.NewShutdownTracker =&gt; shutdowncheck

rawdb.ReadDatabaseVersion =&gt; rawdb

rawdb.WriteDatabaseVersion

core.NewBlockChain =&gt; core

eth.blockchain.SetHead

rawdb.WriteChainConfig

eth.bloomIndexer.Start

core.NewTxPool =&gt; core

newHandler

miner.New =&gt; miner

gasprice.NewOracle =&gt; gasprice

dnsdisc.NewClient =&gt; dnsdisc

dnsclient.NewIterator =&gt; dnsclient

ethapi.NewPublicNetAPI =&gt; ethapi

stack.RegisterAPIs

stack.RegisterProtocols

stack.RegisterLifecycle

eth.shutdownTracker.MarkStartup

停止

```go
// Stop implements node.Lifecycle, terminating all internal goroutines used by the
// Ethereum protocol.
func (s *Ethereum) Stop() error {
    // Stop all the peer-related stuff first.
    s.ethDialCandidates.Close()
    s.snapDialCandidates.Close()
    s.handler.Stop()

    // Then stop everything else.
    s.bloomIndexer.Close()
    close(s.closeBloomHandler)
    s.txPool.Stop()
    s.miner.Close()
    s.blockchain.Stop()
    s.engine.Close()

    // Clean shutdown marker as the last thing before closing db
    s.shutdownTracker.Stop()

    s.chainDb.Close()
    s.eventMux.Stop()

    return nil
}
```



