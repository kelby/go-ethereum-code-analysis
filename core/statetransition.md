StateTransition是用来处理一个一个的交易的。

状态转换。

```go
/*
The State Transitioning Model

A state transition is a change made when a transaction is applied to the current world state
The state transitioning model does all the necessary work to work out a valid new state root.

1) Nonce handling
2) Pre pay gas
3) Create a new state object if the recipient is \0*32
4) Value transfer
== If contract creation ==
  4a) Attempt to run transaction data
  4b) If valid, use result as code for the new state object
== end ==
5) Run Script section
6) Derive new state root
*/
type StateTransition struct {
    gp         *GasPool
    msg        Message
    gas        uint64
    gasPrice   *big.Int
    initialGas uint64
    value      *big.Int
    data       []byte
    state      vm.StateDB
    evm        *vm.EVM
}
```

**ApplyMessage** 通过应用给定的Message 和状态来生成新的状态

定义了接口 Message，说明：Message represents a message sent to a contract. 交易以“消息”的形式在虚拟机里执行。

IntrinsicGas: The intrinsic gas for a transaction is the amount of gas that the transaction uses before any code runs. It is a constant “transaction fee” \(currently 21000 gas\) plus a fee for every byte of data supplied with the transaction \(4 gas for a zero byte, 68 gas for non-zeros\). These constants are all currently defined for geth in params/protocol\_params.go. Presumably they are also coded into the source for the other node/client implementations as well.





