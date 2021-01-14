geth is the official command-line client for Ethereum.

以太坊入口代码位于cmd/geth/main.go

初始化的工作是在init\(\)函数里完成。

启动过程如下：

以太坊启动主要做了3件事：创建结点 makeFullNode、启动结点 startNode、结点进入等待状态 Wait

* 创建结点过程主要做了2件事：

  * 根据配置创建Node实例

  * 注册eth Service

* 启动结点过程主要做了5件事：

  * 创建P2P server

  * 创建Service

  * 启动P2P server

  * 启动Service

  * 启动RPC server

* 最后结点进入等待状态，等待退出



