```
// A BlockNonce is a 64-bit hash which proves (combined with the
```

```go
// mix-hash) that a sufficient amount of computation has been carried
// out on a block.
type BlockNonce [8]byte
```

区块头：

```go
// Header represents a block header in the Ethereum blockchain.
type Header struct {
    ParentHash  common.Hash    `json:"parentHash"       gencodec:"required"`
    UncleHash   common.Hash    `json:"sha3Uncles"       gencodec:"required"`
    Coinbase    common.Address `json:"miner"            gencodec:"required"`
    Root        common.Hash    `json:"stateRoot"        gencodec:"required"`
    TxHash      common.Hash    `json:"transactionsRoot" gencodec:"required"`
    ReceiptHash common.Hash    `json:"receiptsRoot"     gencodec:"required"`
    Bloom       Bloom          `json:"logsBloom"        gencodec:"required"`
    Difficulty  *big.Int       `json:"difficulty"       gencodec:"required"`
    Number      *big.Int       `json:"number"           gencodec:"required"`
    GasLimit    uint64         `json:"gasLimit"         gencodec:"required"`
    GasUsed     uint64         `json:"gasUsed"          gencodec:"required"`
    Time        uint64         `json:"timestamp"        gencodec:"required"`
    Extra       []byte         `json:"extraData"        gencodec:"required"`
    MixDigest   common.Hash    `json:"mixHash"`
    Nonce       BlockNonce     `json:"nonce"`
}
```

ParentHash表示父区块的哈希，UncleHash是叔父区块哈希，Coinbase是示矿工账户地址，Root是状态树的根哈希，TxHash是交易树的根哈希，ReceiptHash是收据树的根哈希，Bloom是块头的bloom filter，Difficulty是挖矿难度（可根据需要调整），GasLimit和GasUsed与汽油费相关，Time是区块大致产生时间，MixDigest与挖矿过程相关，从Nonce经过一些列计算而来，Nonce是挖矿的谜底随机数。

区块体：

```go
// Body is a simple (mutable, non-safe) data container for storing and moving
// a block's data contents (transactions and uncles) together.
type Body struct {
    Transactions []*Transaction
    Uncles       []*Header
}
```

区块：

```go
// Block represents an entire block in the Ethereum blockchain.
type Block struct {
    header       *Header
    uncles       []*Header
    transactions Transactions

    // caches
    hash atomic.Value
    size atomic.Value

    // Td is used by package core to store the total difficulty
    // of the chain up to and including the block.
    td *big.Int

    // These fields are used by package eth to track
    // inter-peer block relay.
    ReceivedAt   time.Time
    ReceivedFrom interface{}
}
```

区块和里面所包含的“树”：

![](/assets/block-and-trie.png)以太坊有四种前缀树：

（1）状态树包括了从地址到账户状态之间的映射。状态树的根节点哈希值由区块保存（在 stateRoot 字段），它标示了区块创建时的当前状态。整个网络中只有一个状态树。

状态标识了以太坊这台分布式计算机的硬盘。它是从地址到账户状态的映射。

（2）交易树包含了一个区块中的所有交易信息。由区块头（在 transactionsRoot 区域）保存交易树的根节点哈希值。每个区块都有一棵交易树。

交易标示了系统中的状态转移。它可以是资金的转移、消息调用或是合约的部署。

（3）交易收据树包含了一个区块中所有交易的收据信息。同样由区块头（在 receiptsRoot 区域）保存交易收据树的根节点哈希值；每个区块都有对应的交易收据树。

（4）账户存储树保存了与某一智能合约相关的数据信息。由账户状态保存账户存储树的根节点哈希值（在 storageRoot 字段）。每个账户都有一个账户存储树。账户状态保存着每个以太坊账户的状态信息。账户状态同样保存着账户状态树的 storageRoot，后者包含了该账户的存储数据。

