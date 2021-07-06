params/config.go

修改代号、硬分叉信息。

consensus/ethash/consensus.go

修改区块奖励和难度值。

cmd/puppeth/genesis.go & core/vm/runtime/runtime.go

硬分叉信息。

consensus/ethash/consensus.go & consensus/ethash/difficulty.go

不同版本，难度值计算方式不一样。

consensus/ethash/consensus.go

不同分叉，奖励值不一样。

params/bootnodes.go

修改启动节点。

\[美图技术团队发布开源 ethereum dpos 实现\]\([https://zhuanlan.zhihu.com/p/38013479\](https://zhuanlan.zhihu.com/p/38013479%29\)

\[Ethereum DPOS源码分析\]\([https://segmentfault.com/a/1190000017505457\](https://segmentfault.com/a/1190000017505457%29\)

\[go-popcateum\]\([https://github.com/popcateum/go-popcateum\](https://github.com/popcateum/go-popcateum%29\)

\[dpos-go-ethereum\]\([https://github.com/mingjingc/dpos-go-ethereum\](https://github.com/mingjingc/dpos-go-ethereum%29\)

\[gttc\]\([https://github.com/TTCECO/gttc\](https://github.com/TTCECO/gttc%29\)

\[go-ethereum\]\([https://github.com/jason-wj/go-ethereum\](https://github.com/jason-wj/go-ethereum%29\)

\[go-ethereum-Hashchain\]\([https://github.com/evilmao/go-ethereum-Hashchain\](https://github.com/evilmao/go-ethereum-Hashchain%29\)

\[golang-Eos-dpos-Ethereum\]\([https://github.com/yinchengtsinghua/golang-Eos-dpos-Ethereum\](https://github.com/yinchengtsinghua/golang-Eos-dpos-Ethereum%29\)

\[币安智能链\]\([https://github.com/binance-chain/whitepaper/blob/master/币安智能链.md\](https://github.com/binance-chain/whitepaper/blob/master/币安智能链.md%29\)

---

品牌、配置的修改（略）

新共识算法的引入：

* consensus 下新共识算法的独立开发
* 新共识算法的集成
* 系统合约的部署、运行，后续升级
* 合约手续费问题（没有减，只有给矿工加，由系统决定）
* 可能影响的签名（共识做为里面的一个字符串前缀之类的东西）



