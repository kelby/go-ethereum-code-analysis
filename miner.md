Miner - 矿工。

Clique - 共识方式一

Ethash - 共识方式二

unconfirmed - 用来跟踪用户本地的挖矿信息的

worker 内部包含了很多agent，同时负责构建区块和对象。

关键作用一：

执行交易

如果结点不挖矿的话，这里会立即调用commitTransactions\(\)提交给EVM执行，获得本地回执；

如果结点挖矿的话，miner会调用commitNewWork\(\)，内部也会调用commitTransactions\(\)执行交易。

* Miner启动打包 - 在eth Service初始化的时候，会创建一个Miner实例
  * 创建Miner实例 - 调用newWorker创建一个worker实例，Miner只是一个发起人，真正干活的是worker
  * 注册Agent - 当worker产生新的Work时会通过这个接口发送给Agent；当Agent这边完成POW计算后，会通过这个channel把Result发送给worker
  * 等待区块同步完成 - 需要等待和其他结点之间完成区块同步，这样才能在最新的状态挖矿
* worker打包区块，发送Work给Agent
  * 创建新区块头 - 获取当前区块，然后创建一个Header结构
  * 初始化共识引擎 - 使用基于POW算法的Ethash共识引擎
  * 创建新Work
  * 执行交易 - 把交易按照gas price进行排序，调用commitTransactions\(\)把交易提交到EVM去执行
  * 处理叔块
  * 打包新区块 - 计算报酬，生成MPT根，生成新区块
  * 向Agent推送Work
  * 更新快照
* Agent完成POW计算后返回Result
* worker存储新区块，启动下一次打包
  * 修改Log中的区块hash值
  * 将区块和状态信息写入数据库
  * 发送NewMinedBlockEvent事件
  * 发送ChainEvent事件
  * 启动下一次打包

Miner 包工头，worker 打工人-半成品，Agent 包装员-成品。Work 产品-半成品，Result 产品-成品。

miner.start\(\)即可进开启实时挖矿。

![](/assets/miner.png)



