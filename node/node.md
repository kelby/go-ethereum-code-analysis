区块链概念上的“服务节点”。

```go
// Node is a container on which services can be registered.
type Node struct {
	eventmux      *event.TypeMux
	config        *Config
	accman        *accounts.Manager
	log           log.Logger
	keyDir        string            // key store directory
	keyDirTemp    bool              // If true, key directory will be removed by Stop
	dirLock       fileutil.Releaser // prevents concurrent use of instance directory
	stop          chan struct{}     // Channel to wait for termination notifications
	server        *p2p.Server       // Currently running P2P networking layer
	startStopLock sync.Mutex        // Start/Stop are protected by an additional lock
	state         int               // Tracks state of node lifecycle

	lock          sync.Mutex
	lifecycles    []Lifecycle // All registered backends, services, and auxiliary services that have a lifecycle
	rpcAPIs       []rpc.API   // List of APIs currently provided by the node
	http          *httpServer //
	ws            *httpServer //
	ipc           *ipcServer  // Stores information about the ipc http server
	inprocHandler *rpc.Server // In-process RPC request handler to process the API requests

	databases map[*closeTrackingDB]struct{} // All open databases
}
```

常见用法：

```go
stack *node.Node
```

创建结点 makeFullNode

* 创建结点 makeConfigNode\(\)
* 注册eth Service utils.RegisterEthService\(\)

启动结点 startNode\(\)

* 创建P2P server
* 创建Service
* 启动P2P server: running.Start\(\)
* 启动Service: service.Start\(running\)
* 启动RPC server: n.startRPC

结点进入等待状态 Wait\(\)

