币安智能链在用的共识算法 PoSA，可供参考。

| 合约名字 | 合约地址 | ABI 文件 |
| :--- | :--- | :--- |
| ValidatorContract | 0x0000000000000000000000000000000000001000 | 略 |
| SlashContract | 0x0000000000000000000000000000000000001001 | 略 |
| SystemRewardContract | 0x0000000000000000000000000000000000001002 | 略 |
| LightClientContract | 0x0000000000000000000000000000000000001003 | 略 |
| TokenHubContract | 0x0000000000000000000000000000000000001004 | 略 |
| RelayerIncentivizeContract | 0x0000000000000000000000000000000000001005 | 略 |
| RelayerHubContract | 0x0000000000000000000000000000000000001006 | 略 |
| GovHubContract | 0x0000000000000000000000000000000000001007 | 略 |
| TokenManagerContract | 0x0000000000000000000000000000000000001008 | 略 |
| CrossChainContract | 0x0000000000000000000000000000000000002000 | 略 |

当前共升级过 3 个版本：

* ramanujanUpgrade
* nielsUpgrade
* mirrorUpgrade

BSC 网络代号（由创世纪决定）：

* BSCGenesisHash - mainNet
* ChapelGenesisHash - chapelNet 新测试网
* RialtoGenesisHash - rialtoNet 旧测试网
* Default - 默认网络（用不到）

ETH 网络代号：

* mainNet
* Ropsten
* Rinkeby
* Görli
* Calaveras（YOLOv3x）

BSC 几次升级代号：

* Ramanujan
* Niels
* Mirror

miner/worker.go

params/config.go

core/state\_transition.go

