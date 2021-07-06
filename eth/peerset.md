peerSet是peer对象的集合，是对peer对象的管理。

```go
// peerSet represents the collection of active peers currently participating in
// the `eth` protocol, with or without the `snap` extension.
type peerSet struct {
	peers     map[string]*ethPeer // Peers connected on the `eth` protocol
	snapPeers int                 // Number of `snap` compatible peers for connection prioritization

	snapWait map[string]chan *snap.Peer // Peers connected on `eth` waiting for their snap extension
	snapPend map[string]*snap.Peer      // Peers connected on the `snap` protocol, but not yet on `eth`

	lock   sync.RWMutex
	closed bool
}
```



