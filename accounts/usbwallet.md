usb 钱包主流钱包有两个：ledger 和 trezor

代码结构分析：

* 两者共用 wallet，实现外部的接口 Wallet

* 两者共用 Hub，实现外部的接口 Backend

* 两者都属于 usb 钱包，对比其它类型钱包，又都有/实现 driver 接口





