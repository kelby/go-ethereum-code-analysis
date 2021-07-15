```go
// Receipt represents the results of a transaction.
type Receipt struct {
    // Consensus fields: These fields are defined by the Yellow Paper
    Type              uint8  `json:"type,omitempty"`
    PostState         []byte `json:"root"`
    Status            uint64 `json:"status"`
    CumulativeGasUsed uint64 `json:"cumulativeGasUsed" gencodec:"required"`
    Bloom             Bloom  `json:"logsBloom"         gencodec:"required"`
    Logs              []*Log `json:"logs"              gencodec:"required"`

    // Implementation fields: These fields are added by geth when processing a transaction.
    // They are stored in the chain database.
    TxHash          common.Hash    `json:"transactionHash" gencodec:"required"`
    ContractAddress common.Address `json:"contractAddress"`
    GasUsed         uint64         `json:"gasUsed" gencodec:"required"`

    // Inclusion information: These fields provide information about the inclusion of the
    // transaction corresponding to this receipt.
    BlockHash        common.Hash `json:"blockHash,omitempty"`
    BlockNumber      *big.Int    `json:"blockNumber,omitempty"`
    TransactionIndex uint        `json:"transactionIndex"`
}
```

由三部分组成，具体见结构体代码里的注释。

```go
// Receipts implements DerivableList for receipts.
type Receipts []*Receipt
```

每个收据里都有一个Bloom Filter，记录了此交易的类型、地址等相关信息。

收据树 receiptsRoot

![](/assets/receipts-root.png)



