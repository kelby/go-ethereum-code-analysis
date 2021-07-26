​ 按照以太坊架构设计，交易的执行可大致分为内外两层结构：第一层是虚拟机外，包括执行前将 Transaction 类型转化成 Message，创建虚拟机\(EVM\)对象，计算一些 Gas 消耗，以及执行交易完毕后创建收据\(Receipt\)对象并返回等；第二层是虚拟机内，包括执行 转帐，和创建合约并执行合约的指令数组。

虚拟机外：



执行 tx 的入口函数是 Process\(\)函数，在 core/state\_processor.go 中。







​ Process\(\)函数的核心是一个 for 循环，它将 Block 里的所有 tx 逐个遍历执行。具体的执行函数为同个 go 文件中的 ApplyTransaction\(\)函数，它每次执行 tx, 会返回一个收据\(Receipt\)对象。Receipt 结构体的声明如下（core/types/receipt.go）：







​ Receipt 中有一个 Log 类型的数组，其中每一个 Log 对象记录了 Tx 中一小步的操作。所以，每一个 tx 的执行结果，由一个 Receipt 对象来表示；更详细的内容，由一组 Log 对象来记录。这个 Log 数组很重要，比如在不同 Ethereum 节点\(Node\)的相互同步过程中， 待同步区块的 Log 数组有助于验证同步中收到的 block 是否正确和完整，所以会被单独同步\(传输\)。



Receipt 的 PostState 保存了创建该 Receipt 对象时，整个 Block 内所有“帐户”的当时状态。Ethereum 里用 stateObject 来表示一个账户 Account，这个账户可转帐\(transfer value\), 可执行 tx, 它的唯一标示符是一个 Address 类型变量。 这个 Receipt.PostState 就是当时所在 Block 里所有 stateObject 对象的 RLP Hash 值。



Bloom 类型是一个 Ethereum 内部实现的一个 256bit 长 Bloom Filter。它可用来快速验证一个新收到的对象是否处于一个已知的大量对象集合之中。这里 Receipt 的 Bloom， 被用以验证某个给定的 Log 是否处于 Receipt 已有的 Log 数组中。

我们来看下 StateProcessor.ApplyTransaction\(\)的具体实现，它的基本流程如下图：







​ ApplyTransaction\(\)首先根据输入参数分别封装出一个 Message 对象和一个 EVM 对象，然后加上一个传入的 GasPool 类型变量，执行 core/state\_transition.go 中的ApplyMessage\(\)，而这个函数又调用同 go 文件中 TransitionDb\(\)函数完成 tx 的执行，待TransitionDb\(\)返回之后，创建一个收据 Receipt 对象，最后返回该 Recetip 对象，以及整个tx 执行过程所消耗 Gas 数量。



GasPool 对象是在一个 Block 执行开始时创建，并在该 Block 内所有 tx 的执行过程中共享，对于一个 tx 的执行可视为“全局”存储对象； Message 由此次待执行的 tx 对象转化而来，并携带了解析出的 tx 的\(转帐\)转出方地址，属于待处理的数据对象；EVM 作为Ethereum 世界里的虚拟机\(Virtual Machine\)，作为此次 tx 的实际执行者，完成转帐和合约\(Contract\)的相关操作。



我们来细看下 TransitioinDb\(\)的执行过程\(/core/state\_transition.go\)。假设有StateTransition 对象 st, 其成员变量 initialGas 表示初始可用 Gas 数量，gas 表示即时可用Gas 数量，初始值均为 0，于是 st.TransitionDb\(\) 可由以下步骤展开：





首先执行 preCheck\(\)函数，检查：1.交易中的 nonce 和账户 nonce 是否为同一个。2. 检查 gas 值是否合适（&lt;=64 \)



购买 Gas。首先从交易的\(转帐\)转出方账户扣除一笔 Ether，费用等于tx.data.GasLimit \* tx.data.Price； 同 时 st.initialGas = st.gas = tx.data.GasLimit； 然 后\(GasPool\) gp –= st.gas 。

计算 tx 的固有 Gas 消耗 – intrinsicGas。它分为两个部分，每一个 tx 预设的消耗量，这个消耗量还因 tx 是否含有\(转帐\)转入方地址而略有不同；以及针对tx.data.Payload 的 Gas 消耗，Payload 类型是\[\]byte，关于它的固有消耗依赖于\[\]byte 中非 0 字节和 0 字节的长度。最终，st.gas –= intrinsicGas

EVM 执行。如果交易的\(转帐\)转入方地址\(tx.data.Recipient\)为空，即contractCreation，调用 EVM 的 Create\(\)函数；否则，调用 Call\(\)函数。无论哪个函数返回后，更新 st.gas。

计算本次执行交易的实际 Gas 消耗： requiredGas = st.initialGas – st.gas

偿退 Gas。它包括两个部分：首先将剩余 st.gas 折算成 Ether，归还给交易的\(转帐\)转出方账户；然后，基于实际消耗量 requiredGas，系统提供一定的补偿，数量为 refundGas。refundGas 所折算的 Ether 会被立即加在\(转帐\)转出方账户上， 同时 st.gas += refundGas，gp += st.gas，即剩余的 Gas 加上系统补偿的 Gas，被一起归并进 GasPool，供之后的交易执行使用。

奖励所属区块的挖掘者：系统给所属区块的作者，亦即挖掘者账户，增加一笔金额，数额等于 st.data,Price \* \(st.initialGas – st.gas\)。注意，这里的 st.gas 在步骤 5 中被加上了 refundGas, 所以这笔奖励金所对应的 Gas，其数量小于该交易实际消耗量 requiredGas。

由上可见，除了步骤 3 中 EVM 函数的执行，其他每个步骤都在围绕着 Gas 消耗量作文章。

步骤 5 的偿退机制很有意思，设立它的目的何在？目前为止我只能理解它可以避免交易执行过程中过快消耗 Gas，至于对其全面准确的理解尚需时日。



步骤 6 是奖励机制

Ethereum 中每个交易\(transaction，tx\)对象在被放进 block 时，都是经过数字签名的， 这样可以在后续传输和处理中随时验证 tx 是否经过篡改。Ethereum 采用的数字签名是椭圆曲线数字签名算法\(Elliptic Cure Digital Signature Algorithm,ECDSA\)。ECDSA 相比于基于大质数分解的 RSA 数字签名算法，可以在提供相同安全级别\(in bits\)的同时，仅需更短的公钥\(public key\)。这里需要特别留意的是，tx 的转帐转出方（发送方）地址，就是对该 tx 对象作 ECDSA 签名计算时所用的公钥 publicKey。



Ethereum 中的数字签名计算过程所生成的签名\(signature\), 是一个长度为 65bytes 的字节数组，它被截成三段放进 tx 中，前 32bytes 赋值给成员变量 R, 再 32bytes 赋值给 S，1byte 赋给 V，当然由于 R、S、V 声明的类型都是\*big.Int, 上述赋值存在\[\]byte –&gt; big.Int 的类型转换。







当需要恢复出 tx 对象的转帐转出方地址时\(比如在需要执行该交易时\)，Ethereum 会先从 tx 的 signature 中恢复出公钥，再将公钥转化成一个 common.Address 类型的地址，signature 由 tx 对象的三个成员变量 R,S,V 转化成字节数组\[\]byte 后拼接得到。



Ethereum 对此定义了一个接口 Signer, 用来执行挂载签名，恢复公钥，对 tx 对象做哈希等操作。 接口定义是在：/ core/types/transaction\_signing.go 的：







这个接口主要做的就是恢复发送地址、生成签名格式、生成交易哈希、验证等。

生成数字签名的函数叫 SignTx\(\)，最根源是定义在 core/types/transaction\_signing.go（mobile/accounts.go 中也有 SignTx，但是这个函数是调用 accounts/keystore/keystore.go中的 SignTX，最终又调用 types.SignTx），它会先调用其函数生成 signature, 然后调用tx.WithSignature\(\)将 signature 分段赋值给 tx 的成员变量 R,S,V。



​ Signer 接口中，恢复（提取？）转出方地址的函数为：Sender,Sender returns the address derived from the signature \(V, R, S\) using secp256k1。使用到的参数是：Signer 和 Transaction ，该函数定义在core/types/transaction\_signing.go 中







​ Sender\(\)函数体中，signer.Sender\(\)会从本次数字签名的签名字符串\(signature\)中恢复出公钥，并转化为 tx 的\(转帐\)转出方地址。此函数最终会调用同文件下的 recoverPlain 函数来进行恢复







在上文提到的 ApplyTransaction\(\)实现中，Transaction 对象需要首先被转化成 Message接口，用到的AsMessage\(\)函数即调用了此处的 Sender\(\)。调用路径为： AsMessage-&gt;transaction\_signing.Sender\(两个参数的\)–&gt;sender（单个参数的） 在 Transaction 对象 tx 的转帐转出方地址被解析出以后，tx 就被完全转换成了Message 类型，可以提供给虚拟机 EVM 执行了。





虚拟机内：



​ 每个交易\(Transaction\)带有两部分内容（参数）需要执行：



转帐，由转出方地址向转入方地址转帐一笔以太币 Ether;

携带的\[\]byte 类型成员变量 Payload，其每一个 byte 都对应了一个单独虚拟机指令。这些内容都是由 EVM\(Ethereum Virtual Machine\)对象来完成 的。EVM 结构体是 Ethereum 虚拟机机制的核心，它与协同类的 UML 关系图如下：



​ 其中 Context 结构体分别携带了 Transaction 的信息\(GasPrice, GasLimit\)，Block 的信息\(Number, Difficulty\)，以及转帐函数等，提供给 EVM；StateDB 接口是针对 state.StateDB 结构体设计的本地行为接口，可为 EVM 提供 statedb 的相关操作； Interpreter 结构体作为解释器，用来解释执行 EVM 中合约\(Contract\)的指令\(Code\)。



​ 注意，EVM 中定义的成员变量 Context 和 StateDB, 仅仅声明了变量名而无类型，而变量名同时又是其类型名，在 Golang 中，这种方式意味着宗主结构体可以直接调用该成员变量的所有方法和成员变量，比如 EVM 调用 Context 中的 Transfer\(\)。



交易的转帐操作由 Context 对象中的 TransferFunc 类型函数来实现，类似的函数类型，还有 CanTransferFunc, 和 GetHashFunc。这三个类型的函数变量 CanTransfer, Transfer, GetHash，在 Context 初始化时从外部传入，目前使用的均是一个本地实现。可见目前的转帐函数 Transfer\(\)的逻辑非常简单，转帐的转出账户减掉一笔以太币，转入账户加上一笔以太币。由于 EVM 调用的 Transfer\(\)函数实现完全由 Context 提供，所以，假设如果基于 Ethereum 平台开发，需要设计一种全新的“转帐”模式，那么只需写一个新的 Transfer\(\)函数实现，在 Context 初始化时赋值即可。



有朋友或许会问，这里 Transfer\(\)函数中对转出和转入账户的操作会立即生效么？万一两步操作之间有错误发生怎么办？答案是不会立即生效。StateDB 并不是真正的数据库， 只是一行为类似数据库的结构体。它在内部以 Trie 的数据结构来管理各个基于地址的账 户，可以理解成一个 cache；当该账户的信息有变化时，变化先存储在 Trie 中。仅当整个Block 要被插入到 BlockChain 时，StateDB 里缓存的所有账户的所有改动，才会被真正的提交到底层数据库。





合约的创建和赋值：



合约\(Contract\)是 EVM 用来执行\(虚拟机\)指令的结构体。Contract 的结构定义于：core/vm/contract.go 中，在这些成员变量里，caller 是转帐转出方地址\(账户\)，self 是转入方地址，不过它们的类型都用接口 ContractRef 来表示；Code 是指令数组，其中每一个 byte 都对应于一个预定义的虚拟机指令；CodeHash 是 Code 的 RLP 哈希值；Input 是数据数组，是指令所操作的数据集合；Args 是参数。



​ 有意思的是 self 这个变量，为什么转入方地址要被命名成 self 呢？ Contract 实现了ContractRef 接口，返回的恰恰就是这个 self 地址。



func \(c \*Contract\) Address\(\) common.Address { return c.self.Address\(\)



}



​ 所以当 Contract 对象作为一个 ContractRef 接口出现时，它返回的地址就是它的 self地址。那什么时候 Contract 会被类型转换成 ContractRef 呢？当 Contract A 调用另一个Contract B 时，A 就会作为 B 的 caller 成员变量出现。Contract 可以调用 Contract，这就为系统在业务上的潜在扩展，提供了空间。



创建一个 Contract 对象时，重点关注对 self 的初始化，以及对 Code, CodeAddr 和Input 的赋值。



另外，StateDB 提供方法 SetCode\(\)，可以将指令数组 Code 存储在某个 stateObject 对象中; 方法 GetCode\(\)，可以从某个 stateObject 对象中读取已有的指令数组 Code。

stateObject \(core/state/state\_object.go\)是 Ethereum 里用来管理一个账户所有信息修改的结构体，它以一个 Address 类型变量为唯一标示符。StateDB 在内部用一个巨大的map 结构来管理这些 stateObject 对象。所有账户信息-包括 Ether 余额，指令数组 Code,该账户发起合约次数 nonce 等-它们发生的所有变化，会首先缓存到 StateDB 里的某个stateObject 里，然后在合适的时候，被 StateDB 一起提交到底层数据库。

​ EVM（core/vm/evm.go）中 目前有五个函数可以创建并执行 Contract，按照作用和调用方式，可以分成两类:



​ Create\(\), Call\(\): 二者均在 StateProcessor 的 ApplyTransaction\(\)被调用以执行单个交易，并且都有调用转帐函数完成转帐。

​ CallCode\(\), DelegateCall\(\), StaticCall\(\)：三者由于分别对应于不同的虚拟机指令\(1 byte\)操作，不会用以执行单个交易，也都不能处理转帐。考虑到与执行交易的相关性，这里着重探讨 Create\(\)和 Call\(\)。先来看 Call\(\)，它用来处理\(转帐\)转入方地址不为空的情况：



Call\(\)函数的逻辑可以简单分为以上 6 步。其中步骤\(3\)调用了转帐函数 Transfer\(\)，转入账户 caller, 转出账户 addr；步骤\(4\)创建一个 Contract 对象，并初始化其成员变量 caller, self\(addr\), value 和 gas； 步骤\(5\)赋值 Contract 对象的 Code, CodeHash, CodeAddr 成员变量；步骤\(6\) 调用 run\(\)函数执行该合约的指令，最后 Call\(\)函数返回。

因为此时\(转帐\)转入地址不为空，所以直接将入参 addr 初始化 Contract 对象的 self 地址，并可从 StateDB 中\(其实是以 addr 标识的账户 stateObject 对象\)读取出相关的 Code 和CodeHash 并赋值给 contract 的成员变量。注意，此时转入方地址参数 addr 同时亦被赋值予 contract.CodeAddr。

再来看看 EVM.Create\(\)，它用来处理\(转帐\)转入方地址为空的情况。







与 Call\(\)相比，Create\(\)因为没有 Address 类型的入参 addr，其流程有几处明显不同：



​ 步骤\(3\)中创建一个新地址 contractAddr，作为\(转帐\)转入方地址，亦作为Contract 的 self 地址；

​ 步骤\(6\)由于 contracrAddr 刚刚新建，db 中尚无与该地址相关的 Code 信息， 所以会将类型为\[\]byte 的入参 code，赋值予 Contract 对象的 Code 成员；

​ 步骤\(8\)将本次执行合约的返回结果，作为 contractAddr 所对应账户\(stateObject 对象\)的 Code 储存起来，以备下次调用。

​ 还有一点隐藏的比较深，Call\(\)有一个入参 input 类型为\[\]byte，而 Create\(\)有一个入参code 类型同样为\[\]byte，没有入参 input，它们之间有无关系？其实，它们来源都是Transaction 对象 tx 的成员变量 Payload！调用 EVM.Create\(\)或 Call\(\)的入口在StateTransition.TransitionDb\(\)中，当 tx.Recipent 为空时，tx.data.Payload 被当作所创建Contract 的 Code；当 tx.Recipient 不为空时，tx.data.Payload 被当作 Contract 的 Input。



预编译合约



​ EVM 中执行合约\(指令\)的函数是 run\(\)，在 core/vm/evm.go 中其实现代码如下： 可见如果待执行的 Contract 对象恰好属于一组预编译的合约集合-此时以指令地址CodeAddr 为匹配项-那么它可以直接运行；没有经过预编译的 Contract，才会由Interpreter 解释执行。这里的”预编译”，可理解为不需要编译\(解释\)指令\(Code\)。预编译的合约，其逻辑全部固定且已知，所以执行中不再需要 Code，仅需 Input 即可。



在代码实现中，预编译合约只需实现两个方法 Required\(\)和 Run\(\)即可，这两方法仅需一个入参 input。

目前，Ethereuem 代码中已经加入了多个预编译合约，功能覆盖了包括椭圆曲线密钥恢复，SHA-3\(256bits\)哈希算法，RIPEMD-160 加密算法等等。相信基于自身业务的需求，二次开发者完全可以加入自己的预编译合约，大大加快合约的执行速度。

