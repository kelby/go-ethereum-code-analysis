TxPool contains all currently known transactions.

* 区块链 BlockChain
* 区块生成 BlockGen
* 区块校验 BlockValidator
* 区块索引 ChainIndexer
* 执行结果 ExecutionResult
* 燃气池 GasPool
* 创世纪 Genesis
* 区块头-链 HeaderChain
* 交易池 TxPool

对链、区块、交易、世界状态（UTXO变体）的一些操作：

* BlockValidator 校验
* ChainIndexer 索引
* BlockGen 生成
* txSenderCacher
* txJournal
* txPricedList
* txNoncer
* txLookup
* statePrefetcher
* StateProcessor
* StateTransition

账户数据结构：

![](/assets/struct-account.png)

交易数据结构：

![](/assets/struct-tx.png)以太坊区块-BLOCK

![](/assets/struct-block.png)

交易回执-RECEIPT

![](/assets/struct-receipt.png)

> 链、区块、交易、收据、日志，虚拟机、世界状态、账户状态，交易池，创世纪等核心内容。



