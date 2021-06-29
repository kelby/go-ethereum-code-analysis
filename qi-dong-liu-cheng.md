Geth的启动入口位置在go-ethereum/cmd/main.go

init函数

geth函数，调用makeFullNode函数创建节点，调用startNode启动节点中的服务，最后调用node.Wait使节点进入等待状态

* makeFullNode函数：来创建一个节点，geth中node对象可以认为是以太坊网络中的一个节点，这个节点中会包含各种服务，比如网络服务、eth服务、DashBoard服务等等。然后向node中注册一个以太坊服务
* Node可以看做是以太坊的各个服务的容器， 一个服务能够注册到node当中，必须实现Service 接口。Node可以不用关心容器中的具体是哪一个服务，只要统一调用他们的接口就行。
* startNode函数：启动node中的服务，主要流程是node将之前注册的所有service交给p2p.Server, 然后启动p2p.Server对象，然后逐个启动每个Service。解锁钱包中的账号。注册钱包事件。启动辅助服务，比如RPC服务，如果配置支持启动挖矿，则执行启动挖矿。
* Node.Wait函数：主要是让geth的主go成进入阻塞状态，保持整个程序不退出，直到从channel中收到Stop消息， Stop消息一般是用户关闭以太坊客户端引起的。

以太坊的启动过程其实就是创建一个Node对象， 接着向Node对象中添加各种服务，然后调用这些服务的Start方法启动他们。Node看起来就像一个容器，将各种模块扔进其中，然后将他们联系起来。

Node中包括P2P服务、RPC服务、以太坊服务等。

