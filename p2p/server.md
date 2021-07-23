Server manages all peer connections.

P2P概念上的“网络节点”

```go
type Server struct {
    // Config fields may not be modified while the server is running.
    Config

    // Hooks for testing. These are useful because we can inhibit
    // the whole protocol stack.
    newTransport func(net.Conn, *ecdsa.PublicKey) transport
    newPeerHook  func(*Peer)
    listenFunc   func(network, addr string) (net.Listener, error)

    lock    sync.Mutex // protects running
    running bool

    listener     net.Listener
    ourHandshake *protoHandshake
    loopWG       sync.WaitGroup // loop, listenLoop
    peerFeed     event.Feed
    log          log.Logger

    nodedb    *enode.DB
    localnode *enode.LocalNode
    ntab      *discover.UDPv4
    DiscV5    *discover.UDPv5
    discmix   *enode.FairMix
    dialsched *dialScheduler

    // Channels into the run loop.
    quit                    chan struct{}
    addtrusted              chan *enode.Node
    removetrusted           chan *enode.Node
    peerOp                  chan peerOpFunc
    peerOpDone              chan struct{}
    delpeer                 chan peerDrop
    checkpointPostHandshake chan *conn
    checkpointAddPeer       chan *conn

    // State of run loop and listenLoop.
    inboundHistory expHeap
}
```

有配置项：

```go
// Config holds Server options.
type Config struct {
    // This field must be set to a valid secp256k1 private key.
    PrivateKey *ecdsa.PrivateKey `toml:"-"`

    // MaxPeers is the maximum number of peers that can be
    // connected. It must be greater than zero.
    MaxPeers int

    // MaxPendingPeers is the maximum number of peers that can be pending in the
    // handshake phase, counted separately for inbound and outbound connections.
    // Zero defaults to preset values.
    MaxPendingPeers int `toml:",omitempty"`

    // DialRatio controls the ratio of inbound to dialed connections.
    // Example: a DialRatio of 2 allows 1/2 of connections to be dialed.
    // Setting DialRatio to zero defaults it to 3.
    DialRatio int `toml:",omitempty"`

    // NoDiscovery can be used to disable the peer discovery mechanism.
    // Disabling is useful for protocol debugging (manual topology).
    NoDiscovery bool

    // DiscoveryV5 specifies whether the new topic-discovery based V5 discovery
    // protocol should be started or not.
    DiscoveryV5 bool `toml:",omitempty"`

    // Name sets the node name of this server.
    // Use common.MakeName to create a name that follows existing conventions.
    Name string `toml:"-"`

    // BootstrapNodes are used to establish connectivity
    // with the rest of the network.
    BootstrapNodes []*enode.Node

    // BootstrapNodesV5 are used to establish connectivity
    // with the rest of the network using the V5 discovery
    // protocol.
    BootstrapNodesV5 []*enode.Node `toml:",omitempty"`

    // Static nodes are used as pre-configured connections which are always
    // maintained and re-connected on disconnects.
    StaticNodes []*enode.Node

    // Trusted nodes are used as pre-configured connections which are always
    // allowed to connect, even above the peer limit.
    TrustedNodes []*enode.Node

    // Connectivity can be restricted to certain IP networks.
    // If this option is set to a non-nil value, only hosts which match one of the
    // IP networks contained in the list are considered.
    NetRestrict *netutil.Netlist `toml:",omitempty"`

    // NodeDatabase is the path to the database containing the previously seen
    // live nodes in the network.
    NodeDatabase string `toml:",omitempty"`

    // Protocols should contain the protocols supported
    // by the server. Matching protocols are launched for
    // each peer.
    Protocols []Protocol `toml:"-"`

    // If ListenAddr is set to a non-nil address, the server
    // will listen for incoming connections.
    //
    // If the port is zero, the operating system will pick a port. The
    // ListenAddr field will be updated with the actual address when
    // the server is started.
    ListenAddr string

    // If set to a non-nil value, the given NAT port mapper
    // is used to make the listening port available to the
    // Internet.
    NAT nat.Interface `toml:",omitempty"`

    // If Dialer is set to a non-nil value, the given Dialer
    // is used to dial outbound peer connections.
    Dialer NodeDialer `toml:"-"`

    // If NoDial is true, the server will not dial any peers.
    NoDial bool `toml:",omitempty"`

    // If EnableMsgEvents is set then the server will emit PeerEvents
    // whenever a message is sent to or received from a peer
    EnableMsgEvents bool

    // Logger is a custom logger to use with the p2p.Server.
    Logger log.Logger `toml:",omitempty"`

    clock mclock.Clock
}
```

start函数主要做了以下6件事：

* 初始化server的字段

* 设置本地节点setupLocalNode

* 设置监听TCP连接请求setupListening

* 设置节点发现（setupDiscovery）

* 设置最大可以主动发起的连接为50/3

* srv.run\(dialer\) 发起建立TCP连接请求



