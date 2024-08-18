---
sidebar_position：2
---

# 交易加入点

## 介绍

交易级别方面定义了多个联接点，每个联接点代表了独特的状态过渡
在特定阶段的功能 [交易生命周期](/develop/core-concepts/lifecycle),
它需要约束智能合约并被EOA交易激活。

 ![img.png](../img/jp2.svg) 

当前的交易已交付到EVM模块，并根据Geth的执行进行交易
逻辑 `ApplyTransaction` ⮕ `ApplyMessage` ⮕ `evm. Call`，在此过程中，执行其他联接点，并
过程显示在以下呼叫图中。

*`RunMsg` 
*|⚙ [验证加入点](/develop/reference/aspect-lib/tx-level-aspect/verify-tx) 
*`ApplyTransaction` 
*⮕ `ApplyMessageWithConfig` 
*⚙ [presxececute加入点](/develop/reference/aspect-lib/tx-level-aspect/pre-tx-execute) 
*⮕ `evm.Call` 
*⮕ `loop opCodes` 
*|⚙ [预合同加入点](/develop/reference/aspect-lib/tx-level-aspect/pre-contract-call) 
*|`evm.Interpreter.Run 0` 
*|
⚙ [ContractCall加入点](/develop/reference/aspect-lib/tx-level-aspect/post-contract-call) 
*|
*|
⚙ [预合同加入点](/develop/reference/aspect-lib/tx-level-aspect/pre-contract-call) 
*|`evm.Interpreter.Run 1` 
*|
⚙ [ContractCall加入点](/develop/reference/aspect-lib/tx-level-aspect/post-contract-call) 
*|...
*|
*⚙ [txexecute加入点](/develop/reference/aspect-lib/tx-level-aspect/post-tx-execute) 
*⮕ `RefundGas` 

## 如何创建事务级方面

一个完整的方面应包括几个组件。 首先，创建您的班级作为方面的实现。
然后，根据要在您的方面实现的联接点导入和继承，并填写自己的逻辑
实施这些功能。 作为您方面的许可管理部分的Isowner方法必须是
实施的。 接下来，用方面限额注册您的上课类。 最后，您的代码应包括导入和
方面频率使用的出口以及您的方面必须包含的组件。

对于给定的代码段，Myaspect是我的方面类：

1. 强制性实施包括：

<！ -  @formatter：off->
```typescript
import { entryPoint, execute, allocate } from "@artela/aspect-libs";
...
class MyAspect {
  isOwner(sender: Uint8Array): bool {
    // your logic here
  }
  ...
}
...
const aspect = new MyAspect();
entryPoint.setAspect(aspect);
export { execute, allocate }
```
<！ -  @formatter：on->>

2. 可选实现可能包括，例如，如果要实现加入点 `preTxExecute` 

<！ -  @formatter：off->
```typescript
import { PreTxExecuteInput, IPreTxExecuteJP } from "@artela/aspect-libs";
...
class MyAspect implements IPreTxExecuteJP {
  preTxExecute(input: PreTxExecuteInput): void {
    // you logic here
  }
}
```
<！ -  @formatter：on->>

一个完整的例子如下。
<！ -  @formatter：off->
```typescript

import {
  entryPoint,
  execute,
  allocate,

  TxVerifyInput,
  PreTxExecuteInput,
  PreContractCallInput,
  PostContractCallInput,
  PostTxExecuteInput,
  OperationInput,

  ITransactionVerifier,
  IPreTxExecuteJP,
  IPreContractCallJP,
  IPostContractCallJP,
  IPostTxExecuteJP,
  IAspectOperation,
} from "@artela/aspect-libs";

class MyAspect implements ITransactionVerifier,
        IPreTxExecuteJP,
        IPreContractCallJP,
        IPostContractCallJP,
        IPostTxExecuteJP,
        IAspectOperation {

  //****  ****  ****  ****  ****  ****  **** 
  // Optional Methods
  //****  ****  ****  ****  ****  ****  **** 

  /**
   * verifyTx is a join-point which will be invoked in both check mode and deliver mode.
   * This method is optional; remove ITransactionVerifier if you do not want to include this functionality.
   * This method is executed only for customized verification transactions.
   *
   * @param input Input of the given join-point
   * @return a 20-byte address
   */
  verifyTx(input: TxVerifyInput): Uint8Array {
    return new Uint8Array(20);
  }

  /**
   * preTxExecute is a join-point that gets invoked before the execution of a transaction.
   * This method is optional; remove IPreTxExecuteJP if you do not want to include this functionality.
   *
   * @param input Input of the given join-point
   * @return void
   */
  preTxExecute(input: PreTxExecuteInput): void {
  }

  /**
   * preContractCall is a join-point that is invoked before the execution of a contract call.
   * This method is optional; remove IPreContractCallJP if you do not want to include this functionality.
   *
   * @param input Input of the given join-point
   * @return void
   */
  preContractCall(input: PreContractCallInput): void {
  }

  /**
   * postContractCall is a join-point that gets invoked after the completion of a contract call.
   * This method is optional; remove IPostContractCallJP if you do not want to include this functionality.
   *
   * @param input Input of the given join-point
   * @return void
   */
  postContractCall(input: PostContractCallInput): void {
  }

  /**
   * postTxExecute is a join-point that gets invoked when the transaction execution is completed, and the state is not yet committed.
   * This method is optional; remove IPostTxExecuteJP if you do not want to include this functionality.
   *
   * @param input Input of the given join-point
   * @return void
   */
  postTxExecute(input: PostTxExecuteInput): void {
  }

  /**
   * operation is used to execute the logics within the Aspect.
   *
   * @param input input data for the operation.
   * @return the data of the operation output.
   */
  operation(input: OperationInput): Uint8Array {

    return new Uint8Array(10);
  }

  //****  ****  ****  ****  ****  ****  **** 
  // Required Methods
  //****  ****  ****  ****  ****  ****  **** 

  /**
   * isOwner is the governance account implemented by the Aspect.
   * When any governance operation, including upgrade, config, or destroy, is performed, the isOwner method is invoked to check against the initiator's account.
   * This ensures that the initiator has the necessary permission.
   *
   * @param sender Address of the operation initiator
   * @return True if the check is successful, false if the check fails
   */
  isOwner(sender: Uint8Array): bool {
    return true;
  }

}

// Register your aspect instance to aspect libs
const aspect = new MyAspect()
entryPoint.setAspect(aspect)

// Export lib methods for aspect-runtime
export {execute, allocate}

```
<！ -  @formatter：on->>

## 加入点描述

接下来，我们将在IaspectTransaction接口中提供详细的描述。 您可以单击
与每个方法名称关联的链接以访问更多详细信息。 建议有先验
理解 [联接点的概念](/develop/core-concepts/join-point).

1. ISOWER

> 这是该方面实施的治理帐户（当
>
包括 [绑定](/develop/core-concepts/lifecycle#binding)，，，，[打开](/develop/core-concepts/lifecycle#unbinding)，...））
> 是制造的，将调用Isowner方法来检查发起者的帐户，以确保其已获得许可。

2. 2。[verifytx](/develop/reference/aspect-lib/join-points/verify-aspect) 

> 当绑定到某些DAPP和EOA时，方面可以提供交易验证服务。 如果某个方面被绑定
> 某些DAPP和EOA作为交易验证者，而不是进行遗产SECP256K1签名验证，方面
> 将用验证方法中实现的自定义验证逻辑替换。

3. 3。[presxececute](/develop/reference/aspect-lib/join-points/pre-tx-execute) 

> 在交易执行之前触发。 在此阶段，帐户状态仍然是原始的，允许方面
> 必要时预加载信息。

4. 4。[预合同](/develop/reference/aspect-lib/join-points/pre-contract-call) 

> 在执行交叉合同呼叫之前触发。 例如，在TX执行期间，UNISWAP合同调用
> 进入制造商合同，该方面将被执行。

5. 5。[订婚后](/develop/reference/aspect-lib/join-points/post-contract-call) 

> 在执行交叉合同呼叫后触发。 然后，该方面可以检查合同的通话后状态
> 并做出后续执行决定。

6. 6。[txexecute](/develop/reference/aspect-lib/join-points/post-tx-execute) 

> 一旦执行交易并完成了账户状态，就被激活了。 随后，方面可以
> 对最终执行状态进行全面审查。

7. 7。[手术](/develop/reference/aspect-lib/operation) 

> 操作是一个特殊接口。 与其他在交易执行过程中触发的联接点不同，此
> 界面可以通过EOA签署的操作交易直接调用。 操作交易将触发
> 执行方面。