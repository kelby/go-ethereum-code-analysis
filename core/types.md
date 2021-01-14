* 交易 Block
* 过滤器 Bloom
* 区块头 Header
* 区块体 Body（区块数据结构里非区块头内容）
* 消息 Message
* 收据 Receipt
* 交易 Transaction

一个关键的数据结构：

交易

```go
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

区块

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

区块头

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

一个区块在数据库中被分为Header和Body两部分。

通过源码我们可以看到Bloom数组就是从收据\(Receipt\)中的Logs字段生成的。具体的内容是Logs中的Address和Topics字段。

区块头的Bloom字段其实就是根据一套算法（bloom9\)将合约Address和Topics生成了一个2048字节的数组。方便用户可以查看自己感兴趣的日志。

bloom过滤器是用来快速的查找log的，那以太坊是如何用bloom过滤器来查找的呢？

想要要找某一条log，如果从区块链的头区块开始，根据区块头的hash依次开始查找的话是效率比较低的，每个区块写在本地数据库是散列存储的， 会增加很多io请求，io请求的速度很慢的。如何能快速的找到目的区块，这时候就要用到Chain\_Indexer。以太坊的BloomIndexer具体实现了Chain\_Indexer，可以认为是Chain\_Indexer的派生类。

> 以太坊先创建topics的bloom，再创建logs的bloom，再创建收据的bloom，在创建header的bloom，最后创建block的bloom，一步一步构建上去。
>
> 于此对应的，在查找日志的过程正好相反，先在block的bloom里面找，再在header的bloom里面找，再在收据的bloom里面找，直到找到最终的日志。

回执/收据，数据结构：

```go
// Receipt represents the results of a transaction.
type Receipt struct {
    // Consensus fields: These fields are defined by the Yellow Paper
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

回执信息分为三部分：共识信息、交易信息、区块信息。

