区块链概念上的“服务节点”，有配置项：

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
    NoUSB bool `toml:",omitempty"`

    // DirectBroadcast enable directly broadcast mined block to all peers
    DirectBroadcast bool `toml:",omitempty"`

    // RangeLimit enable 5000 blocks limit when handle range query
    RangeLimit bool `toml:",omitempty"`

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

    LogConfig *LogConfig `toml:",omitempty"`

    staticNodesWarning     bool
    trustedNodesWarning    bool
    oldGethResourceWarning bool

    // AllowUnprotectedTxs allows non EIP-155 protected transactions to be send over RPC.
    AllowUnprotectedTxs bool `toml:",omitempty"`
}
```



