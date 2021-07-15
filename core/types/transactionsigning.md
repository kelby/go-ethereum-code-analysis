```go
// Signer encapsulates transaction signature handling. The name of this type is slightly
// misleading because Signers don't actually sign, they're just for validating and
// processing of signatures.
//
// Note that this interface is not a stable API and may change at any time to accommodate
// new protocol rules.
type Signer interface {
    // Sender returns the sender address of the transaction.
    Sender(tx *Transaction) (common.Address, error)

    // SignatureValues returns the raw R, S, V values corresponding to the
    // given signature.
    SignatureValues(tx *Transaction, sig []byte) (r, s, v *big.Int, err error)
    ChainID() *big.Int

    // Hash returns 'signature hash', i.e. the transaction hash that is signed by the
    // private key. This hash does not uniquely identify the transaction.
    Hash(tx *Transaction) common.Hash

    // Equal returns true if the given signer is the same as the receiver.
    Equal(Signer) bool
}
```

对交易进行签名，最最关键方法应是 `SignatureValues` 返回 R, S, V

具体实现有 EIP155Signer、FrontierSigner、HomesteadSigner 共 3 个。

