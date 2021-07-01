固定油费（Intrinsic Gas）

创建合约

```
Keccak(RLP(call_addr, nonce))[:12]

stateObject
```

![](/assets/address-and-state-code.png)

交易开始

![](/assets/tx-to-message.png)

生成 Contract 对象

![](/assets/message-to-contract.png)

送入解释器执行

![](/assets/contract-and-input-to-evm.png)

解释器中需要操作四大组件：

* PC：类似于 CPU 中的 PC 寄存器，指向当前执行的指令
* Stack：执行堆栈，位宽为 256 bits，最大深度为 1024
* Memory：内存空间
* Gas：油费池，耗光邮费则交易执行失败

解释执行的流程

![](/assets/evm-opration.png)

调用合约函数

![](/assets/input-to-call-function.png)

合约调用合约

合约调用合约有下面 4 种方式：

* CALL

* CALLCODE

* DELEGATECALL

* STATICCALL

CALL![](/assets/contract-call.png)CALL vs. CALLCODE

![](/assets/call-vs-callcode.png)



CALLCODE vs. DELEGATECALL

![](/assets/callcode-vs-delegatecall.png)







