```go
// AccessList is an EIP-2930 access list.
type AccessList []AccessTuple
```

```go
// AccessTuple is the element type of an access list.
type AccessTuple struct {
    Address     common.Address `json:"address"        gencodec:"required"`
    StorageKeys []common.Hash  `json:"storageKeys"    gencodec:"required"`
}
```

```go
// AccessListTx is the data of EIP-2930 access list transactions.
type AccessListTx struct {
    ChainID    *big.Int        // destination chain ID
    Nonce      uint64          // nonce of sender account
    GasPrice   *big.Int        // wei per gas
    Gas        uint64          // gas limit
    To         *common.Address `rlp:"nil"` // nil means contract creation
    Value      *big.Int        // wei amount
    Data       []byte          // contract invocation input data
    AccessList AccessList      // EIP-2930 access list
    V, R, S    *big.Int        // signature values
}
```

实现 TxData 接口，对应需求 EIP-2930 访问列表。

