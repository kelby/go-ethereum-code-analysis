peer对象代表的是与之建立连接的对方节点，其主要功能有两个：一是在本地记录对方一些已经拥有的数据，比如对方已经拥有了哪些block、哪些transaction、对方最新的区块哈希和TD是多少；二是发送数据给对方。

```go
// ethPeer is a wrapper around eth.Peer to maintain a few extra metadata.
type ethPeer struct {
	*eth.Peer
	snapExt *snapPeer // Satellite `snap` connection

	syncDrop *time.Timer   // Connection dropper if `eth` sync progress isn't validated in time
	snapWait chan struct{} // Notification channel for snap connections
	lock     sync.RWMutex  // Mutex protecting the internal fields
}
```



