```
// Oracle recommends gas prices based on the content of recent
```

```go
// blocks. Suitable for both light and full clients.
type Oracle struct {
    backend   OracleBackend
    lastHead  common.Hash
    lastPrice *big.Int
    maxPrice  *big.Int
    cacheLock sync.RWMutex
    fetchLock sync.Mutex

    defaultPrice      *big.Int
    sampleTxThreshold int

    checkBlocks int
    percentile  int
}
```

预言机，根据当前、历史区块情况，推荐 GasPrice.

配置

```go
type Config struct {
    Blocks           int
    Percentile       int
    MaxHeaderHistory int
    MaxBlockHistory  int
    Default          *big.Int `toml:",omitempty"`
    MaxPrice         *big.Int `toml:",omitempty"`
    IgnorePrice      *big.Int `toml:",omitempty"`
}
```

NewOracle

> 在 eth.APIBackend.gpo = gasprice.NewOracle 被调用
>
> 在 leth.ApiBackend.gpo = gasprice.NewOracle 被调用



