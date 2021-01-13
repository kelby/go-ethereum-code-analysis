以太坊数据存储，**关键是定义接口**，具体使用哪个数据库，自己封装实现。

以太坊的存储数据结构为MPT（Merkle Patricia Tries），一切数据都为键值对（KeyValue）。为了存储（序列化）这些健值对，以太坊采用LevelDB为原型来定制自己的数据库ethdb。





