# 以太坊 Ethereum\(ETH\) 源码解读

使用以太坊 Ethereum\(ETH\) 源码，进行二次开发，在此过程中结合网上搜索到的资料，以及自己的理解，编写而成。

部分内容为原创，部分内容为网上资料，参考来源众多，不一一列举。

如有错误，敬请详解。

本图书 GitBook 链接：

[https://kelby.gitbook.io/ethereum-eth/](#以太坊-ethereumeth-源码解读)

以太坊项目太大，文件、目录众多，建议重点关注“区块链核心”和“命令行客户端”部分，即：

* cmd, 特别是 geth 和 utils 部分
* node
* consensus
* miner
* accounts, 特别是 keystore 部分
* eth, 又可拆分 Ethereum, gasprice, api, handler, downloader/fetcher, protocols 等
* les, 又可拆分 LightEthereum, api, handler, downloader/fetcher 等
* light
* core, 又可拆分 BlockChain, Genesis, Block, TxPool, Transaction 等
* params

其它部分，可归为“P2P 网络”、“Key-Value 存储”、“密码学”、“行业标准库”，以及“工具库、底层库”也很重要。

但从上述重点关注下手，理清脉络。

#### 更多项目源码解析

[https://cointoken100.com](https://cointoken100.com)

