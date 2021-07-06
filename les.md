LES： Light Ethereum Subprotocol，以太坊客户端的轻量级的子协议，只需要下载区块头，其他详细信息可以按需获取

以太坊中关于区块同步和交换的代码位于eth目录下和les目录下。其中eth实现了所有的相关逻辑，而les只是light同步模式下的实现。这句话来源参考 RegisterEthService

当配置文件中的SyncMode字段为downloader.LightSync时，会使用les目录下的代码；否则使用eth目录下的代码。

LES 协议处理流程：

![](/assets/les-protocol-process.png)

