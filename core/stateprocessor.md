StateProcessor 用来处理区块级别的交易的。

```go
// StateProcessor is a basic Processor, which takes care of transitioning
// state from one point to another.
//
// StateProcessor implements Processor.
type StateProcessor struct {
    config *params.ChainConfig // Chain configuration options
    bc     *BlockChain         // Canonical block chain
    engine consensus.Engine    // Consensus engine used for block rewards
}
```

Process\(\)方法对block中的每个交易tx调用ApplyTransaction\(\)来执行交易，入参state存储了各个账户的信息，如账户余额、合约代码\(仅对合约账户而言\)，我们姑且将其理解为一个内存中的数据库。其中每个账户以state object表示。

ApplyTransaction\(\)方法完成以下功能



* 调用AsMessage\(\)用tx为参数生成core.Message。也就是将tx中的一些字段存入Message，再从tx的数字签名中反解出tx的sender，重点关注其中的data字段：如果是普通的转账交易，该字段为空，如果是创建一个新的合约，该字段为新的合约的代码，如果是执行一个已经在区块链上存在的合约，该参数为合约代码的输入参数

* 调用NewEVMContext\(\)创建一个EVM运行上下文vm.Context。注意其中的Coinbase字段需要填入的矿工的地址，Transfer是具体的转账方法，其实就是操作sender和recipient的账户余额

* 调用NewEVM\(\)创建一个虚拟机运行环境EVM，它主要作用是汇集之前的信息以及创建一个代码解释器\(Interpreter\)，这个解释器之后会用来解释并执行合约代码

* 接下来就是调用ApplyMessage\(\)将以上的信息施加在以太坊当前状态上，使得状态机发生状态变换

ApplyMessage\(\)的顶层比较简单，它创建一个StateTransition结构并调用其TransitionDb\(\)方法，StateTransition表示一次以太访的状态转移。

