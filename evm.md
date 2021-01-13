evm 模块的核心对象是 EVM，它代表了一个以太坊虚拟机，用于创建或调用某个合约。每次处理一个交易对象时，都会创建一个 EVM 对象。

evm 只是实现了一个虚拟机，它在以太坊区块链环境中，逐条地解释执行智能合约的指令

EVM 对象内部主要依赖三个对象：解释器 Interpreter、虚拟机相关配置对象 vm.Config、以太坊状态数据库 StateDB。

* StateDB 主要的功能就是用来提供数据的永久存储和查询。
* Interpreter 是一个接口，在代码中由 EVMInterpreter 实现具体功能。这是一个解释器对象，循环解释执行给定的合约指令，直接遇到退出指令。每执行一次指令前，都会做一些检查操作，确保 gas、栈空间等充足。但各指令真正的解释执行代码却不在这个对象中，而是记录在 vm.Config 的 JumpTable 字段中。
* vm.Config 为虚拟机和解释器提供了配置信息，其中最重要的就是 JumpTable。JumpTable 是 vm.Config 的一个字段，它是一个由 256 个 operation 对象组成的数组。解释器每拿到一个准备执行的新指令时，就会从 JumpTable 中获取指令相关的信息，即 operation 对象。这个对象中包含了解释执行此条指令的函数、计算指令的 gas 消耗的函数等。

**以太坊虚拟机：EVM**

ApplyTransaction -&gt; Transaction.AsMessage

-&gt; Context

-&gt; vm.NewEVM

-&gt; ApplyMessage

-&gt; StateTransition.TransitionDb

-&gt; 分支 EVM.Create

-&gt; 分支 EVM.Call

-&gt; StateTransaction.refundGas

如果交易的接收者为空，则代表此条交易的目的是要创建一条合约，因此调用 EVM.Create 执行相关的功能

**调用合约**

EVM 对象有三个方法实现合约的调用，它们分别是：



* EVM.Call

* EVM.CallCode

* EVM.DelegateCall

* EVM.StaticCall

**解释器对象：EVMInterpreter**

跳转表：vm.Config.JumpTable





