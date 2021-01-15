EVM架构

![](/assets/evm-arch.png)EVM 对象内部主要依赖三个对象：解释器 Interpreter、虚拟机相关配置对象 vm.Config、以太坊状态数据库 StateDB。

* StateDB 主要的功能就是用来提供数据的永久存储和查询。
* Interpreter 是一个接口，在代码中由 EVMInterpreter 实现具体功能。这是一个解释器对象，循环解释执行给定的合约指令，直接遇到退出指令。每执行一次指令前，都会做一些检查操作，确保 gas、栈空间等充足。
* vm.Config 为虚拟机和解释器提供了配置信息，其中最重要的就是 JumpTable。





