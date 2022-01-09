fetcher包含基于块通知的同步。当我们接收到NewBlockHashesMsg消息得时候，我们只收到了很多Block的hash值。 需要通过hash值来同步区块，然后更新本地区块链。 fetcher就提供了这样的功能。

fetcher 模块和 downloader 模块所承担的任务是不同的：

* downloader 功能比较重，用来保证自己的区块链和其它节点之间不会有太多差距；

* fetcher 功能较轻，只会对矿工新产生的区块进行同步和广播。

在一些消息处理代码中，会将收到的数据交给fetcher的filter方法处理，剩下的再交给downloader的Deliver方法处理。

一个区块的同步流程:

* 发现区块
* 通知（Fetcher.announced）
* 下载中（Fetcher.fetching）
* header 下载完成（Fetcher.fetched）
* body 下载中（Fetcher.completing）
* body 下载完成（Fetcher.queue）
* 正式入库

Fetcher 对象对外导出的方法中，能导致同步功能的只有两个方法：

* Fetcher.Notify
* Fetcher.Enqueue

这两个方法分别在 ProtocolManager.handleMsg 中处理 NewBlockHashesMsg 和 NewBlockMsg 两个消息时被调用。

这两个消息又是分别由 peer.AsyncSendNewBlockHash 和 peer.AsyncSendNewBlock 两个方法发出的，这两个方法只有在矿工挖到新的区块时才会被调用。

