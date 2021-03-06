RLPx Encryption\(RLPx加密\)

每一个节点会开启两个同样的端口，一个是UDP端口，用来节点发现，一个是TCP端口，用来承载业务数据。 UDP的端口和TCP的端口的端口号是同样的。 这样只要通过UDP发现了端口，就等于可以用TCP来连接到对应的端口。

RLPx协议就定义了TCP链接的加密过程。

RLPx使用了\(Perfect Forward Secrecy\), 简单来说。 链接的两方生成生成随机的私钥，通过随机的私钥得到公钥。 然后双方交换各自的公钥， 这样双方都可以通过自己随机的私钥和对方的公钥来生成一个同样的共享密钥\(shared-secret\)。后续的通讯使用这个共享密钥作为对称加密算法的密钥。 这样来说。如果有一天一方的私钥被泄露，也只会影响泄露之后的消息的安全性， 对于之前的通讯是安全的\(因为通讯的密钥是随机生成的，用完后就消失了\)。

![](/assets/ethereum-p2pnet-rlpx.png)

在底层的golang的网络通信类库Tcp socket接口与 上层的核心协议之间， 存在一个中间层

被称之为transport， 这一层的主要工作就是对于上层消息的编解码/加解密从而使得通信高效而安全。

在thereum 中 这一层的实现是通过rlpx 协议来完成。

Rlpx 协议详解

rlpx 协议之中又可以分为3个层次：

1. 首先是对上层协议的原始消息的编解码.编解码的协议是RLP。

主要目的是使得数据类型在传输中编解码的结果与平台无关.

1. 其次是针对RLP编解码的结果进行压缩或者解压缩。这里采用的是snappy压缩算

2. ECIES\(Elliptic Curve Integrated Encryption Scheme\) 加/解密算法

对于压缩后的数据进行加密

或者对于网络栈上传的数据进行解密而后解压缩。

ECIES 算法是一种基于椭圆曲线的集成了密钥交换+对称加密+消息验证码的一套算法体系。

握手流程：

RLPx连接基于TCP通信，并且每次通信都会生成随机的临时密钥用于加密和验证。生成临时密钥的过程被称作“握手” \(handshake\)，握手在发起端（initiator, 发起TCP连接请求的节点）和接收端（recipient, 接受连接的节点）之间进行。

1. 发起端向接收端发起TCP连接，发送auth消息
2. 接收端接受连接，解密、验证auth消息（检查recovery of signature == keccak256\(ephemeral-pubk\)）
3. 接收端通过remote-ephemeral-pubk 和 nonce生成auth-ack消息
4. 接收端推导密钥，发送首个包含Hello消息的数据帧 \(frame\)
5. 发起端接收到auth-ack消息，导出密钥
6. 发起端发送首个加密后的数据帧，包含发起端Hello消息
7. 接收端接收并验证首个加密后的数据帧
8. 发起端接收并验证首个加密后的数据帧
9. 如果两边的首个加密数据帧的MAC都验证通过，则加密握手完成

如果首个数据帧的验证失败，则任意一方都可以断开连接。主动握手部分源码initiatorEncHandshake

握手双方使用到的信息有:各自的公私钥地址对\(iPrv,iPub,rPrv,rPub\)、各自生成的随机公私钥对\(iRandPrv,iRandPub,rRandPrv,rRandPub\)、各自生成的临时随机数\(initNonce,respNonce\).

其中i开头的表示发起方\(initiator\)信息,r开头的表示接收方\(receiver\)信息.

密钥的交换和共享密钥的生成 doEncHandshake\(\) 



