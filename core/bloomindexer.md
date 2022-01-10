```go
// BloomIndexer implements a core.ChainIndexer, building up a rotated bloom bits index
// for the Ethereum header bloom filters, permitting blazing fast filtering.
type BloomIndexer struct {
    size    uint64               // section size to generate bloombits for
    db      ethdb.Database       // database instance to write index data and metadata into
    gen     *bloombits.Generator // generator to rotate the bloom bits crating the bloom index
    section uint64               // Section is the section number being processed currently
    head    common.Hash          // Head is the hash of the last header processed
}
```

实现了接口 core.ChainIndexerBackend，目的是更快的查询。

NewBloomIndexer

> 被 eth.New 构建 Ethereum 时调用
>
> 被 les.New 构建 LightEthereum 时调用



