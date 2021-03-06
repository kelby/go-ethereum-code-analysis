功能节点配置

```go
// Defaults contains default settings for use on the Ethereum main net.
var Defaults = Config{
    SyncMode: downloader.SnapSync,
    Ethash: ethash.Config{
        CacheDir:         "ethash",
        CachesInMem:      2,
        CachesOnDisk:     3,
        CachesLockMmap:   false,
        DatasetsInMem:    1,
        DatasetsOnDisk:   2,
        DatasetsLockMmap: false,
    },
    NetworkId:               params.PhoenixChainConfig.ChainID.Uint64(),
    TxLookupLimit:           2350000,
    LightPeers:              100,
    UltraLightFraction:      75,
    DatabaseCache:           512,
    TrieCleanCache:          154,
    TrieCleanCacheJournal:   "triecache",
    TrieCleanCacheRejournal: 60 * time.Minute,
    TrieDirtyCache:          256,
    TrieTimeout:             60 * time.Minute,
    SnapshotCache:           102,
    Miner: miner.Config{
        GasCeil:  8000000,
        GasPrice: big.NewInt(500 * params.GWei),
        Recommit: 3 * time.Second,
    },
    TxPool:      core.DefaultTxPoolConfig,
    RPCGasCap:   50000000,
    GPO:         FullNodeGPO,
    RPCTxFeeCap: 10, // 10 ether
}
```

中文都翻译为“节点”，这里说明是功能节点，区别于P2P节点。

预言机配置

```go

// FullNodeGPO contains default gasprice oracle settings for full node.
var FullNodeGPO = gasprice.Config{
	Blocks:           20,
	Percentile:       60,
	MaxHeaderHistory: 1024,
	MaxBlockHistory:  1024,
	MaxPrice:         gasprice.DefaultMaxPrice,
	IgnorePrice:      gasprice.DefaultIgnorePrice,
}

// LightClientGPO contains default gasprice oracle settings for light client.
var LightClientGPO = gasprice.Config{
	Blocks:           2,
	Percentile:       60,
	MaxHeaderHistory: 300,
	MaxBlockHistory:  5,
	MaxPrice:         gasprice.DefaultMaxPrice,
	IgnorePrice:      gasprice.DefaultIgnorePrice,
}

```

本质是预言机配置 `gasprice.Config`





