每当p2p模块发现一个新的节点并连接完成时，就会调用p2p.Protocol.Run函数。而此函数的注册是在NewProtocolManager中完成的

ProtocolManager.handle方法对每个新到来的连接进行处理。

“握手”完成后:

* 一是告诉peers对象（一个peerSet类型的对象，用来管理所有的peer）和downloader对象有新的节点连接进来了
* 二是与新的节点同步自己所有pending状态的交易（syncTransactions）

所谓的“白名单区块”是你认为的可信任的区块，这些区块的高度和哈希被写在配置文件里的Whitelist字段中。

ProtocolManager.handleMsg, 用来处理接收到的消息的。从对方连接中读取消息，根据消息码的不同进行处理

