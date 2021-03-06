输入一笔交易，内部会转换成一个Message对象，传入EVM执行。

如果是一笔普通转账交易，那么直接修改StateDB中对应的账户余额即可。

如果是智能合约的创建或者调用，则通过EVM中的解释器加载和执行字节码，执行过程中可能会查询或者修改StateDB。

* 固定油费（Intrinsic Gas）- 如果你的交易不带额外数据（Payload），比如普通转账，那么需要收取21000的油费。

  如果你的交易携带额外数据，那么这部分数据也是需要收费的，具体来说是按字节收费。

* 生成Contract对象 - 交易会被转换成一个Message对象传入EVM，而EVM则会根据Message生成一个Contract对象以便后续执行。执行合约能够消耗的油费有一个上限，就是节点配置的每个区块能够容纳的GasLimit。

* 送入解释器执行 - EVM是基于栈的虚拟机。分别对Stack、Memory或者StateDB进行读写操作。

* 调用合约函数

* 合约调用合约
* 创建合约
* 油费计算

现实中合约是一张纸，是静态的，但以太坊中合约是动态的、可编程的。

手动布署合约：

* 编写合约 - solidity
* 编译合约 - bin/asm
* 提交合约 - rpc eth\_sendTransaction, eth\_getTransactionReceipt
* 调用合约 - eth\_call/eth\_sendTransaction



