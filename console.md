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



