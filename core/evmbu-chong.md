EVM架构

![](/assets/evm-arch.png)EVM 对象内部主要依赖三个对象：解释器 Interpreter、虚拟机相关配置对象 vm.Config、以太坊状态数据库 StateDB。

* StateDB 主要的功能就是用来提供数据的永久存储和查询。
* Interpreter 是一个接口，在代码中由 EVMInterpreter 实现具体功能。这是一个解释器对象，循环解释执行给定的合约指令，直接遇到退出指令。每执行一次指令前，都会做一些检查操作，确保 gas、栈空间等充足。
* vm.Config 为虚拟机和解释器提供了配置信息，其中最重要的就是 JumpTable。

ApplyTransaction 函数的功能就是将交易的信息记录到以太坊状态数据库（state.StateDB）中，这其中包括转账信息和执行合约信息（如果交易中有合约的话）。

调用 Transaction.AsMessage 将一个 Transaction 对象转换成 Message 对象

ApplyTransaction 创建了 Context 对象，这个对象中主要包含了一些访问当前区块链数据的方法。

调用 vm.NewEVM 函数创建了以太坊虚拟机，通过 ApplyMessage 执行相关功能。

最终调用了 StateTransition.TransitionDb 方法。

**EVM 的创建：**

* ctx: 提供访问当前区块链数据和挖矿环境的函数和数据
* statedb: 以太坊状态数据库对象
* chainConfig: 当前节点的区块链配置信息
* vmConfig: 虚拟机配置信息

ApplyTransaction

```go
ApplyTransaction
    AsMessage
    NewEVMContext
    NewEVM
    ApplyMessage
    Finalise or IntermediateRoot
    NewReceipt
    GetLogs
    CreateBloom
```



