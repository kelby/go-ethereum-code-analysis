Create:

// 确保特定的地址没有合约存在

        nonce := evm.StateDB.GetNonce\(caller.Address\(\)\)

        evm.StateDB.SetNonce\(caller.Address\(\), nonce+1\)

        snapshot := evm.StateDB.Snapshot\(\) //创建一个StateDB的快照，以便回滚



        evm.StateDB.CreateAccount\(contractAddr\) //创建账户

        evm.Transfer\(evm.StateDB, caller.Address\(\), contractAddr, value\) //转账

        ret, err = run\(evm, snapshot, contract, nil\) //执行合约的初始化代码

        //如果合同创建成功并且没有错误返回，则计算存储代码所需的GAS。 如果由于没有足够的GAS而导致代码不能被存储设置错误，并通过下面的错误检查条件来处理。

        // 当错误返回我们回滚修改，

Call方法, 无论我们转账或者是执行合约代码都会调用到这里， 同时合约里面的call指令也会执行到这里。





    // Call 执行与给定的input作为参数与addr相关联的合约。

    // 它还处理所需的任何必要的转账操作，并采取必要的步骤来创建帐户

    // 并在任意错误的情况下回滚所做的操作。

 // 调用深度最多1024

        // 查看我们的账户是否有足够的金钱。

  // 执行转账

        evm.Transfer\(evm.StateDB, caller.Address\(\), to.Address\(\), value\)



     // 如果是由revert指令触发的错误，因为ICO一般设置了人数限制或者资金限制

                // 在大家抢购的时候很可能会触发这些限制条件，导致被抽走不少钱。这个时候

                // 又不能设置比较低的GasPrice和GasLimit。因为要速度快。

                // 那么不会使用剩下的全部Gas，而是只会使用代码执行的Gas

                // 不然会被抽走 GasLimit \*GasPrice的钱，那可不少。

剩下的三个函数 CallCode, DelegateCall, 和 StaticCall，这三个函数不能由外部调用，只能由Opcode触发。



