区块链概念上的“服务节点”。

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

