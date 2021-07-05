Node 实例是节点程序运行的基础，以太坊主程序和很多子命令的运行都需要首先启动 Node才能进一步运行所有服务。这里以以太坊全节点的启动为例，来看一下 Node 的启动过程，流程如下。

1）  首先创建一个Node实例，名称为stack。创建实例的同时，创建一个临时账户管理器，用于导入已存在的账户，以及配置4种RPC服务地址，RPC服务根据启动参数分别控制打开还是关闭，以及配置开放哪些RPC服务。

2）  注册Ethereum服务到stack。注意，这里并没有进行实例化，注册的是Ethereum服务的构造器函数，在后面再通过执行该构造器函数来实例化Ethereum。

3）  启动Node，即stack.Start\(\)。在Node启动中完成下面的操作。

* 配置P2P节点：生成节点私钥，配置节点名字，读取静态节点和信任节点列表。
* 创建一个p2p.Server实例。
* 启动Server中的服务，即创建一个Ethereum实例。Ethereum实例的初始化流程如下。
  * 创建一个存储区块和链的数据库，底层数据库为 levelDB。
  * 读取并保存创世块的配置并保存Node的临时账户管理器到本实例。
  * 创建以太坊核心组件，具体如下。
    * TxPool：等待上链的交易缓存池。
    * Engine：共识引擎。
    * BlockChain：链和区块管理。
    * Miner：管理矿工挖矿。
    * AccountManager：账户管理。
    * ProtocolManager：peer消息收发。

4）  注册Ethereum中的Protocol到p2p.Server实例上。这里的Protocol是Protocol-Manager初始化时注册的eth/63和eth/62协议，用于对P2P协议层消息的收发进行处理。

5）  启动 p2p.Server，启动邻居节点发现，发现邻居并握手连接，刷新邻居列表。

6）  启动 Protocol，处理区块链业务消息，与邻居之间进行区块链相关数据的同步交互。

7）  启动 RPC 服务。主要工作为收集各个服务的 RPC 接口并注册 RPC 请求分发器，启动 http、websocket、ipc、in-process RPC 监听后端服务。

8）  监听 keystore 操作事件：创建钱包 WalletArrived，打开钱包 WalletOpened，删除钱包 WalletDropped。

9）  如果启动参数中使用了挖矿功能，则根据挖矿参数配置启动挖矿功能。



