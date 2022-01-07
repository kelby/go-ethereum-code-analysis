ETH 全节点服务端。

**按照区块链要素的划分，属于区块链核心，并非网络部分，但它与 p2p 直接相关，是 p2p 网络到达区块链系统后的第一道关口。**

3 个角色：

* 做为 P2P 网络中的一员，节点与节点之间下载、同步
* 做为 HTTP, RPC 网络中的一员，做为服务端，接收客户端请求，响应数据给客户端
* 做为服务节点，维持自身运转，协调各个模块

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

网络模块的运行框架：![](/assets/eth-protocol-manager.png)这个框架主要由ProtocolManager对象实现。可以看到这个框架实现了消息处理、广播区块和交易、同步区块和交易等功能。

