一、ABI编码请求参数

包括方法名，入参。输入参数

二、构造交易对象

rawTx

三、交易签名

types.SignTx

* 计算rlpHash
* 私钥签名
* 填充交易对象中的V,R,S字段

四、发送交易

ethClient.SendTransaction

数据流转的过程包括：

* 合约方法及参数进行ABI编码
* 构造Transaction交易对象
* 交易对象RLP编码
* 对编码后交易数据使用私钥进行椭圆曲线签名得到签名串
* 根据签名串生成签名后交易对象
* 对签名后的交易对象进行RLP编码得到签名后的交易数据

从原始数据到签名数据，核心的技术点包括：

* ABI编码
* 交易信息rpl编码
* 椭圆曲线secp256k1签名
* 根据签名结果计算V,R,S

由 Tx 生成签名的具体流程如下图：

![](/assets/tx-sign-process.png)

