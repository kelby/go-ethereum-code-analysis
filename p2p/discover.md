基于UDP的p2p节点发现协议。

type Config - 配置

type Table - 表

type UDPv4

type UDPv5

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

节点第一次启动读取公共种子节点信息,已本节点ID作为target更新本地K桶,然后每隔一段时间进行节点更新, 刷新K桶流程如下:

a. 随机生成目标节点Id，记为TargetId，从1开始记录发现次数和刷新时间。

b. 在当前节点的K桶里查找与目标节点最近的16个节点

c. 向b中得到的每个节点发送findnode命令,接收到每个节点传回的neighbours节点

d. 对c返回的每个节点进行ping-pong测试然后更新到本地k桶

e. 上述流程均是基于UDP的发现流程,p2p网络会定时随机取k桶中未连接的节点进行TCP连接,在连接好的TCP通道进行通信\(tcp连接协程里会自己做心跳维护这个连接\)

监听UDP

* 新建路由表
* 测试邻居节点连通性
* 检测各类信息
* 处理UDP数据包

以太坊的k桶设置：

```go
const (
	alpha           = 3  // Kademlia并发参数, 是系统内一个优化参数,控制每次从K桶最多取出节点个数,ethereum取值3
  
	bucketSize      = 16 // K桶大小(可容纳节点数)
  
	maxReplacements = 10 // 每桶更换列表的大小
	hashBits          = len(common.Hash{}) * 8 //每个节点ID长度,32*8=256, 32位16进制
	nBuckets          = hashBits / 15       //  K桶个数
  ）
```



