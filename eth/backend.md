// Ethereum implements the Ethereum full node service.

```go
type Ethereum struct {
    config *ethconfig.Config

    // Handlers
    txPool             *core.TxPool
    blockchain         *core.BlockChain
    handler            *handler
    ethDialCandidates  enode.Iterator
    snapDialCandidates enode.Iterator

    // DB interfaces
    chainDb ethdb.Database // Block chain database

    eventMux       *event.TypeMux
    engine         consensus.Engine
    accountManager *accounts.Manager

    bloomRequests     chan chan *bloombits.Retrieval // Channel receiving bloom data retrieval requests
    bloomIndexer      *core.ChainIndexer             // Bloom indexer operating during block imports
    closeBloomHandler chan struct{}

    APIBackend *EthAPIBackend

    miner     *miner.Miner
    gasPrice  *big.Int
    etherbase common.Address

    networkID     uint64
    netRPCService *ethapi.PublicNetAPI

    p2pServer *p2p.Server

    lock sync.RWMutex // Protects the variadic fields (e.g. gas price and etherbase)
}
```

按主要功能拆分成 3 类：

* API 后端：APIBackend、netRPCService
* 和链、区块链核心：txPool、blockchain、handler，engine、accountManager，miner
* 和P2P网络：p2pServer

这里是全节点，客户端节点参考 `les/client.go`

Package les implements the Light Ethereum Subprotocol.

```go
type LightEthereum struct {
    lesCommons

    peers              *serverPeerSet
    reqDist            *requestDistributor
    retriever          *retrieveManager
    odr                *LesOdr
    relay              *lesTxRelay
    handler            *clientHandler
    txPool             *light.TxPool
    blockchain         *light.LightChain
    serverPool         *vfc.ServerPool
    serverPoolIterator enode.Iterator
    pruner             *pruner

    bloomRequests chan chan *bloombits.Retrieval // Channel receiving bloom data retrieval requests
    bloomIndexer  *core.ChainIndexer             // Bloom indexer operating during block imports

    ApiBackend     *LesApiBackend
    eventMux       *event.TypeMux
    engine         consensus.Engine
    accountManager *accounts.Manager
    netRPCService  *ethapi.PublicNetAPI

    p2pServer  *p2p.Server
    p2pConfig  *p2p.Config
    udpEnabled bool
}
```



