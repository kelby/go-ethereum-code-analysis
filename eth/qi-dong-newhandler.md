## newHandler

```go
h := &handler{
        networkID:  config.Network,
        forkFilter: forkid.NewFilter(config.Chain),
        eventMux:   config.EventMux,
        database:   config.Database,
        txpool:     config.TxPool,
        chain:      config.Chain,
        peers:      newPeerSet(),
        merger:     config.Merger,
        whitelist:  config.Whitelist,
        quitSync:   make(chan struct{}),
    }
```

Sync FullSync

 config.Checkpoint

h.downloader = downloader.New

Construct the fetcher \(short sync\)

* validator
* heighter
* inserter

h.blockFetcher = fetcher.NewBlockFetcher

h.txFetcher = fetcher.NewTxFetcher

newChainSyncer

## Start

txpool.SubscribeNewTxsEvent

txBroadcastLoop

minedBroadcastLoop

chainSync.loop



