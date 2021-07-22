以太坊黄皮书上说的gasLimit的计算方法：

```
gasLimit = Gtransaction + Gtxdatanonzero × dataByteLength
```

需要注意的是这只是静态的gas消耗，实际gas消耗还需要加上合约执行的开销。

计算 IntrinsicGas的源码位置 core/state\_transition.go



