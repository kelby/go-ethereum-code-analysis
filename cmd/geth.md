geth is the official command-line client for Ethereum.

以太坊入口代码位于cmd/geth/main.go

初始化的工作是在init\(\)函数里完成。

**启动过程如下：**

以太坊启动主要做了3件事：创建结点 makeFullNode、启动结点 startNode、结点进入等待状态 Wait

* 创建结点过程主要做了2件事：

  * 根据配置创建Node实例 - makeConfigNode，DefaultConfig，ServiceContext

  * 注册eth Service - utils.RegisterEthService

* 启动结点过程主要做了5件事：

  * 创建P2P server - Start

  * 创建Service - serviceFuncs， ServiceContext

  * 启动P2P server - Start

  * 启动Service - Start

  * 启动RPC server - startRPC

* 最后结点进入等待状态，等待退出

使用此命令可以使节点作为full node（默认），或者archive node（保留所有历史状态）或light node（检索数据实时）运行。

