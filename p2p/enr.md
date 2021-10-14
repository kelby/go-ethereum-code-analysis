EIP-778: Ethereum Node Records \(ENR\)

现状：

Ethereum nodes discover each other through the node discovery protocol. The purpose of that protocol is relaying node identity public keys \(on the secp256k1 curve\), their IP address and two port numbers. No other information can be relayed.

期望改善：

This specification seeks to lift the restrictions of the discovery v4 protocol by defining a flexible format, the node record, for connectivity-related information. Node records can be relayed through a future version of the node discovery protocol. They can also be relayed through arbitrary other mechanisms such as DNS, ENS, a devp2p subprotocol, etc.



