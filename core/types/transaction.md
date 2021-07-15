区块链系统里的“交易”。

```go
// Transaction is an Ethereum transaction.
type Transaction struct {
    inner TxData    // Consensus contents of a transaction
    time  time.Time // Time first seen locally (spam avoidance)

    // caches
    hash atomic.Value
    size atomic.Value
    from atomic.Value
}
```

```go
// Transactions implements DerivableList for transactions.
type Transactions []*Transaction
```

概念的对应转换：当进入虚拟机，做为合约的一部分时，“交易”会被转换成“消息”，进行后续处理。

```go
// Message is a fully derived transaction and implements core.Message
//
// NOTE: In a future PR this will be removed.
type Message struct {
    to         *common.Address
    from       common.Address
    nonce      uint64
    amount     *big.Int
    gasLimit   uint64
    gasPrice   *big.Int
    data       []byte
    accessList AccessList
    checkNonce bool
}
```

有不同类型交易，交易数据以接口的方式存在：

```go
// TxData is the underlying data of a transaction.
//
// This is implemented by LegacyTx and AccessListTx.
type TxData interface {
    txType() byte // returns the type ID
    copy() TxData // creates a deep copy and initializes all fields

    chainID() *big.Int
    accessList() AccessList
    data() []byte
    gas() uint64
    gasPrice() *big.Int
    tip() *big.Int
    feeCap() *big.Int
    value() *big.Int
    nonce() uint64
    to() *common.Address

    rawSignatureValues() (v, r, s *big.Int)
    setSignatureValues(chainID, v, r, s *big.Int)
}
```

当前有 AccessListTx、DynamicFeeTx、LegacyTx 3 种交易。

