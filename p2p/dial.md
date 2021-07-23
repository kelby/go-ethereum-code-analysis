拨号



dial.go在p2p里面主要负责建立链接的部分工作。 比如发现建立链接的节点。 与节点建立链接。

dialScheduler

```go
// dialer creates outbound connections and submits them into Server.
// Two types of peer connections can be created:
//
//  - static dials are pre-configured connections. The dialer attempts
//    keep these nodes connected at all times.
//
//  - dynamic dials are created from node discovery results. The dialer
//    continuously reads candidate nodes from its input iterator and attempts
//    to create peer connections to nodes arriving through the iterator.
//
type dialScheduler struct {
    dialConfig
    setupFunc   dialSetupFunc
    wg          sync.WaitGroup
    cancel      context.CancelFunc
    ctx         context.Context
    nodesIn     chan *enode.Node
    doneCh      chan *dialTask
    addStaticCh chan *enode.Node
    remStaticCh chan *enode.Node
    addPeerCh   chan *conn
    remPeerCh   chan *conn

    // Everything below here belongs to loop and
    // should only be accessed by code on the loop goroutine.
    dialing   map[enode.ID]*dialTask // active tasks
    peers     map[enode.ID]connFlag  // all connected peers
    dialPeers int                    // current number of dialed peers

    // The static map tracks all static dial tasks. The subset of usable static dial tasks
    // (i.e. those passing checkDial) is kept in staticPool. The scheduler prefers
    // launching random static tasks from the pool over launching dynamic dials from the
    // iterator.
    static     map[enode.ID]*dialTask
    staticPool []*dialTask

    // The dial history keeps recently dialed nodes. Members of history are not dialed.
    history          expHeap
    historyTimer     mclock.Timer
    historyTimerTime mclock.AbsTime

    // for logStats
    lastStatsLog     mclock.AbsTime
    doneSinceLastLog int
}
```

dialTask

```go
// A dialTask generated for each node that is dialed.
type dialTask struct {
    staticPoolIndex int
    flags           connFlag
    // These fields are private to the task and should not be
    // accessed by dialScheduler while the task is running.
    dest         *enode.Node
    lastResolved mclock.AbsTime
    resolveDelay time.Duration
}
```



