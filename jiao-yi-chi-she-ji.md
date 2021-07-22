![](/assets/tx-mempool.png)构建 NewTxPool

reset方法检索区块链的当前状态并且确保事务池的内容关于当前的区块链状态是有效的。

addTx

addTxsLocked

demoteUnexecutables 从pending删除无效的或者是已经处理过的交易，其他的不可执行的交易会被移动到future queue中。

enqueueTx 把一个新的交易插入到future queue。

promoteExecutables方法把 已经变得可以执行的交易从future queue 插入到pending queue。通过这个处理过程，所有的无效的交易\(nonce太低，余额不足\)会被删除。

promoteTx把某个交易加入到pending 队列.

removeTx，删除某个交易， 并把所有后续的交易移动到future queue

loop是txPool的一个goroutine.也是主要的事件循环.等待和响应外部区块链事件以及各种报告和交易驱逐事件。

add 方法, 验证交易并将其插入到future queue. 如果这个交易是替换了当前存在的某个交易,那么会返回之前的那个交易,这样外部就不用调用promote方法. 如果某个新增加的交易被标记为local, 那么它的发送账户会进入白名单,这个账户的关联的交易将不会因为价格的限制或者其他的一些限制被删除.

validateTx 使用一致性规则来检查一个交易是否有效,并采用本地节点的一些启发式的限制.



