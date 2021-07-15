```go
type DynamicFeeTx struct {
	ChainID    *big.Int
	Nonce      uint64
	Tip        *big.Int
	FeeCap     *big.Int
	Gas        uint64
	To         *common.Address `rlp:"nil"` // nil means contract creation
	Value      *big.Int
	Data       []byte
	AccessList AccessList

	// Signature values
	V *big.Int `json:"v" gencodec:"required"`
	R *big.Int `json:"r" gencodec:"required"`
	S *big.Int `json:"s" gencodec:"required"`
}
```

实现了 TxData 接口。



