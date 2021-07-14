Engine接口定义了共识引擎需要实现的所有函数，实际上按功能可以划分为2类：

区块验证类：以Verify开头，当收到新区块时，需要先验证区块的有效性

区块盖章类：包括Prepare/Finalize/Seal等，用于最终生成有效区块（比如添加工作量证明）

与区块验证相关联的还有2个外部接口：Processor用于执行交易，而Validator用于验证区块内容和状态。

另外，由于需要访问以前的区块链数据，抽象出了一个ChainReader接口，BlockChain和HeaderChain都实现了该接口以完成对数据的访问。

---

Ethash算法主要分为以下几个步骤：

根据区块信息生成一个seed

根据seed计算出一个16MB的伪随机cache，由轻客户端存储

根据cache计算出一个1GB的dataset，其中的每一个数据都是通过cache中的一小部分数据计算出来的。该dataset由完整客户端或者矿工存储，大小随时间线性增长

矿工会从dataset中随机取出数据计算hash

验证者会根据cache重新生成dataset中所需要的那部分数据，因此只需要存储cache就可以了

主要方法及 worker 调用入口：

* Prepare - commitNewWork

* Seal - taskLoop
* FinalizeAndAssemble - commit
* Finalize - 无，上链时用到
* SealHash - taskLoop, resultLoop, commit



