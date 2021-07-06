

engine是以太坊封定义的一个接口，它的功能可以分为3类：



* 验证区块类，主要用在将区块加入到区块链前，对区块进行共识验证。

* 产生区块类，主要用在挖矿时。

* 辅助类。

![](/assets/consensus-engine.png)

产生区块类

* Prepare：初始化区块头信息，不同的共识算法初始化不同。使用场景是，worker创建work的时候调用。

* Finalize：根据数据生成“基本定型”的区块，但区块头中还缺少部分数据。使用场景是，1）模拟区块链的时候，被GenerateChain调用，用来生成区块链。2）交易状态管理时，被StateProcessor.Process调用用来执行交易。3）worker创建work的时候调用。

* Seal：根据传入的块，进行的是挖矿工作，使用挖矿的结果，修改区块头，然后生成新的区块。使用场景是，被agent.mine调用。



验证区块类

* VerifyHeader：验证区块头。使用在fetcher中，当fetcher要插入区块的时候，需要先对区块头进行校验。

* VerifyHeaders：验证一批区块头。有2种使用场景，1）区块链中，insertChain当把一批区块插入到区块链这个链条的时候，需要进行检查；2）LightChain中，把一批区块头插入到本地链。

* VerifyUncles：验证区块中的叔块。insertChain当区块插入区块链的时候，需要对叔块进行验证，调用在VerifyHeaders之后。

* VerifySeal：针对Seal函数做的功能进行验证。验证Seal\(\)所修改的区块头中的数据。对外的使用场景是，把Work发送给远端Agent的时候调用。对内的使用场景是，验证区块头的时候会被调用。



辅助类

* APIs：生成以太坊共识相关的API。在以太坊启动RPC服务时，生成API。

* Author：读取区块头中的coinbase。被ethstats使用，ethstats是以太坊状态管理服务，当报告数据的时候，需要获取区块的Author信息。





