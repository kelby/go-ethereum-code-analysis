包trie 实现了Merkle Patricia Tries，这里用简称MPT来称呼这种数据结构，这种数据结构实际上是一种Trie树变种，MPT是以太坊中一种非常重要的数据结构，用来存储用户账户的状态及其变更、交易信息、交易的收据信息。MPT实际上是三种数据结构的组合，分别是Trie树， Patricia Trie， 和Merkle树。

* 数据库 Database
* 迭代器 Iterator
* 安全树 SecureTrie
* 堆栈树 StackTrie
* 同步 Sync
* 树 Trie

Merkle Patricia Tree（又称为Merkle Patricia Trie）是一种经过改良的、融合了Merkle tree和前缀树两种树结构优点的数据结构，是以太坊中用来组织管理账户数据、生成交易集合哈希的重要数据结构。

MPT结合了Radix trie和Merkle两种树结构的特点与优势

MPT 全称是 Merkle Patricia Trie 也叫 Merkle Patricia Tree，是 Merkle Tree 和 Patricia Tree 的混合物。 Merkle Tree\(默克尔树\) 用于保证数据安全，Patricia Tree\(基数树,也叫基数特里树或压缩前缀树\) 用于提升树的读写效率。

以太坊区块头不是只包括一棵MPT树，针对三种对象，以太坊设计了三种树：交易树（Transaction Tree）、状态树（State Tree）和收据树（Receipt Tree）。下图展示了三棵树在区块头的存储情况，分别存储了三棵树的树根节点的散列值。

![](/assets/block-head-mpt.png)

