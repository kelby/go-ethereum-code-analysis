｜-database.go 底层的存储设计

｜-dump.go  用来dumpstateDB数据

｜-iterator.go，用来遍历Trie

｜-journal.go，用来记录状态的改变

｜-state\_object.go 通过state object操作账户值，并将修改后的storage trie写入数据库

｜-statedb.go，以太坊整个的状态

｜-sync.go，用来和downloader结合起来同步state

core/state 包主要为以太坊的state trie提供了一层缓存层\(cache\)

