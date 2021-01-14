交易数据结构，根据用途，将其划分为四部分。

![](/assets/txdata.png)

```go
type Transaction struct {
    data txdata    // Consensus contents of a transaction
    time time.Time // Time first seen locally (spam avoidance)

    // caches
    hash atomic.Value
    size atomic.Value
    from atomic.Value
}

type txdata struct {
    AccountNonce uint64          `json:"nonce"    gencodec:"required"`
    Price        *big.Int        `json:"gasPrice" gencodec:"required"`
    GasLimit     uint64          `json:"gas"      gencodec:"required"`
    Recipient    *common.Address `json:"to"       rlp:"nil"` // nil means contract creation
    Amount       *big.Int        `json:"value"    gencodec:"required"`
    Payload      []byte          `json:"input"    gencodec:"required"`

    // Signature values
    V *big.Int `json:"v" gencodec:"required"`
    R *big.Int `json:"r" gencodec:"required"`
    S *big.Int `json:"s" gencodec:"required"`

    // This is only used when marshaling to JSON.
    Hash *common.Hash `json:"hash" rlp:"-"`
}
```

在区块链中最见的是哈希运算，所有链上数据基本都要参与哈希运算。而哈希运算是CPU密集型的，因此有必要对一些哈希运算等进行缓存，降低CPU计算量。首次计算完交易哈希值后，便缓存交易哈希到 hash 字段上。

