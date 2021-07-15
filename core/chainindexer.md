chain\_indexer 顾名思义， 就是用来给区块链创建索引的功能。 

BloomIndexer是chain\_indexer的一个特殊的实现， 可以理解为派生类， 主要的功能其实是在chain\_indexer这里面实现的。虽说是派生类，但是chain\_indexer其实就只被BloomIndexer使用。也就是给区块链的布隆过滤器创建了索引，以便快速的响应用户的日志搜索功能。

