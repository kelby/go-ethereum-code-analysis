ETH 全节点服务端。

命令行客户端、轻客户端与它交互

* 过滤器 - implements a core.ChainIndexer, building up a rotated bloom bits index for the Ethereum header bloom filters, permitting blazing fast filtering.
* 后端API - ethapi.Backend for full nodes
* 以太坊对象 - implements the Ethereum full node service.
* 私有管理员API
* 私有调试API - collection of Ethereum full node APIs exposed over the private debugging endpoint.
* 私有矿工API
* 公开调试API
* 公开以太坊API - provides an API to access Ethereum full node-related information.
* 公开矿工API
* 配置信息，如 DefaultConfig
* 联动p2p模块，广播：BroadcastBlock、BroadcastTransactions，minedBroadcastLoop、txBroadcastLoop；接收：AcceptTxs，handleHeaders、handleBodies、handleBlockAnnounces、handleBlockBroadcast
* 远程节点管理
* 同步数据！

type EthAPIBackend

type Ethereum

type PrivateAdminAPI

type PrivateDebugAPI

type PrivateMinerAPI

type PublicDebugAPI

type PublicEthereumAPI

type PublicMinerAPI

网络模块的运行框架：![](/assets/eth-protocol-manager.png)

