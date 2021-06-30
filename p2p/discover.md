基于UDP的p2p节点发现协议。

以太坊Kad网总共定义了4种消息类型:

```go
const (
    pingPacket = iota + 1 // ping操作
    pongPacket            // pong操作

    findnodePacket        // find node节点查询
    neighborsPacket       // neighbors邻居回应
)
```

ping和pong是一对操作,用于检测节点活性,节点收到ping消息后立即回复pong响应:

findnode和neighbors是一对操作.

findnode用于查找与某节点相距最近的节点,查找到后以neighbors类型消息回复查找发起者

```
server.Start() ==> discover.ListenUDP ==> newUDP()

refreshLoop
loop
readLoop
```



