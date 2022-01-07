定规则

When the signer receives a request over the external API, the corresponding method is evaluated.

Three things can happen:

1. The method returns "Approve". This means the operation is permitted.

2. The method returns "Reject". This means the operation is rejected.

3. Anything else; other return values \[\*\], method not implemented or exception occurred during processing. This means

that the operation will continue to manual processing, via the regular UI method chosen by the user.

cmd/clef 调用，约等于没用。



