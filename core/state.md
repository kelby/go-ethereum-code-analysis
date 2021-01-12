｜-database.go 底层的存储设计。提供了一个数据库的抽象。

｜-dump.go  用来dumpstateDB数据

｜-iterator.go，用来遍历Trie

｜-journal.go，用来记录状态的改变。代表了操作日志， 并针对各种操作的日志提供了对应的回滚功能。 可以基于这个日志来做一些事务类型的操作。

｜-state\_object.go 通过state object操作账户值，并将修改后的storage trie写入数据库。表示正在修改的以太坊帐户。

｜-statedb.go，以太坊整个的状态。用来存储以太坊中关于merkle trie的所有内容。 StateDB负责缓存和存储嵌套状态。

｜-sync.go，用来和downloader结合起来同步state

core/state 包主要为以太坊的state trie提供了一层缓存层\(cache\)

