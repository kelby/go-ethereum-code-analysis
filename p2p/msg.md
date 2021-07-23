Msg defines the structure of a p2p message.

```go
// Msg defines the structure of a p2p message.
//
// Note that a Msg can only be sent once since the Payload reader is
// consumed during sending. It is not possible to create a Msg and
// send it any number of times. If you want to reuse an encoded
// structure, encode the payload into a byte array and create a
// separate Msg with a bytes.Reader as Payload for each send.
type Msg struct {
	Code       uint64
	Size       uint32 // Size of the raw payload
	Payload    io.Reader
	ReceivedAt time.Time

	meterCap  Cap    // Protocol name and version for egress metering
	meterCode uint64 // Message within protocol for egress metering
	meterSize uint32 // Compressed message size for ingress metering
}
```



