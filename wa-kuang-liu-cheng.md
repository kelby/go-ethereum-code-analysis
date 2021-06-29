以太坊挖矿的主要流程是由miner包负责

外部是通过miner对象进行了操作，miner里面则是实用worker对象来实现挖矿的整体功能。miner决定着是否停止挖矿或者是否可以开始挖矿，同时还可以设置矿工的地址来获取奖励。

四个循环：

* newWorkLoop
* mainLoop
* TaskLoop
* resultLoop

startCh：接收startCh信号，开始挖矿

chainHeadCh：表示接收到新区块，需要终止当前的挖矿工作，开始新的挖矿。

timer.C：默认每三秒检查一次是否有新交易需要处理。如果有则需要重新开始挖矿。以便将加高的交易优先打包到区块中。

newWorkCh:接收生成新的挖矿任务信号

chainSideCh:接收区块链中加入了一个新区块作为当前链头的旁支的信号

txsCh:接收交易池的Pending中新加入了交易事件的信号

TaskLoop则是提交新的挖矿任务，而resultLoop则是成功出块之后做的一些处理。

