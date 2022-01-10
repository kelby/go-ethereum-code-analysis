broadcast.go 广播

broadcastBlocks

broadcastTransactions

announceTransactions

discovery.go 发现

StartENRUpdater

currentENREntry

dispatcher.go 转发

dispatchRequest

dispatchResponse

dispatcher

handler.go 处理

Handle

handleMessage

handlers.go 处理

handleGetBlockHeaders66

handleGetBlockBodies66

handleGetNodeData66

handleGetReceipts66

handleNewBlockhashes

handleNewBlock

handleBlockHeaders66

handleBlockBodies66

handleNodeData66

handleReceipts66

handleNewPooledTransactionHashes

handleGetPooledTransactions66

handleTransactions

handlePooledTransactions66

handshake.go 握手

Handshake

peer.go 节点

```go
// Peer is a collection of relevant information we have about a `eth` peer.
type Peer struct {
	id string // Unique ID for the peer, cached

	*p2p.Peer                   // The embedded P2P package peer
	rw        p2p.MsgReadWriter // Input/output streams for snap
	version   uint              // Protocol version negotiated

	head common.Hash // Latest advertised head block hash
	td   *big.Int    // Latest advertised head block total difficulty

	knownBlocks     *knownCache            // Set of block hashes known to be known by this peer
	queuedBlocks    chan *blockPropagation // Queue of blocks to broadcast to the peer
	queuedBlockAnns chan *types.Block      // Queue of blocks to announce to the peer

	txpool      TxPool             // Transaction pool used by the broadcasters for liveness checks
	knownTxs    *knownCache        // Set of transaction hashes known to be known by this peer
	txBroadcast chan []common.Hash // Channel used to queue transaction propagation requests
	txAnnounce  chan []common.Hash // Channel used to queue transaction announcement requests

	reqDispatch chan *request  // Dispatch channel to send requests and track then until fulfilment
	reqCancel   chan *cancel   // Dispatch channel to cancel pending requests and untrack them
	resDispatch chan *response // Dispatch channel to fulfil pending requests and untrack them

	term chan struct{} // Termination channel to stop the broadcasters
	lock sync.RWMutex  // Mutex protecting the internal fields
}
```

protocol.go 协议

略

tracker.go 跟踪

略



