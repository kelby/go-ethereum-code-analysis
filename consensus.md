共识算法是区块链项目的核心之一，每一个运行着的区块链都需要一个共识算法来保证出块的有效性和有序性。

核心就是接口 Engine，下面具体的不同挖矿算法实现它即可。当前共有 2 套实现，如定制，按约定新增实现即可。

在以太坊的官方源码中，有两个共识算法：clique和ethash，它们都位于以太坊项目的consensus目录下。

clique目录下的代码实现的是PoA\(权威证明，Proof of Authority\)共识；在ethash目录下实现的是PoW\(工作量证明，Proof of Work\)共识。

clique（PoA类型）仅在测试网络里使用，真实的以太坊主网还是使用PoW算法（ethash模块实现）

algorithm.go 实现了Dagger-Hashimoto算法的所有功能，比如生成cache和dataset、根据Header和Nonce计算挖矿哈希等。

api.go 实现了供RPC使用的api方法。

consensus.go 实现了以太坊共识接口的部分方法，包括Verify系列方法（VerifyHeader、VerifySeal等）、Prepare和Finalize、CalcDifficulty、Author、SealHash。

ethash.go 实现了cache结构体和dataset结构体及它们各自的方法、MakeCache/MakeDataset函数、Ethash对象的New函数，和Ethash的内部方法。

sealer.go 实现了共识接口的Seal方法，和Ethash的内部方法mine。这些方法实现了ethash的挖矿功能。

---

API 文件

算法核心

Snapshot 快照（算法补充）

---

prepare 预处理当前区块的头信息（比如 挖矿难度）

seal 开始做挖矿的事情，“解题”直到成功或者退出

Finalize 做挖矿成功后最后善后的事情

verifyHeader

VerifySeal

> 区块链核心内容之一。



