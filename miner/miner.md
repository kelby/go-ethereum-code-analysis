Miner creates blocks and searches for proof-of-work values.

```go
type Miner struct {
    mux      *event.TypeMux
    worker   *worker
    coinbase common.Address
    eth      Backend
    engine   consensus.Engine
    exitCh   chan struct{}
    startCh  chan common.Address
    stopCh   chan struct{}
}
```

上游：eth 是个接口

下游：worker

下游：engine 也是个接口

核心：coinbase 矿工收益地址

