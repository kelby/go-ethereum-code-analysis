Gas预估 - EstimateGas

相关源码位置：internal/ethapi/api.go

EstimateGas采用二分查找法获取要评估交易的gas值。二分查找的下限是param.TxGas, 如果args参数指定Gas大于param.Gas，那么二分查找的上限就是args.Gas，否则以当前pending块的block gas limit\(后面简称BGL\)作为二分查找的上限。doCall函数模拟智能合约的执行，经过多次尝试找到智能合约能够成功运行的最佳gas值。

由于二分查找的上限和BGL有关，而BGL和不是固定不变的，因此每次gas评估的结果不一定都是相同的，可能每个区块周期就会变动一次。

测试上限，根据传入参数或区块计算，根据账户可用余额计算，根据封顶上限计算。

最低到最高二分查找（测试运行），由低到高，返回等一次成功时的最高值。

