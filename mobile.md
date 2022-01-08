simplified mobile APIs

给移动端 App 使用的 API，可以通过 gomobile 编译。由于有数据类型，以及操作系统限制，功能比较简单。

* 账号\(Account/Accounts\)和地址\(Address/Addresses\)
* 区块（主要是获取区块数据）
* 合约、收据（绑定、部署、调用，获取信息）
* 合约调用（Msg 信息获取）
* 以太坊相关、区块头等（全节点，发送交易、获取链上数据等）
* 过滤条件
* 存储
* 节点网络信息（节点信息，开始、停止）
* 交易

可以看到，除了密码学外，其它几个部分网络、存储、钱包、区块链核心都涉及到了。

* 网络，获取网络信息 - Enodes，NodeInfo，PeerInfo
* 存储，数据存储在本地
* 钱包，钱包、账号等信息及交互 - EthereumClient，Account，Accounts，KeyStore
* 核心，区块、交易、合约等信息及交互 - Node，Log，Nonce，Header，Block，Transaction，Receipt

> 属于API客户端连接库。

## 区块链核心

accounts.go

> "github.com/ethereum/go-ethereum/accounts"
>
> "github.com/ethereum/go-ethereum/accounts/keystore"

ethereum.go

> "github.com/ethereum/go-ethereum"

geth.go

> "github.com/ethereum/go-ethereum/node"

params.go

> "github.com/ethereum/go-ethereum/core"

types.go

> "github.com/ethereum/go-ethereum/core/types"

## 密码学

## 钱包、API、客户端

ethclient.go

> "github.com/ethereum/go-ethereum/ethclient"

## P2P 网络

p2p.go

> "github.com/ethereum/go-ethereum/p2p"

discover.go

> "github.com/ethereum/go-ethereum/p2p/enode"



## Key-Value 存储

## 行业标准库

bind.go

> "github.com/ethereum/go-ethereum/accounts/abi/bind"
>
> "github.com/ethereum/go-ethereum/core/types"

vm.go

> "github.com/ethereum/go-ethereum/core/types"



## 工具库





common.go

> "github.com/ethereum/go-ethereum/common"











