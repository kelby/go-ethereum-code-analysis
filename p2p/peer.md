Peer represents a connected remote node.

P2P概念上的“远程节点”：

```go
// Peer represents a connected remote node.
type Peer struct {
	rw      *conn
	running map[string]*protoRW
	log     log.Logger
	created mclock.AbsTime

	wg       sync.WaitGroup
	protoErr chan error
	closed   chan struct{}
	disc     chan DiscReason

	// events receives message send / receive events if set
	events   *event.Feed
	testPipe *MsgPipeRW // for testing
}
```



