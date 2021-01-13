Miner - 矿工。

Clique - 共识方式一

Ethash - 共识方式二

unconfirmed - 用来跟踪用户本地的挖矿信息的

worker 内部包含了很多agent，同时负责构建区块和对象。



关键作用一：

执行交易

如果结点不挖矿的话，这里会立即调用commitTransactions\(\)提交给EVM执行，获得本地回执；

如果结点挖矿的话，miner会调用commitNewWork\(\)，内部也会调用commitTransactions\(\)执行交易。



