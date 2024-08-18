---
sidebar_position：12
---

# 定制的交易验证

方面提供了一种新颖的方法，用于定制外部帐户（EOA）的交易验证过程。 此功能超出了传统的ECDA验证，允许交易验证数据包含广泛的范围，包括零知识（ZK）证明。 让我们探讨这是如何工作的。

## 概述

要采用自定义的交易验证过程，发件人的EOA和收件人的智能合约都必须与相同的交易验证者方面联系起来。 这种双重结合确保了EOA和DAPP之间提供的验证方法中的相互信任。

：：：警告
谨慎行事，并在结合之前彻底检查交易验证者方面。 不当使用可能会避免标准ECDSA签名验证。
:::

当EOA发送缺乏有效ECDSA签名的交易时，`onTxVerify` 交易验证者方面的方法是通过事务验证数据触发的。 这些数据包含在交易中的 `data` 字段与以太坊定义的常规呼叫数据格式不同。 相反，它采用格式 `abi.encode(validationData, callData)`。 随着交易的验证和执行，Artela的基础层解码并将交易数据恢复为其原始结构。

以下是描述交易验证过程的图：

 ![在TX-Verify](./on-tx-verify.svg) 

## 事务验证者接口

要充当交易验证者方面，一个方面必须实现 `ITransactionVerifier` 界面。 该接口包括一个方法，`verifyTx` 这是从没有有效的ECDSA签名的情况下从EOA发送的交易来调用的。

```typescript
export class Aspect implements ITransactionVerifier {
    /**
     * The verifyTx method is invoked for transactions from an EoA.
     * It allows customization of the transaction verification process.
     * 
     * @param ctx The chain context in transaction verification.
     * @param validationData Data used to derive the sender's address.
     */
    verifyTx(ctx: VerifyTxCtx, validationData: Uint8Array): Uint8Array {
        // Custom verification logic
        // ...
        return address; // A 20-byte address must be returned to the base layer.
    }
}
```

## 绑定极限

出于绩效原因，智能合约仅限于具有一个交易验证者方面的约束。 但是，EOA可以与多个交易验证符结合，每个验证者都量身定制用于访问不同的DAPP。