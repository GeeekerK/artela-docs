---
sidebar_position: 6
---

# Aspect 生命周期

Aspect 的生命周期包含多个阶段，包括部署、升级、配置、绑定、解除绑定、执行和销毁。

值得注意的是，位于地址 `0x000000000000000000000000000000000000000A27E14` 的系统合约 Aspect Core 管理所有 Aspect 生命周期操作。有关 Aspect Core 的 ABI 的更多详细信息，请参阅 [此链接](https://github.com/artela-network/artela-web3.js/blob/1.9.0/packages/web3-utils/src/aspect_core.json)。

## 部署

部署 Aspect 反映了传统智能合约的部署过程。要通过 EOA 交易部署 Aspect，请提供以下关键信息：

| 参数名称     | 必需 | 说明                                                         |
| ------------ | ---- | ------------------------------------------------------------ |
| `code`       | 是   | Aspect 的 WASM 工件的十六进制格式的字节码。                  |
| `properties` | 否   | Aspect 的初始只读状态。                                      |
| `account`    | 是   | 这是结算账户，负责为 Aspect 支付 gas 费用。某些 Aspect 操作会产生 gas 费用。目前，结算账户默认为合约调用的发送者，但可自定义的结算账户将在未来版本中提供。 |
| `proof`      | 否   | 用于未来支持自定义结算账户绑定验证的占位符。                 |

就像智能合约一样，一旦部署，Aspect 就会收到一个与 EVM 地址类型（20 字节）等效的唯一 ID。初始部署会为 Aspect 分配版本 1。

Aspect 的状态可以用 JSON 表示为：

```json
{
  "id": "0xABCDEF....",
  "code": {
    "1": "0xABCDEF...."
  },
  "properties": {
    "property-name": "property-value",
    ...
  },
  "settlementAccount": "0xABCDEF....",
  "currentVersion": 1
}
```

## 升级和配置

部署后，您可以更新 Aspect 的代码和属性。每次更新都会将版本增加一。

要升级 Aspect，请提供以下内容：

| 参数名称     | 必需 | 描述                                            |
| ------------ | ---- | ----------------------------------------------- |
| `aspectId`   | 是   | 正在升级的 Aspect 的 ID。                       |
| `code`       | 是   | 以十六进制格式更新的 Aspect WASM 工件的字节码。 |
| `properties` | 否   | 在 Aspect 内更新的只读状态。                    |

只有 Aspect 的所有者才能启动升级。所有者的地址必须通过 Aspect 中定义的 `isOwner(address): bool` 验证方法。

升级后的 Aspect 状态（以 JSON 表示）显示为：

```json
{
"id": "0xABCDEF....", // 保持不变
"code": {
"1": "0xABCDEF....",
"2": "新版本代码"
},
"properties": {
"property-name": "property-value",
"new-property": "new-value", // 如果设置了新属性，则添加
...
},
"settlementAccount": "0xABCDEF....", // 保持不变
"currentVersion": 2 // 增加 1
}
```

:::warning
升级 Aspect 不会破坏其当前绑定状态。绑定到旧 Aspect 版本的合约将继续执行旧代码。确保 Aspect 具有向后兼容性，以防止出现不可预见的行为。
:::

## 绑定

绑定将 Aspect 与特定的智能合约关联起来。只有通过合约中定义的 `isOwner(address): bool` 方法验证了智能合约所有者的地址，才能启动该过程。

绑定过程需要：

| 参数名称        | 必填 | 说明                                                         |
| --------------- | ---- | ------------------------------------------------------------ |
| `aspectId`      | 是   | 需要绑定的 Aspect 的 ID。                                    |
| `aspectVersion` | 是   | 需要绑定的 Aspect 的版本。使用 0 绑定到最新版本。            |
| `account`       | 是   | 要与 Aspect 绑定的帐户的地址。                               |
| `priority`      | 是   | Aspect 的执行优先级。数字越小，优先级越高。对于优先级相同的 Aspect，先绑定的 Aspect 先执行。 |

Aspect Core 合约将绑定关系记录为：

```json
{
  "0x{AccountAddress}": [
    {
      "aspectId": "0x{AspectId1}",
      "aspectVersion": 1
    },
    {
      "aspectId": "0x{AspectId2}",
      "aspectVersion": 2
    }
    ...
  ]
}
```

## 解除绑定

Aspect 可以与智能合约分离。只有智能合约的所有者（其地址必须通过 `isOwner(address): bool` 验证）才能发起解除绑定。

要解除绑定，您需要：

| 参数名称   | 必填 | 说明                        |
| ---------- | ---- | --------------------------- |
| `aspectId` | 是   | 要解除绑定的 Aspect 的 ID。 |
| `account`  | 是   | 要分离的帐户的地址。        |

一旦解除绑定，当收到与给定帐户相关的交易时，Aspect 将不会执行。

## 执行

Aspect 执行在两种情况下触发：

1. 指向绑定智能合约地址的 EOA 交易或调用。

2. 直接调用 Aspect 操作方法的 EOA 交易。

### 在连接点执行

在 Aspect 中，有一组预定义的方法将在特定连接点触发。
在交易处理的某个阶段，Aspect 的入口点函数将被调用，并且它将路由到与当前连接点对应的方法对。
例如，在进行合约调用之前，将执行 Aspect 的 `PreContractCall` 方法。

### 使用 EOA 交易执行

每个 Aspect 都有一个字节输入字节输出 `operation` 方法。
此方法是一个维护接口，允许 Aspect 维护者通过交易/调用更新或获取 Aspect 状态。
如果您的 Aspect 包含敏感数据，请确保在更改状态之前已实施必要的授权检查。
目前，`operation` 方法以 `bytes in bytes out` 格式运行，让开发人员管理编码、解码和路由。
未来版本将提供更简化的解决方案。

## 销毁

:::warning
此功能正在开发中。完成后将提供更新。
:::
