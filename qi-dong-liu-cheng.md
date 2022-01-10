Geth的启动入口位置在go-ethereum/cmd/main.go

init函数

geth函数，调用makeFullNode函数创建节点，调用startNode启动节点中的服务，最后调用node.Wait使节点进入等待状态

* makeFullNode函数：来创建一个节点，geth中node对象可以认为是以太坊网络中的一个节点，这个节点中会包含各种服务，比如网络服务、eth服务、DashBoard服务等等。然后向node中注册一个以太坊服务
* Node可以看做是以太坊的各个服务的容器， 一个服务能够注册到node当中，必须实现Service 接口。Node可以不用关心容器中的具体是哪一个服务，只要统一调用他们的接口就行。
* startNode函数：启动node中的服务，主要流程是node将之前注册的所有service交给p2p.Server, 然后启动p2p.Server对象，然后逐个启动每个Service。解锁钱包中的账号。注册钱包事件。启动辅助服务，比如RPC服务，如果配置支持启动挖矿，则执行启动挖矿。
* Node.Wait函数：主要是让geth的主go成进入阻塞状态，保持整个程序不退出，直到从channel中收到Stop消息， Stop消息一般是用户关闭以太坊客户端引起的。

以太坊的启动过程其实就是创建一个Node对象， 接着向Node对象中添加各种服务，然后调用这些服务的Start方法启动他们。Node看起来就像一个容器，将各种模块扔进其中，然后将他们联系起来。

Node中包括P2P服务、RPC服务、以太坊服务等。

一些细节：

makeConfigNode

eth.DefaultConfig是geth的默认配置

makeConfigNode方法中还有一个从配置文件加载配置的逻辑

stack, err := node.New\(&cfg.Node\) 初始一个node实例

fullNode, err := eth.New\(ctx, cfg\)，即创建一个ethereum实例

ethereum服务会在node.start\(\)时同时执行ethereum.start方法

s.protocolManager.Start\(maxPeers\)，启动协议管理实例

把需要处理的消息放到channel中。然后由p.broadcast\(\)方法处理

注册ethereum服务，除此之外还注册了whisper服务，统计服务，最后返回这个node实例

设置serverconfig

找出本节点支持的service

循环所有支持的服务进行启动service.Start\(running\)

* 启动 ethereum服务
* 启动rpc服务，包括ipc/http/websocket

go srv.run\(dialer\) 负责与初始的节点进行握手并启动节点的连接

go srv.runPeer\(p\)即与p节点建立连接

readLoop是读取连接发送来的数据

读取消息后，进行处理 \(p \*Peer\) handle

开始挖矿ethereum.StartMining\(threads\)

> 整个启动过程其实就是解析参数。然后创建和启动节点。 然后把服务注入到节点中。 所有跟以太坊相关的功能都是以服务的形式实现的。

启动流程图：

![](/assets/geth-run-process.png)

cmd/geth 模块：

main.go

config.go

cmd/utils 模块：

cmd.go

flag.go

node 模块：

node.go

service.go

eth 模块：

backend.go

handler.go

p2p 模块：

server.go

core 模块：

genesis.go

blockchain.go

tx\_pool.go

miner 模块：

miner.go



从 cmd 模块到其它 6 个模块，配置，并启动、运行。

