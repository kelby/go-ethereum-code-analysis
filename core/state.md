｜-database.go 底层的存储设计。提供了一个数据库的抽象。

｜-dump.go  用来dumpstateDB数据

｜-iterator.go，用来遍历Trie

｜-journal.go，用来记录状态的改变。代表了操作日志， 并针对各种操作的日志提供了对应的回滚功能。 可以基于这个日志来做一些事务类型的操作。

｜-state\_object.go 通过state object操作账户值，并将修改后的storage trie写入数据库。表示正在修改的以太坊帐户。

｜-statedb.go，以太坊整个的状态。用来存储以太坊中关于merkle trie的所有内容。 StateDB负责缓存和存储嵌套状态。

｜-sync.go，用来和downloader结合起来同步state

core/state 包主要为以太坊的state trie提供了一层缓存层\(cache\)

重要数据结构：

* StateDB

Account

```go
// Account is the Ethereum consensus representation of accounts.
// These objects are stored in the main account trie.
type Account struct {
    Nonce    uint64
    Balance  *big.Int
    Root     common.Hash // merkle root of the storage trie
    CodeHash []byte
}
```

stateObject

```go
// stateObject represents an Ethereum account which is being modified.
//
// The usage pattern is as follows:
// First you need to obtain a state object.
// Account values can be accessed and modified through the object.
// Finally, call CommitTrie to write the modified storage trie into a database.
type stateObject struct {
    address  common.Address
    addrHash common.Hash // hash of ethereum address of the account
    data     Account
    db       *StateDB

    // DB error.
    // State objects are used by the consensus core and VM which are
    // unable to deal with database-level errors. Any error that occurs
    // during a database read is memoized here and will eventually be returned
    // by StateDB.Commit.
    dbErr error

    // Write caches.
    trie Trie // storage trie, which becomes non-nil on first access
    code Code // contract bytecode, which gets set when code is loaded

    originStorage  Storage // Storage cache of original entries to dedup rewrites, reset for every transaction
    pendingStorage Storage // Storage entries that need to be flushed to disk, at the end of an entire block
    dirtyStorage   Storage // Storage entries that have been modified in the current transaction execution
    fakeStorage    Storage // Fake storage which constructed by caller for debugging purpose.

    // Cache flags.
    // When an object is marked suicided it will be delete from the trie
    // during the "update" phase of the state transition.
    dirtyCode bool // true if the code was updated
    suicided  bool
    deleted   bool
}
```

StateDB

```go
// StateDB structs within the ethereum protocol are used to store anything
// within the merkle trie. StateDBs take care of caching and storing
// nested states. It's the general query interface to retrieve:
// * Contracts
// * Accounts
type StateDB struct {
    db   Database
    trie Trie

    snaps         *snapshot.Tree
    snap          snapshot.Snapshot
    snapDestructs map[common.Hash]struct{}
    snapAccounts  map[common.Hash][]byte
    snapStorage   map[common.Hash]map[common.Hash][]byte

    // This map holds 'live' objects, which will get modified while processing a state transition.
    stateObjects        map[common.Address]*stateObject
    stateObjectsPending map[common.Address]struct{} // State objects finalized but not yet written to the trie
    stateObjectsDirty   map[common.Address]struct{} // State objects modified in the current execution

    // DB error.
    // State objects are used by the consensus core and VM which are
    // unable to deal with database-level errors. Any error that occurs
    // during a database read is memoized here and will eventually be returned
    // by StateDB.Commit.
    dbErr error

    // The refund counter, also used by state transitioning.
    refund uint64

    thash, bhash common.Hash
    txIndex      int
    logs         map[common.Hash][]*types.Log
    logSize      uint

    preimages map[common.Hash][]byte

    // Per-transaction access list
    accessList *accessList

    // Journal of state modifications. This is the backbone of
    // Snapshot and RevertToSnapshot.
    journal        *journal
    validRevisions []revision
    nextRevisionId int

    // Measurements gathered during execution for debugging purposes
    AccountReads         time.Duration
    AccountHashes        time.Duration
    AccountUpdates       time.Duration
    AccountCommits       time.Duration
    StorageReads         time.Duration
    StorageHashes        time.Duration
    StorageUpdates       time.Duration
    StorageCommits       time.Duration
    SnapshotAccountReads time.Duration
    SnapshotStorageReads time.Duration
    SnapshotCommits      time.Duration
}
```

状态关系：世界状态  -&gt; 个人账号 -&gt; 个人状态

