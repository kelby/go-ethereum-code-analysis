通过geth console或者geth attach与节点交互的时候，输入的命令是如何被处理的呢？

* 命令行编辑器Liner等待用户输入命令

* JSRE使用一个名为scheduler的通道（chan）接收命令

* JSRE把命令发送给Javascript解释器Otto处理

* Otto中预加载了web3.js，执行对应的函数并通过provider发送RPC请求

* Web3 provider被设置为一个Bridge模块，接收请求并转发给RCP Client

* RPC Client通过全双工管道和RPC Server通信，完成RPC调用

* 将RPC调用结果输出到命令行

涉及到很多模块。这些模块都被包含在Console的数据结构之中：

```go
// Console is a JavaScript interpreted runtime environment. It is a fully fledged
// JavaScript console attached to a running node via an external or in-process RPC
// client.
type Console struct {
    client   *rpc.Client         // RPC client to execute Ethereum requests through
    jsre     *jsre.JSRE          // JavaScript runtime environment running the interpreter
    prompt   string              // Input prompt prefix string
    prompter prompt.UserPrompter // Input prompter to allow interactive user feedback
    histPath string              // Absolute path to the console scrollback history
    history  []string            // Scroll history maintained by the console
    printer  io.Writer           // Output writer to serialize any display strings to
}
```

Liner：带历史记录的命令行编辑器

Otto：JavaScript解释器

JSRE：实现事件循环

web3.js和bridge

RPC Client

RPC Server

把所有知识串联到一起

```
// Console is a JavaScript interpreted runtime environment. It is a fully fledged
// JavaScript console attached to a running node via an external or in-process RPC
// client.
```

> 客户端/API，与区块链核心的连接。
>
> jsre.JSRE - 通向客户端；
>
> rpc.Client - 通向区块链核心。

主要模块有：

```
# 主要包含对区块链进行访问和交互相关的方法
eth

# 主要包含查看p2p 网络状态的方法
net

# 主要包含与管理节点相关的方法
admin

# 主要包含挖矿相关的一些方法
miner

# 包含账户管理的方法
personal

# 包含查看交易内存池的方法
txpool

# 包含以上所有对象,还包含一些通用方法
web3
```

常用命令有:

```
# 创建账户
personal.newAccount()

# 解锁账户
personal.unlockAccount()
# 解锁账户，指定解锁具体账户
personal.unlockAccount(eth.accounts[0])

# 列出系统中的账户
eth.accounts

# 1、查看账户余额,返回值的单位是 Wei (“ ”里面是自己管理的账户地址)
eth.getBalance()
# 2、查看账户余额,返回值的单位是 Wei (“ ”里面是自己管理的账户地址)
eth.getBalance("写上账户地址")
# 3、转换为单位ether,便于阅读
web3.fromWei(eth.getBalance("写上账户地址"),'ether')
# 4、如果是在里面创建的账户可以调用内部函数拿到地址，不用每次都复制地址。
eth.getBalance(eth.accounts[0])
web3.fromWei(eth.getBalance(eth.accounts[0]),'ether')

#发起交易（发起方需要是自己管理的账户，其次需要先解锁账户），from：发起交易的地址；to:接受交易的地址
eth.sendTransaction({from:eth.accounts[0],to:"接受交易的地址",value:100000})

# 列出当前区块高度
eth.blockNumber

# 获取交易信息
eth.getTransaction()
# 获取交易收据
eth.getTransactionReceipt()

# 获取区块信息
eth.getBlock()

# 开始挖矿
miner.start()
# 表示一直挖矿
miner.start(1)
# 查看
eth.coinbase

挖矿状态
eth.mining

# 停止挖矿
miner.stop()
# 开始挖矿,当挖到一个块时就停止，
miner.start(1);admin.sleepBlocks(1);miner.stop()

# Wei 换算成以太币
web3.fromWei()

# 以太币换算成 Wei
web3.toWei()

# 交易池的状态
txpool.status
```



