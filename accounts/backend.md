是个接口：

```go
// Backend is a "wallet provider" that may contain a batch of accounts they can
// sign transactions with and upon request, do so.
type Backend interface {
    // ...
    }
```

当前共有 5 套实现。

抽象的概念，和 Wallet 息息相关。

