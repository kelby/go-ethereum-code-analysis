rpc包提供这样一种能力，可以通过网络或者其他I/O连接，可以访问对象被导出的方法。创建一个服务器之后，对象可以注册到服务器上，然后可以让外界访问。通过这种方式导出的方法可以被远程调用。 同时还支持发布/订阅模式。

* 客户端 Client
* 通知 Notifier
* 服务端 Server
* 订阅 Subscription

既可以做为客户端，向其它远程节点发起请求；

也可以做为服务端，对其它远程节点发起的请求进行响应。

RPC Client是真正发起RPC调用的模块，对端的RPC Server会处理请求并返回执行结果。

RPC Server是真正处理RPC请求的模块，内部通过ServerCodec对象完成具体的处理工作。

a structure picture of ethereum RPC:

![](/assets/structure-of-rpc.png)

调用流程

先是 cmd/geth/main.go 里 geth

调用了startNode, 传入context and 节点node.

然后在startNode里调用了utils.StartNode\(stack\), stack是node

start函数: startRPC

startInProc, initializes an in-process RPC endpoint.

handler := rpc.NewServer\(\)  // 初始化RPC的server了

这个startPRC    开启了不同的远端service的API

* startInProc
* startIPC
* startHTTP
* startWS

RPC使用举例

远端调用, 走json rpc.

startHttp, NewHTTPServer

ServeHTTP

serveRequest, Server的主要处理流程。从codec读取请求，找到对应的方法并调用，然后把回应写入codec。

> 属于API客户端连接库范畴。

以太坊系统，想要提供 RPC 服务，不是直接使用像 net/rpc 这种官方库，而是自己搞了个和 rpc 有关的库，就是目前看到的这个。

它是底层库，由它处理 RPC 相关操作，或提供服务。

rpc.Server

> node

rpc.Client

> accounts/external
>
> console
>
> ethclient
>
> ethclient/gethclient
>
> p2p/simulations/adapters
>
> signer/core

rpc.Subscription

rpc.BlockNumber

rpc.BlockNumberOrHash

rpc.ID

