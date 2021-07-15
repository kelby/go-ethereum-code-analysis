```go
// BlockGen creates blocks for testing.
// See GenerateChain for a detailed explanation.
type BlockGen struct {
    i       int
    parent  *types.Block
    chain   []*types.Block
    header  *types.Header
    statedb *state.StateDB

    gasPool  *GasPool
    txs      []*types.Transaction
    receipts []*types.Receipt
    uncles   []*types.Header

    config *params.ChainConfig
    engine consensus.Engine
}
```

用于测试生成区块。



