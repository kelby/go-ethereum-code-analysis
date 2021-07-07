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



