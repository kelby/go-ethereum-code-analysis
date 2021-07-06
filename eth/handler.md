每当p2p模块发现一个新的节点并连接完成时，就会调用p2p.Protocol.Run函数。而此函数的注册是在NewProtocolManager中完成的

ProtocolManager.handle方法对每个新到来的连接进行处理。

“握手”完成后:

* 一是告诉peers对象（一个peerSet类型的对象，用来管理所有的peer）和downloader对象有新的节点连接进来了
* 二是与新的节点同步自己所有pending状态的交易（syncTransactions）





