```go
// chainSyncer coordinates blockchain sync components.
type chainSyncer struct {
	handler     *handler
	force       *time.Timer
	forced      bool // true when force timer fired
	peerEventCh chan struct{}
	doneCh      chan error // non-nil when sync is running
}
```



