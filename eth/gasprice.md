```go
// Oracle recommends gas prices based on the content of recent
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



