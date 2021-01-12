p2p通信的协议管理模块ProtocolManager

p2p模块对外暴露了Server关键结构,帮助上层管理复杂的p2p网路,使其集中于Protocol的实现,只关注于数据的传输.

* 远程节点 Peer
* 服务 Server

服务管理着远程节点。

> 网络这块，主要由服务、本地节点、远程节点构成。



