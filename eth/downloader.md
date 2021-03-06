downloader主要负责区块链最开始的同步工作，当前的同步有两种模式。

一种是传统的fullmode,这种模式通过下载区块头，和区块体来构建区块链，同步的过程就和普通的区块插入的过程一样，包括区块头的验证，交易的验证，交易执行，账户状态的改变等操作，这其实是一个比较消耗CPU和磁盘的一个过程。

另一种模式就是 快速同步的fast sync模式， 这种模式有专门的文档来描述。请参考fast sync的文档。简单的说 fast sync的模式会下载区块头，区块体和收据， 插入的过程不会执行交易，然后在一个区块高度\(最高的区块高度 - 1024\)的时候同步所有的账户状态，后面的1024个区块会采用fullmode的方式来构建。 这种模式会加区块的插入时间，同时不会产生大量的历史的账户信息。会相对节约磁盘， 但是对于网络的消耗会更高。 因为需要下载收据和状态。

* downloader.go ：实现了区块同步逻辑

* peer.go ：对区块各个阶段的组装，下面的各个FetchXXX 就是很依赖这个模块。

* queue.go ：对eth/peer.go的封装，可以理解为这是一个对区块的组装队列

* statesync.go ：同步state对象

fetcher 模块和 downloader 模块所承担的任务是不同的：

downloader 功能比较重，用来保证自己的区块链和其它节点之间不会有太多差距。

在以太坊中，区块同步有三种同步模式：full、fast、light。

在一些消息处理代码中，会将收到的数据交给fetcher的filter方法处理，剩下的再交给downloader的Deliver方法处理。

区块下载流程：

![](/assets/block-download-example.png)

downloader.New

> 被 h.downloader = downloader.New 调用



