fetcher包含基于块通知的同步。当我们接收到NewBlockHashesMsg消息得时候，我们只收到了很多Block的hash值。 需要通过hash值来同步区块，然后更新本地区块链。 fetcher就提供了这样的功能。

fetcher 模块和 downloader 模块所承担的任务是不同的：

fetcher 功能较轻，只会对矿工新产生的区块进行同步和广播。

