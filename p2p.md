p2p通信的协议管理模块ProtocolManager

p2p模块对外暴露了Server关键结构,帮助上层管理复杂的p2p网路,使其集中于Protocol的实现,只关注于数据的传输.

* 远程节点 Peer
* 服务 Server

服务管理着远程节点。

> 网络这块，主要由服务、本地节点、远程节点构成。

无法连接的时候，常见的一些原因如下：

* 本地时钟错误。
* 防火墙配置错误导致阻止了UDP连接。

检查连接

```go
> net.listening
true
> net.peerCount
4
```

若想获取更多关于已连接节点的信息

```go
> admin.peers
```

静态节点:

static-nodes.json

```go
admin.addPeer()
```

以太坊的节点发现基于类似的kademlia算法，源码中有两个版本，v4和v5。v4适用于全节点，通过discover.ListenUDP使用，v5适用于轻节点通过discv5.ListenUDP使用

节点发现功能主要涉及 Server Table udp 这几个数据结构，它们有独自的事件响应循环，节点发现功能便是它们互相协作完成的。其中，每个以太坊客户端启动后都会在本地运行一个Server，并将网络拓扑中相邻的节点视为Node，而Table是Node的容器，udp则是负责维持底层的连接。这些结构的关系如下图：

![](/assets/server-table-udp.png)p2p服务开启节点发现，在P2p的server.go 的start方法中.

setupDiscovery

ListenUDP

节点第一次启动的时候，节点会与硬编码在以太坊源码中的bootnode进行连接，所有的节点加入几乎都先连接了它。连接上bootnode后，获取bootnode部分的邻居节点，然后进行节点发现，获取更多的活跃的邻居节点







