以太坊数据存储，**关键是定义接口**，具体使用哪个数据库，自己封装实现。

以太坊的存储数据结构为MPT（Merkle Patricia Tries），一切数据都为键值对（KeyValue）。为了存储（序列化）这些健值对，以太坊采用LevelDB为原型来定制自己的数据库ethdb。

StateDB、Transactions、Receipts的存储，这3棵树的Merkle Root最终会存储到区块Header中的Root、TxHash、ReceiptHash字段。

StateDB中存储了很多stateObject，而每一个stateObject则代表了一个以太坊账户，包含了账户的地址、余额、nonce、合约代码hash等状态信息。所有账户的当前状态在以太坊中被称为“世界状态”，在每次挖出或者接收到新区块时需要更新世界状态。



* 第一级缓存以map的形式存储stateObject

* 第二级缓存以MPT的形式存储

* 第三级就是LevelDB上的持久化存储

当上一级缓存中没有所需的数据时，会从下一级缓存或者数据库中进行加载。

按接口类型来分，主要分为 Trie（一、二级之间）和 Database（二、三级之间）两种接口。

Trie接口主要用于操作内存中的MPT，而Database接口主要用于操作LevelDB，做持久化存储。

StateDB中同时包含了这两种接口。



