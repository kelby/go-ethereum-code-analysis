实现了一个高等级的以太坊账户管理。

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

在 accounts 中总共支持两大类共4种钱包类型。两大类包括keystore和usbwallet；其中keystore中的私钥存储可以分为加密的和不加密的；usbwallet支持ledger和trenzer两种硬件钱包。

keystore类型的钱包其实是一个本地文件夹目录。在这个目录下可以存放多个文件，每个文件都存储着一个私钥信息。这些文件都是json格式

