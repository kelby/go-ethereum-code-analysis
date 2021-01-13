Engine接口定义了共识引擎需要实现的所有函数，实际上按功能可以划分为2类：



区块验证类：以Verify开头，当收到新区块时，需要先验证区块的有效性

区块盖章类：包括Prepare/Finalize/Seal等，用于最终生成有效区块（比如添加工作量证明）

与区块验证相关联的还有2个外部接口：Processor用于执行交易，而Validator用于验证区块内容和状态。

另外，由于需要访问以前的区块链数据，抽象出了一个ChainReader接口，BlockChain和HeaderChain都实现了该接口以完成对数据的访问。



