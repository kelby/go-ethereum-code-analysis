外部账户特点总结：

* 拥有以太余额。

* 能发送交易，包括转账和执行合约代码。

* 被私钥控制。

* 没有相关的可执行代码。

合约账户特点总结：

* 拥有以太余额。

* 有相关的可执行代码（合约代码）。

* 合约代码能够被交易或者其他合约消息调用。

* 合约代码被执行时可再调用其他合约代码。

* 合约代码被执行时可执行复杂运算，可永久地改变合约内部的数据存储。

以太坊外部账户只由一个独立私钥创建，无法进行多签。 但合约具有可编程性，可编写符合多重签名的逻辑，实现一个支持多签的账户。

数据结构：

```go
// Account is the Ethereum consensus representation of accounts.
// These objects are stored in the main account trie.
type Account struct {
	Nonce    uint64
	Balance  *big.Int
	Root     common.Hash // merkle root of the storage trie
	CodeHash []byte
}
```



