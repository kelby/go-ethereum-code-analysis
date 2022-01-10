config.SyncMode 检查同步模式

config.Miner.GasPrice 设置最小矿工费率

config.TrieCleanCache

config.SnapshotCache

config.TrieDirtyCache

ethashConfig

stack.OpenDatabaseWithFreezer

core.SetupGenesisBlockWithOverride

recover state

consensus.NewMerger

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

rawdb.ReadDatabaseVersion

rawdb.WriteDatabaseVersion

core.NewBlockChain

eth.blockchain.SetHead

rawdb.WriteChainConfig

eth.bloomIndexer.Start

core.NewTxPool

newHandler

miner.New

gasprice.NewOracle

dnsdisc.NewClient

dnsclient.NewIterator

ethapi.NewPublicNetAPI

stack.RegisterAPIs

stack.RegisterProtocols

stack.RegisterLifecycle

eth.shutdownTracker.MarkStartup





