Manager

```go
// Manager is an overarching account manager that can communicate with various
// backends for signing transactions.
type Manager struct {
    config      *Config                    // Global account manager configurations
    backends    map[reflect.Type][]Backend // Index of backends currently registered
    updaters    []event.Subscription       // Wallet update subscriptions for all backends
    updates     chan WalletEvent           // Subscription sink for backend wallet changes
    newBackends chan newBackendEvent       // Incoming backends to be tracked by the manager
    wallets     []Wallet                   // Cache of all wallets from all registered backends

    feed event.Feed // Wallet feed notifying of arrivals/departures

    quit chan chan error
    term chan struct{} // Channel is closed upon termination of the update loop
    lock sync.RWMutex
}
```

重点：Backend 和 Wallet. 可以多钱包、多后端管理。



