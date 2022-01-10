其中，6 个值得关注，而里面的 4 个值得重点关注。

gasprice \* 预言机

```go
type Config struct {
    Blocks           int
    Percentile       int
    MaxHeaderHistory int
    MaxBlockHistory  int
    Default          *big.Int `toml:",omitempty"`
    MaxPrice         *big.Int `toml:",omitempty"`
    IgnorePrice      *big.Int `toml:",omitempty"`
}
```

accounts

```go
// Config contains the settings of the global account manager.
//
// TODO(rjl493456442, karalabe, holiman): Get rid of this when account management
// is removed in favor of Clef.
type Config struct {
    InsecureUnlockAllowed bool // Whether account unlocking in insecure environment is allowed
}
```

ethash

```go
// Config are the configuration parameters of the ethash.
type Config struct {
    CacheDir         string
    CachesInMem      int
    CachesOnDisk     int
    CachesLockMmap   bool
    DatasetDir       string
    DatasetsInMem    int
    DatasetsOnDisk   int
    DatasetsLockMmap bool
    PowMode          Mode

    // When set, notifications sent by the remote sealer will
    // be block header JSON objects instead of work package arrays.
    NotifyFull bool

    Log log.Logger `toml:"-"`
}
```

console

```go
// Config is the collection of configurations to fine tune the behavior of the
// JavaScript console.
type Config struct {
    DataDir  string              // Data directory to store the console history at
    DocRoot  string              // Filesystem path from where to load JavaScript files from
    Client   *rpc.Client         // RPC client to execute Ethereum requests through
    Prompt   string              // Input prompt prefix string (defaults to DefaultPrompt)
    Prompter prompt.UserPrompter // Input prompter to allow interactive user feedback (defaults to TerminalPrompter)
    Printer  io.Writer           // Output writer to serialize any display strings to (defaults to os.Stdout)
    Preload  []string            // Absolute paths to JavaScript files to preload
}
```

vm 虚拟机、解析器

```go
// Config are the configuration options for the Interpreter
type Config struct {
    Debug                   bool      // Enables debugging
    Tracer                  EVMLogger // Opcode logger
    NoBaseFee               bool      // Forces the EIP-1559 baseFee to 0 (needed for 0 price calls)
    EnablePreimageRecording bool      // Enables recording of SHA3/keccak preimages

    JumpTable *JumpTable // EVM instruction table, automatically populated if unset

    ExtraEips []int // Additional EIPS that are to be enabled
}
```

runtime 运行时

```go
// Config is a basic type specifying certain configuration flags for running
// the EVM.
type Config struct {
    ChainConfig *params.ChainConfig
    Difficulty  *big.Int
    Origin      common.Address
    Coinbase    common.Address
    BlockNumber *big.Int
    Time        *big.Int
    GasLimit    uint64
    GasPrice    *big.Int
    Value       *big.Int
    Debug       bool
    EVMConfig   vm.Config
    BaseFee     *big.Int

    State     *state.StateDB
    GetHashFn func(n uint64) common.Hash
}
```

ethconfig \* 以太坊对象

```go
// Config contains configuration options for of the ETH and LES protocols.
type Config struct {
    // The genesis block, which is inserted if the database is empty.
    // If nil, the Ethereum main net block is used.
    Genesis *core.Genesis `toml:",omitempty"`

    // Protocol options
    NetworkId uint64 // Network ID to use for selecting peers to connect to
    SyncMode  downloader.SyncMode

    // This can be set to list of enrtree:// URLs which will be queried for
    // for nodes to connect to.
    EthDiscoveryURLs  []string
    SnapDiscoveryURLs []string

    NoPruning  bool // Whether to disable pruning and flush everything to disk
    NoPrefetch bool // Whether to disable prefetching and only load state on demand

    TxLookupLimit uint64 `toml:",omitempty"` // The maximum number of blocks from head whose tx indices are reserved.

    // Whitelist of required block number -> hash values to accept
    Whitelist map[uint64]common.Hash `toml:"-"`

    // Light client options
    LightServ          int  `toml:",omitempty"` // Maximum percentage of time allowed for serving LES requests
    LightIngress       int  `toml:",omitempty"` // Incoming bandwidth limit for light servers
    LightEgress        int  `toml:",omitempty"` // Outgoing bandwidth limit for light servers
    LightPeers         int  `toml:",omitempty"` // Maximum number of LES client peers
    LightNoPrune       bool `toml:",omitempty"` // Whether to disable light chain pruning
    LightNoSyncServe   bool `toml:",omitempty"` // Whether to serve light clients before syncing
    SyncFromCheckpoint bool `toml:",omitempty"` // Whether to sync the header chain from the configured checkpoint

    // Ultra Light client options
    UltraLightServers      []string `toml:",omitempty"` // List of trusted ultra light servers
    UltraLightFraction     int      `toml:",omitempty"` // Percentage of trusted servers to accept an announcement
    UltraLightOnlyAnnounce bool     `toml:",omitempty"` // Whether to only announce headers, or also serve them

    // Database options
    SkipBcVersionCheck bool `toml:"-"`
    DatabaseHandles    int  `toml:"-"`
    DatabaseCache      int
    DatabaseFreezer    string

    TrieCleanCache          int
    TrieCleanCacheJournal   string        `toml:",omitempty"` // Disk journal directory for trie cache to survive node restarts
    TrieCleanCacheRejournal time.Duration `toml:",omitempty"` // Time interval to regenerate the journal for clean cache
    TrieDirtyCache          int
    TrieTimeout             time.Duration
    SnapshotCache           int
    Preimages               bool

    // Mining options
    Miner miner.Config

    // Ethash options
    Ethash ethash.Config

    // Transaction pool options
    TxPool core.TxPoolConfig

    // Gas Price Oracle options
    GPO gasprice.Config

    // Enables tracking of SHA3 preimages in the VM
    EnablePreimageRecording bool

    // Miscellaneous options
    DocRoot string `toml:"-"`

    // RPCGasCap is the global gas cap for eth-call variants.
    RPCGasCap uint64

    // RPCEVMTimeout is the global timeout for eth-call.
    RPCEVMTimeout time.Duration

    // RPCTxFeeCap is the global transaction fee(price * gaslimit) cap for
    // send-transction variants. The unit is ether.
    RPCTxFeeCap float64

    // Checkpoint is a hardcoded checkpoint which can be nil.
    Checkpoint *params.TrustedCheckpoint `toml:",omitempty"`

    // CheckpointOracle is the configuration for checkpoint oracle.
    CheckpointOracle *params.CheckpointOracleConfig `toml:",omitempty"`

    // Arrow Glacier block override (TODO: remove after the fork)
    OverrideArrowGlacier *big.Int `toml:",omitempty"`

    // OverrideTerminalTotalDifficulty (TODO: remove after the fork)
    OverrideTerminalTotalDifficulty *big.Int `toml:",omitempty"`
}
```

miner \* 矿工

```go
// Config is the configuration parameters of mining.
type Config struct {
    Etherbase  common.Address `toml:",omitempty"` // Public address for block mining rewards (default = first account)
    Notify     []string       `toml:",omitempty"` // HTTP URL list to be notified of new work packages (only useful in ethash).
    NotifyFull bool           `toml:",omitempty"` // Notify with pending block headers instead of work packages
    ExtraData  hexutil.Bytes  `toml:",omitempty"` // Block extra data set by the miner
    GasFloor   uint64         // Target gas floor for mined blocks.
    GasCeil    uint64         // Target gas ceiling for mined blocks.
    GasPrice   *big.Int       // Minimum gas price for mining a transaction
    Recommit   time.Duration  // The time interval for miner to re-create mining work.
    Noverify   bool           // Disable remote mining solution verification(only useful in ethash).
}
```

node \* 服务节点

```go
// Config represents a small collection of configuration values to fine tune the
// P2P network layer of a protocol stack. These values can be further extended by
// all registered services.
type Config struct {
    // Name sets the instance name of the node. It must not contain the / character and is
    // used in the devp2p node identifier. The instance name of geth is "geth". If no
    // value is specified, the basename of the current executable is used.
    Name string `toml:"-"`

    // UserIdent, if set, is used as an additional component in the devp2p node identifier.
    UserIdent string `toml:",omitempty"`

    // Version should be set to the version number of the program. It is used
    // in the devp2p node identifier.
    Version string `toml:"-"`

    // DataDir is the file system folder the node should use for any data storage
    // requirements. The configured data directory will not be directly shared with
    // registered services, instead those can use utility methods to create/access
    // databases or flat files. This enables ephemeral nodes which can fully reside
    // in memory.
    DataDir string

    // Configuration of peer-to-peer networking.
    P2P p2p.Config

    // KeyStoreDir is the file system folder that contains private keys. The directory can
    // be specified as a relative path, in which case it is resolved relative to the
    // current directory.
    //
    // If KeyStoreDir is empty, the default location is the "keystore" subdirectory of
    // DataDir. If DataDir is unspecified and KeyStoreDir is empty, an ephemeral directory
    // is created by New and destroyed when the node is stopped.
    KeyStoreDir string `toml:",omitempty"`

    // ExternalSigner specifies an external URI for a clef-type signer
    ExternalSigner string `toml:",omitempty"`

    // UseLightweightKDF lowers the memory and CPU requirements of the key store
    // scrypt KDF at the expense of security.
    UseLightweightKDF bool `toml:",omitempty"`

    // InsecureUnlockAllowed allows user to unlock accounts in unsafe http environment.
    InsecureUnlockAllowed bool `toml:",omitempty"`

    // NoUSB disables hardware wallet monitoring and connectivity.
    // Deprecated: USB monitoring is disabled by default and must be enabled explicitly.
    NoUSB bool `toml:",omitempty"`

    // USB enables hardware wallet monitoring and connectivity.
    USB bool `toml:",omitempty"`

    // SmartCardDaemonPath is the path to the smartcard daemon's socket
    SmartCardDaemonPath string `toml:",omitempty"`

    // IPCPath is the requested location to place the IPC endpoint. If the path is
    // a simple file name, it is placed inside the data directory (or on the root
    // pipe path on Windows), whereas if it's a resolvable path name (absolute or
    // relative), then that specific path is enforced. An empty path disables IPC.
    IPCPath string

    // HTTPHost is the host interface on which to start the HTTP RPC server. If this
    // field is empty, no HTTP API endpoint will be started.
    HTTPHost string

    // HTTPPort is the TCP port number on which to start the HTTP RPC server. The
    // default zero value is/ valid and will pick a port number randomly (useful
    // for ephemeral nodes).
    HTTPPort int `toml:",omitempty"`

    // HTTPCors is the Cross-Origin Resource Sharing header to send to requesting
    // clients. Please be aware that CORS is a browser enforced security, it's fully
    // useless for custom HTTP clients.
    HTTPCors []string `toml:",omitempty"`

    // HTTPVirtualHosts is the list of virtual hostnames which are allowed on incoming requests.
    // This is by default {'localhost'}. Using this prevents attacks like
    // DNS rebinding, which bypasses SOP by simply masquerading as being within the same
    // origin. These attacks do not utilize CORS, since they are not cross-domain.
    // By explicitly checking the Host-header, the server will not allow requests
    // made against the server with a malicious host domain.
    // Requests using ip address directly are not affected
    HTTPVirtualHosts []string `toml:",omitempty"`

    // HTTPModules is a list of API modules to expose via the HTTP RPC interface.
    // If the module list is empty, all RPC API endpoints designated public will be
    // exposed.
    HTTPModules []string

    // HTTPTimeouts allows for customization of the timeout values used by the HTTP RPC
    // interface.
    HTTPTimeouts rpc.HTTPTimeouts

    // HTTPPathPrefix specifies a path prefix on which http-rpc is to be served.
    HTTPPathPrefix string `toml:",omitempty"`

    // WSHost is the host interface on which to start the websocket RPC server. If
    // this field is empty, no websocket API endpoint will be started.
    WSHost string

    // WSPort is the TCP port number on which to start the websocket RPC server. The
    // default zero value is/ valid and will pick a port number randomly (useful for
    // ephemeral nodes).
    WSPort int `toml:",omitempty"`

    // WSPathPrefix specifies a path prefix on which ws-rpc is to be served.
    WSPathPrefix string `toml:",omitempty"`

    // WSOrigins is the list of domain to accept websocket requests from. Please be
    // aware that the server can only act upon the HTTP request the client sends and
    // cannot verify the validity of the request header.
    WSOrigins []string `toml:",omitempty"`

    // WSModules is a list of API modules to expose via the websocket RPC interface.
    // If the module list is empty, all RPC API endpoints designated public will be
    // exposed.
    WSModules []string

    // WSExposeAll exposes all API modules via the WebSocket RPC interface rather
    // than just the public ones.
    //
    // *WARNING* Only set this if the node is running in a trusted network, exposing
    // private APIs to untrusted users is a major security risk.
    WSExposeAll bool `toml:",omitempty"`

    // GraphQLCors is the Cross-Origin Resource Sharing header to send to requesting
    // clients. Please be aware that CORS is a browser enforced security, it's fully
    // useless for custom HTTP clients.
    GraphQLCors []string `toml:",omitempty"`

    // GraphQLVirtualHosts is the list of virtual hostnames which are allowed on incoming requests.
    // This is by default {'localhost'}. Using this prevents attacks like
    // DNS rebinding, which bypasses SOP by simply masquerading as being within the same
    // origin. These attacks do not utilize CORS, since they are not cross-domain.
    // By explicitly checking the Host-header, the server will not allow requests
    // made against the server with a malicious host domain.
    // Requests using ip address directly are not affected
    GraphQLVirtualHosts []string `toml:",omitempty"`

    // Logger is a custom logger to use with the p2p.Server.
    Logger log.Logger `toml:",omitempty"`

    staticNodesWarning     bool
    trustedNodesWarning    bool
    oldGethResourceWarning bool

    // AllowUnprotectedTxs allows non EIP-155 protected transactions to be send over RPC.
    AllowUnprotectedTxs bool `toml:",omitempty"`
}
```

discover

```go
// Config holds settings for the discovery listener.
type Config struct {
    // These settings are required and configure the UDP listener:
    PrivateKey *ecdsa.PrivateKey

    // These settings are optional:
    NetRestrict  *netutil.Netlist   // list of allowed IP networks
    Bootnodes    []*enode.Node      // list of bootstrap nodes
    Unhandled    chan<- ReadPacket  // unhandled packets are sent on this channel
    Log          log.Logger         // if set, log messages go here
    ValidSchemes enr.IdentityScheme // allowed identity schemes
    Clock        mclock.Clock
}
```

dnsdisc

```go
// Config holds configuration options for the client.
type Config struct {
    Timeout         time.Duration      // timeout used for DNS lookups (default 5s)
    RecheckInterval time.Duration      // time between tree root update checks (default 30min)
    CacheLimit      int                // maximum number of cached records (default 1000)
    RateLimit       float64            // maximum DNS requests / second (default 3)
    ValidSchemes    enr.IdentityScheme // acceptable ENR identity schemes (default enode.ValidSchemes)
    Resolver        Resolver           // the DNS resolver to use (defaults to system DNS)
    Logger          log.Logger         // destination of client log messages (defaults to root logger)
}
```

p2p

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

trie

```go
// Config defines all necessary options for database.
type Config struct {
    Cache     int    // Memory allowance (MB) to use for caching trie nodes in memory
    Journal   string // Journal of clean cache to survive node restarts
    Preimages bool   // Flag whether the preimage of trie key is recorded
}
```



