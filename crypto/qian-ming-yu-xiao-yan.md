签名算法 secp256k1

![](/assets/crypto-sign-verify.png)以太坊的签名格式是r+s+v。 r 和 s 是ECDSA签名的原始输出，而末尾的一个字节为 recovery id 值，但在以太坊中用V表示，v 值为1或者0。recovery id 简称 recid，表示从内容和签名中成功恢复出公钥时需要查找的次数（因为根据r值在椭圆曲线中查找符合要求的坐标点可能有多个）

![](/assets/eth-sign-example.png)交易数据签名

![](/assets/eth-tx-sign-example.png)



