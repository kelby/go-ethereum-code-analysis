```go
// SyncMode represents the synchronisation mode of the downloader.
// It is a uint32 as it is used with atomic operations.
type SyncMode uint32

const (
    FullSync  SyncMode = iota // Synchronise the entire blockchain history from full blocks
    FastSync                  // Quickly download the headers, full sync only at the chain
    SnapSync                  // Download the chain and the state via compact snapshots
    LightSync                 // Download only the headers and terminate afterwards
)
```

区块头、区块体（原始数据），收据（处理结构），状态数据（处理过程/明细）。

downloader主要负责区块链最开始的同步工作，当前的同步有两种模式：

* 一种是传统的fullmode,这种模式通过下载区块头，和区块体来构建区块链，同步的过程就和普通的区块插入的过程一样，包括区块头的验证，交易的验证，交易执行，账户状态的改变等操作，这其实是一个比较消耗CPU和磁盘的一个过程。 
* 一种模式就是 快速同步的fast sync模式， 这种模式有专门的文档来描述。简单的说 fast sync的模式会下载区块头，区块体和收据， 插入的过程不会执行交易，然后在一个区块高度\(最高的区块高度 - 1024\)的时候同步所有的账户状态，后面的1024个区块会采用fullmode的方式来构建。

pivot 共同祖先，锚点？

fetchHeaders - fetchHeaders方法用来获取header。 然后根据获取的header去获取body和receipt等信息。fetchHeaders不断的重复这样的操作，发送header请求，等待所有的返回。直到完成所有的header请求。 为了提高并发性，同时仍然能够防止恶意节点发送错误的header，我们使用我们正在同步的“origin”peer构造一个头文件链骨架，并使用其他人填充缺失的header。 其他peer的header只有在干净地映射到骨架上时才被接受。 如果没有人能够填充骨架 - 甚至origin peer也不能填充 - 它被认为是无效的，并且origin peer也被丢弃。

processFastSyncContent

同步模式：

* full mode - 在数据库中保存所有区块数据，但在同步时，只从远程节点同步 header 和 body 数据，state 和 receipt 数据则是在本地计算出来的。
* fast mode - recepit 不再由本地计算，而是和区块数据一样，直接由 downloader 从其它节点中同步；state 数据并不会全部计算和下载，而是选一个较新的区块（称之为 pivot，后面会有详细解释）的 state 进行下载，以这个区块为分界，之前的区块是没有 state 数据的，之后的区块会像 full 模式下一样在本地计算 state
* ligth mode - 它只对区块头进行同步，而不同步其它的数据。

通道：

* headerCh : 传送header数据

* bodyCh : 传送body数据

* receiptCh: 传送receipt数据

* stateCh : 传送state数据

downloader 的工作流程：

![](/assets/eth-downloader-process.png)

findAncestor - 区块同步的第一件重要的事情，就是确定需要同步哪些区块。

