type Config 配置

type Msg 传递的消息，已被封装

type NodeInfo 节点信息

type Peer 已连接的远程节点

type PeerInfo 远程节点信息

~~type Protocol 协议~~

type Server 管理所有远程节点连接

代码文件、目录可分为3类：

* 网络基础库。如 nat、netutil
* 业务基础库。如 dnsdisc、enode、enr、msgrate、nodestate、rlpx、simulations、tracker
* 业务核心库。如 peer.go、server.go、discover

做为个体，阅读 x\_udp.go，主要做 3 件事：发送消息、接收处理消息和握手。其中握手又可细分为 Ping/Pong，查找邻居节点。

做为集体，阅读 table.go，主要做 3 件事：定时尝试刷新，增量（替换）存量（移除），入库。

