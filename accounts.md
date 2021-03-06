实现了一个高等级的以太坊账户管理。约等同于钱包管理！

重要数据结构：Backend 和 Wallet，重中之中钱包的签名功能。

* url - URL格式账号，转换、解析
* sort - 实现 sort 相关接口，方便排序
* manager - 管理账号、钱包、后端
* hd - hierarchical deterministic 按照一定格式展示账号
* accounts - 账号（地址和或URL）、钱包（实现指定接口）、后端（指定钱包，及订阅通道）
* usbwallet - USB 硬件钱包
* scwallet - smartcard 智能卡钱包
* keystore - 工具，密钥管理
* abi - 工具，ABI 管理

accounts 模块是以太坊的账户管理模块，其实也是一个命令行的钱包。它可以使用本地文件存储加密的私钥信息；也可以连接硬件钱包实现对私钥的创建与管理。

在 accounts 中总共支持两大类共4种钱包类型。

三大类包括 keystore 和 usbwallet 以及 smartcard；其中keystore中的私钥存储可以分为加密的和不加密的；usbwallet支持ledger和trenzer两种硬件钱包。

keystore类型的钱包其实是一个本地文件夹目录。在这个目录下可以存放多个文件，每个文件都存储着一个私钥信息。这些文件都是json格式

keystore目录和目录内的文件是可以直接拷贝的。

我们首先解释一下HD（Hierarchical Deterministic）的概念。这个概念的中文名称叫做“分层确定性”，我的理解是这是一种key的派生方式，它可以在只使用一个公钥（我们称这个公钥为主公钥，其对应的私钥称为主私钥）的情况下，生成任意多个子公钥，而这些子公钥都是可以被主私钥控制的。HD的概念最早是从比特币的BIP-32提案中提出来的。

* 账号 Account
* 钱包 Wallet
* 管理 Manager，本质也是实现 Backend 接口，只是它与设备无关，可以通用

wallet、account、address这三者的区别和联系

i：wallet中可能包含多个account，而每个account中包含一个address和账户所在路径（URL）。

对账号的操作，实质是对 keystore, scwallet, usbwallet 等软硬件存储设备进行操作。

当前支持三大类设备，除最见的文件存储外，用到第三方库驱动：

```go
"github.com/status-im/keycard-go/derivationpath" //scwallet

"github.com/karalabe/usb" //usbwallet
```

> keystore, account, manager 这些属于区块链核心内容之一；
>
> url, abi, hd 等属于相对比较独立的工具库；
>
> scwallet, usbwallet 属于钱包客户端范畴。
>
> 总体来说，也可以理解为它是区块链核心与钱包客户端的连接处。

所谓账户，其实对外展现形式就是地址Address，它很简单。核心是钱包Wallet，而钱包与设备相关，可以有不同实现。

这里，关键是定义 Wallet 和 Backend 接口，当前共有 5 套实现！

