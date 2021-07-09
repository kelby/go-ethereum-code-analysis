Transaction Validity

* The transaction is a well-formed RLP.
* The transaction signature is valid.
* The transaction nonce is valid \(same as the transaction sender account current nonce\).
* The transaction's intrinsic cost is lower than the transaction gas limit.
* The transaction sender account balance is equal to or higher than the required upfront payment for the transaction.
* Transaction Gas Limit Not Exceeding Block Gas Limit

Transaction Execution

* Increment sender account nonce by one.
* Reduce the sender balance by part of the up-front cost \(gasLimit \* gasPrice\).
* Define the gas available for the execution \(gasLimit - intrinsic cost\).
* Execute the transaction operations \(value transfer, message call or contract creation\).
* Refund sender for SELFDESTRUCT and SSTORE operations.
* Refund any remaining gas to the sender.
* Pay mining fees to the beneficiary account. The miner of the block that includes this transaction chooses the beneficiary account \(usually an account that belongs to the miner\).

代码：

```go
func (eth *Ethereum) stateAtTransaction(block *types.Block, txIndex int, reexec uint64) (core.Message, vm.BlockContext, *state.StateDB, error) {
    // ...
}

// stateAtBlock ...
```



