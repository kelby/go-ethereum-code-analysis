node在go ethereum中代表了一个节点。 可能是全节点，可能是轻量级节点。 node可以理解为一个进程，以太坊由运行在世界各地的很多中类型的node组成。

Package node sets up multi-protocol Ethereum nodes.

一个典型的node就是一个p2p的节点。 运行了p2p网络协议，同时根据节点类型不同，运行了不同的业务层协议\(以区别网络层协议。 参考p2p peer中的Protocol接口\)。

* 本地节点 Node

> node 和 peer 都翻译为“节点”的话，容易混淆。约定，Node 偏向于表示本地节点；Peer 偏向于远程节点。

Node启动时会调用各Service的构造函数创建Service实例。Service是一个接口，要对外暴露RPC API的模块都需要实现该接口，比如Ethereum, Whisper, Swarm等等。

