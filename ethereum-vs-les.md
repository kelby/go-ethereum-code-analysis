同步模式有3种：

* full
* snap
* light

前面两者同步还能切换，是兼容的，最后一种则不然，数据结构和数据都不同。代码理解上，可以认为是两条链。

前面两种由 Ethereum 处理，最后一种由 LightEthereum 处理。

前面两种打交道的是 core.BlockChain，最后一种打交道的是 light.LightChain.

前面两种打交道的是 core.TxPool，最后一种打交道的是 light.TxPool.

三大不同：

* 不同的链
* 不同的后端服务（指的是和钱包、客户端、API、命令行服务所对应的后端
* 不同的网络同步、下载服务



