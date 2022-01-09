implements the general Ethereum API functions.

* 私有账号API PrivateAccountAPI
* 私有调试API PrivateDebugAPI
* 公开账号API PublicAccountAPI
* 公开区块链API PublicBlockChainAPI
* 公开调试API PublicDebugAPI
* 公开以太坊API PublicEthereumAPI
* 公开网络API PublicNetAPI
* 公开交易池API1 PublicTransactionPoolAPI
* 公开交易池API2 PublicTxPoolAPI

被引用：

> eth
>
> eth/tracers
>
> graphql
>
> les

用户通过JSON RPC发起eth\_sendTransaction请求，最终会调用PublicTransactionPoolAPI

发起交易，数据结构：

```go
// SendTxArgs represents the arguments to sumbit a new transaction into the transaction pool.
type SendTxArgs struct {
    From     common.Address  `json:"from"`
    To       *common.Address `json:"to"`
    Gas      *hexutil.Uint64 `json:"gas"`
    GasPrice *hexutil.Big    `json:"gasPrice"`
    Value    *hexutil.Big    `json:"value"`
    Nonce    *hexutil.Uint64 `json:"nonce"`
    // We accept "data" and "input" for backwards-compatibility reasons. "input" is the
    // newer name and should be preferred by clients.
    Data  *hexutil.Bytes `json:"data"`
    Input *hexutil.Bytes `json:"input"`
}
```

ethapi/api包分析，一共有三种api的命令：

* admin相关的命令，这个是通过安全的RPC通道实现的。其结构体为PrivateAdminAPI
* personal相关的命令，主要是负责账户管理相关命令，可以lock和unlock账户。其结构体为PrivateAccountAPI
* eth相关的命令，主要是可以操作区块上的相关命令。其结构体为PublicBlockChainAPI

对于以太坊客户端/API来说，整个以太坊就是一个大后端，它们比较关心自己与之交互的部分。

```go
// Backend interface provides the common API services (that are provided by
// both full and light clients) with access to necessary functions.
type Backend interface {
    // General Ethereum API
    Downloader() *downloader.Downloader
    SuggestGasTipCap(ctx context.Context) (*big.Int, error)
    ChainDb() ethdb.Database
    AccountManager() *accounts.Manager
    ExtRPCEnabled() bool
    RPCGasCap() uint64        // global gas cap for eth_call over rpc: DoS protection
    RPCTxFeeCap() float64     // global tx fee cap for all transaction related APIs
    UnprotectedAllowed() bool // allows only for EIP155 transactions.

    // Blockchain API
    SetHead(number uint64)
    HeaderByNumber(ctx context.Context, number rpc.BlockNumber) (*types.Header, error)
    HeaderByHash(ctx context.Context, hash common.Hash) (*types.Header, error)
    HeaderByNumberOrHash(ctx context.Context, blockNrOrHash rpc.BlockNumberOrHash) (*types.Header, error)
    CurrentHeader() *types.Header
    CurrentBlock() *types.Block
    BlockByNumber(ctx context.Context, number rpc.BlockNumber) (*types.Block, error)
    BlockByHash(ctx context.Context, hash common.Hash) (*types.Block, error)
    BlockByNumberOrHash(ctx context.Context, blockNrOrHash rpc.BlockNumberOrHash) (*types.Block, error)
    StateAndHeaderByNumber(ctx context.Context, number rpc.BlockNumber) (*state.StateDB, *types.Header, error)
    StateAndHeaderByNumberOrHash(ctx context.Context, blockNrOrHash rpc.BlockNumberOrHash) (*state.StateDB, *types.Header, error)
    GetReceipts(ctx context.Context, hash common.Hash) (types.Receipts, error)
    GetTd(ctx context.Context, hash common.Hash) *big.Int
    GetEVM(ctx context.Context, msg core.Message, state *state.StateDB, header *types.Header, vmConfig *vm.Config) (*vm.EVM, func() error, error)
    SubscribeChainEvent(ch chan<- core.ChainEvent) event.Subscription
    SubscribeChainHeadEvent(ch chan<- core.ChainHeadEvent) event.Subscription
    SubscribeChainSideEvent(ch chan<- core.ChainSideEvent) event.Subscription

    // Transaction pool API
    SendTx(ctx context.Context, signedTx *types.Transaction) error
    GetTransaction(ctx context.Context, txHash common.Hash) (*types.Transaction, common.Hash, uint64, uint64, error)
    GetPoolTransactions() (types.Transactions, error)
    GetPoolTransaction(txHash common.Hash) *types.Transaction
    GetPoolNonce(ctx context.Context, addr common.Address) (uint64, error)
    Stats() (pending int, queued int)
    TxPoolContent() (map[common.Address]types.Transactions, map[common.Address]types.Transactions)
    SubscribeNewTxsEvent(chan<- core.NewTxsEvent) event.Subscription

    // Filter API
    BloomStatus() (uint64, uint64)
    GetLogs(ctx context.Context, blockHash common.Hash) ([][]*types.Log, error)
    ServiceFilter(ctx context.Context, session *bloombits.MatcherSession)
    SubscribeLogsEvent(ch chan<- []*types.Log) event.Subscription
    SubscribePendingLogsEvent(ch chan<- []*types.Log) event.Subscription
    SubscribeRemovedLogsEvent(ch chan<- core.RemovedLogsEvent) event.Subscription

    ChainConfig() *params.ChainConfig
    Engine() consensus.Engine
}
```

当前仅有全节点、轻节点模式。

