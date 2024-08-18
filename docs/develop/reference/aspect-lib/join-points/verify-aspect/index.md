---
sidebar_position: 2
---

# 交易验证 Aspect

## 介绍

Aspect 在绑定到某些 dApp 和 EoA 时可以提供交易验证服务。如果某个 Aspect 已作为交易验证器绑定到
某些 dApp 和 EoA，Aspect 将不再执行传统的 secp256k1 签名验证，而是将其替换为在 `verifyTx` 方法中实现的自定义验证逻辑。

![verify.svg](verify.svg)

## 接口

```
interface ITransactionVerifier extends IAspectBase {
  verifyTx(input: TxVerifyInput): Uint8Array;
}
```

* **参数**
    * 输入：TxVerifyInput；基础层将把TxVerifyInput对象传递给此连接点中的Aspect。
        - `input.block.number`：当前区块编号。
        - `input.tx.from`：交易调用者。
        - `input.tx.to`：交易目的地地址。
        - `input.tx.hash`：交易哈希值。
* **返回**
    * Uint8Array；已验证的账户地址。

## 示例

要充当交易验证器方面，方面必须实现 `ITransactionVerifier` 接口。此接口包含一个方法 verifyTx，该方法用于从没有有效 ECDSA 签名的 EoA 发送的交易。

```typescript
import {
    ITransactionVerifier, allocate, entryPoint, execute, sys, TxVerifyInput
} from "@artela/aspect-libs";

class Aspect implements ITransactionVerifier {
    verifyTx(input: TxVerifyInput): Uint8Array {
        return sys.aspect.property.get<Uint8Array>('verifyAccount');
    }

    /**
     * isOwner is the governance account implemented by the Aspect, when any of the governance operation
     * (including upgrade, config, destroy) is made, isOwner method will be invoked to check
     * against the initiator's account to make sure it has the permission.
     *
     * @param ctx context of Aspect state
     * @param sender address of the operation initiator
     * @return true if check success, false if check fail
     */
    isOwner(sender: Uint8Array): bool {
        // always return false on isOwner can make the Aspect immutable
        return true;
    }
}

// 2.register aspect Instance
const aspect = new Aspect();
entryPoint.setAspect(aspect);

// 3.must export it
export {execute, allocate};
```

## 编程指南

此方法有两种编程模式：

1. 通过利用“input”输入参数，它提供了对交易和块处理的重要见解。
请参阅[如何使用输入](#how-to-use-input)。

2. 使用“sys”命名空间，它提供对系统数据和区块链运行时生成的上下文信息的高级 API 和低级 API 访问，包括有关环境、块、交易和实用程序类（如加密和 ABI 编码/解码）的详细信息。请参阅[更多详细信息](#how-to-use-apis)。

## 主机 API

有关所有 API 及其用法的全面概述，请参阅[API 参考](/develop/reference/aspect-lib/components/overview)。

每个连接点都可以访问不同的主机 API，当前断点内可用的主机 API 可以在下表中找到。

| System APIs                                                                                                                 | Availability | Description                                                                                                                                              |
|-----------------------------------------------------------------------------------------------------------------------------|--------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| [sys.revert](/develop/reference/aspect-lib/components/sys#1-revert)                                                         | ✅            | Forces the current transaction to fail.                                                                                                                  |
| [sys.require](/develop/reference/aspect-lib/components/sys#2-require)                                                       | ✅            | Checks if certain conditions are met; if not, forces the entire transaction to fail.                                                                     |
| [sys.log](/develop/reference/aspect-lib/components/sys#3-log)                                                               | ✅            | A wrapper for `sys.hostApi.util.log`, prints log messages to Artela output for debugging on the localnet.                                                |
| [sys.aspect.id](/develop/reference/aspect-lib/components/sys-aspect#1-sysaspectid)                                          | ✅            | Retrieves the ID of the aspect.                                                                                                                          |
| [sys.aspect.version ](/develop/reference/aspect-lib/components/sys-aspect#2-sysaspectversion)                               | ✅            | Retrieves the version of the aspect.                                                                                                                     |
| [sys.aspect.mutableState](/develop/reference/aspect-lib/components/sys-aspect#4-sysaspectmutablestate)                      | ❌            | A wrapper for `sys.hostApi.aspectState` that facilitates easier reading or writing of values of a specified type to aspect state.                        |
| [sys.aspect.property](/develop/reference/aspect-lib/components/sys-aspect#5-sysaspectproperty)                              | ✅            | A wrapper for `sys.hostApi.aspectProperty` that facilitates easier reading of values of a specified type from aspect property.                           |
| [sys.aspect.readonlyState](/develop/reference/aspect-lib/components/sys-aspect#3-sysaspectreadonlystate)                    | ✅            | A wrapper for `sys.hostApi.aspectState` that facilitates easier reading of values of a specified type from aspect state.                                 |
| [sys.aspect.transientStorage](/develop/reference/aspect-lib/components/sys-aspect#6-sysaspecttransientstorage)              | ❌            | A wrapper for `sys.hostApi.aspectTransientStorage` that facilitates easier reading or writing of values of a specified type to aspect transient storage. |
| [sys.hostApi.aspectProperty](/develop/reference/aspect-lib/components/sys-hostapi#syshostapiaspectproperty)                 | ✅            | Retrieves the property of the aspect as written in aspect deployment.                                                                                    |
| [sys.hostApi.aspectState](/develop/reference/aspect-lib/components/sys-hostapi#syshostapiaspectstate)                       | ✅            | Retrieves or writes the state of the aspect.                                                                                                             |
| [sys.hostApi.aspectTransientStorage](/develop/reference/aspect-lib/components/sys-hostapi#syshostapiaspecttransientstorage) | ❌            | Retrieves or writes to the transient storage of the aspect. This storage is only valid within the current transaction lifecycle.                         |
| [sys.hostApi.crypto.ecRecover](/develop/reference/aspect-lib/components/sys-hostapi#4-ecrecover)                            | ✅            | Calls crypto methods `ecRecover`.                                                                                                                        |
| [sys.hostApi.crypto.keccak](/develop/reference/aspect-lib/components/sys-hostapi#1-keccak)                                  | ✅            | Calls crypto methods `keccak`.                                                                                                                           |
| [sys.hostApi.crypto.ripemd160](/develop/reference/aspect-lib/components/sys-hostapi#3-ripemd160)                            | ✅            | Calls crypto methods `ripemd160`.                                                                                                                        |
| [sys.hostApi.crypto.sha256](/develop/reference/aspect-lib/components/sys-hostapi#2-sha256)                                  | ✅            | Calls crypto methods `sha256`.                                                                                                                           |
| [sys.hostApi.runtimeContext](/develop/reference/aspect-lib/components/sys-hostapi#1-get-context)                            | ✅            | Retrieves runtime context by the key.  Refer to the [runtime context](#runtime-context)  to see which keys can be accessed by the current join point.    |
| [sys.hostApi.stateDb.balance](/develop/reference/aspect-lib/components/sys-hostapi#1-balance)                               | ❌            | Gets the balance of the specified address from the EVM state database.                                                                                   |
| [sys.hostApi.stateDb.codeHash](/develop/reference/aspect-lib/components/sys-hostapi#4-codehash)                             | ❌            | Gets the hash of the code from the EVM state database.                                                                                                   |
| [sys.hostApi.stateDb.codeSize](/develop/reference/aspect-lib/components/sys-hostapi#6-codesize)                             | ❌            | Gets the size of the code from the EVM state database.                                                                                                   |
| [sys.hostApi.stateDb.hasSuicided](/develop/reference/aspect-lib/components/sys-hostapi#3-hassuicided)                       | ❌            | Gets the codehash from the EVM state database.                                                                                                           |
| [sys.hostApi.stateDb.nonce](/develop/reference/aspect-lib/components/sys-hostapi#5-nonce)                                   | ❌            | Checks if the contract at the specified address is suicided in the current transactions.                                                                 |
| [sys.hostApi.stateDb.stateAt](/develop/reference/aspect-lib/components/sys-hostapi#2-stateat)                               | ❌            | Gets the state at a specific point.                                                                                                                      |
| [sys.hostApi.evmCall.jitCall](/develop/reference/aspect-lib/components/sys-hostapi#2-jitcall)                               | ❌            | Creates a contract call and executes it immediately.                                                                                                     |
| [sys.hostApi.evmCall.staticCall](/develop/reference/aspect-lib/components/sys-hostapi#1-staticcall)                         | ✅            | Creates a static call and executes it immediately.                                                                                                       |
| [sys.hostApi.trace.queryCallTree](/develop/reference/aspect-lib/components/sys-hostapi#2-querycalltree )                    | ❌            | Returns the call tree of EVM execution.                                                                                                                  |
| [sys.hostApi.trace.queryStateChange](/develop/reference/aspect-lib/components/sys-hostapi#1-querystatechange)               | ❌            | Returns the state change in EVM execution for the specified key.                                                                                         |

## 运行时上下文

Aspect 运行时上下文封装了通过共识过程生成的数据。使用获取的运行时上下文
对象，通过指定相关的上下文键来检索特定数据。每个上下文键都与特定类型的数据或信息相关联。

### 用法

```javascript

const isCall = sys.hostApi.runtimeContext.get('isCall');
// decode BoolData
const isCallData = Protobuf.decode < BoolData > (isCall, BoolData.decode);
sys.log('is call' + ' ' + isCallData.data.toString());

const number = sys.hostApi.runtimeContext.get('block.header.number');
// decode UintData
const numberData = Protobuf.decode < UintData > (number, UintData.decode);
sys.log('block.header.number' + ' ' + numberData.data.toString(10));

```

### Key table

| Context key                                  | Value type                                                                                 | Description                                                                                                                                                                               |
|----------------------------------------------|--------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| isCall                                       | <a href="/api/docs/classes/proto.BoolData.html" target="_blank">BoolData</a>               | Get the current transaction is **Call** or **Send**. If it is **Call**, return true.                                                                                                      |
| tx.type                                      | <a href="/api/docs/classes/proto.UintData.html" target="_blank">UintData</a>               | Returns the transaction type id. LegacyTxType=0x00 AccessListTxType=0x01 DynamicFeeTxType=0x02 BlobTxType=0x03                                                                            |
| tx.chainId                                   | <a href="/api/docs/classes/proto.BytesData.html" target="_blank">BytesData</a>             | Returns the EIP155 chain ID of the transaction. The return value will always be non-nil. For legacy transactions which are not replay-protected, the return value is zero.                |
| tx.accessList                                | <a href="/api/docs/classes/proto.EthAccessList.html" target="_blank">EthAccessList</a>     | AccessListTx is the data of EIP-2930 access list transactions.                                                                                                                            |
| tx.nonce                                     | <a href="/api/docs/classes/proto.UintData.html" target="_blank">UintData</a>               | Returns the sender account nonce of the transaction.                                                                                                                                      |
| tx.gasPrice                                  | <a href="/api/docs/classes/proto.BytesData.html" target="_blank">BytesData</a>             | Returns the gas price of the transaction.                                                                                                                                                 |
| tx.gas                                       | <a href="/api/docs/classes/proto.UintData.html" target="_blank">UintData</a>               | Returns the gas limit of the transaction.                                                                                                                                                 |
| tx.gasTipCap                                 | <a href="/api/docs/classes/proto.BytesData.html" target="_blank">BytesData</a>             | Returns the gasTipCap per gas of the transaction.                                                                                                                                         |
| tx.gasFeeCap                                 | <a href="/api/docs/classes/proto.BytesData.html" target="_blank">BytesData</a>             | Returns the fee cap per gas of the transaction.                                                                                                                                           |
| tx.to                                        | <a href="/api/docs/classes/proto.BytesData.html" target="_blank">BytesData</a>             | Returns the recipient address of the transaction. For contract-creation transactions, To returns nil.                                                                                     |
| tx.value                                     | <a href="/api/docs/classes/proto.BytesData.html" target="_blank">BytesData</a>             | Returns the ether amount of the transaction.                                                                                                                                              |
| tx.data                                      | <a href="/api/docs/classes/proto.BytesData.html" target="_blank">BytesData</a>             | Returns the input data of the transaction.                                                                                                                                                |
| tx.bytes                                     | <a href="/api/docs/classes/proto.BytesData.html" target="_blank">BytesData</a>             | Returns the transaction marshal binary.                                                                                                                                                   |
| tx.hash                                      | <a href="/api/docs/classes/proto.BytesData.html" target="_blank">BytesData</a>             | Returns the transaction hash.                                                                                                                                                             |
| tx.unsigned.bytes                            | <a href="/api/docs/classes/proto.BytesData.html" target="_blank">BytesData</a>             | Returns the unsigned transaction marshal binary.                                                                                                                                          |
| tx.unsigned.hash                             | <a href="/api/docs/classes/proto.BytesData.html" target="_blank">BytesData</a>             | Returns the unsigned transaction hash.                                                                                                                                                    |
| block.header.number                          | <a href="/api/docs/classes/proto.UintData.html" target="_blank">UintData</a>               | Get the current block number.                                                                                                                                                             |
| env.extraEIPs                                | <a href="/api/docs/classes/proto.IntArrayData.html" target="_blank">IntArrayData</a>       | Retrieve the EVM module parameters for the '**extra_eips**': defines the additional EIPs for the vm.Config.                                                                               |
| env.enableCreate                             | <a href="/api/docs/classes/proto.BoolData.html" target="_blank">BoolData</a>               | Retrieve the EVM module parameters for the '**enable_create**': toggles states transitions that use the vm.Create function.                                                               |
| env.enableCall                               | <a href="/api/docs/classes/proto.BoolData.html" target="_blank">BoolData</a>               | Retrieve the EVM module parameters for the '**enable_call**': toggles states transitions that use the vm.Call function.                                                                   |
| env.allowUnprotectedTxs                      | <a href="/api/docs/classes/proto.BoolData.html" target="_blank">BoolData</a>               | Retrieve the EVM module parameters for the '**allow_unprotected_txs**': defines if replay-protected (i.e non EIP155 // signed) transactions can be executed on the states machine.        |
| env.chain.chainId                            | <a href="/api/docs/classes/proto.UintData.html" target="_blank">UintData</a>               | Retrieve the Ethereum chain config id.                                                                                                                                                    |
| env.chain.homesteadBlock                     | <a href="/api/docs/classes/proto.UintData.html" target="_blank">UintData</a>               | Retrieve the Ethereum chain configuration for the '**homestead_block**': switch (nil no fork, 0 = already homestead)                                                                      |
| env.chain.daoForkBlock                       | <a href="/api/docs/classes/proto.UintData.html" target="_blank">UintData</a>               | Retrieve the Ethereum chain configuration for the '**dao_fork_block**': corresponds to TheDAO hard-fork switch block (nil no fork)                                                        |
| env.chain.daoForkSupport                     | <a href="/api/docs/classes/proto.BoolData.html" target="_blank">BoolData</a>               | Retrieve the Ethereum chain configuration for the '**dao_fork_support**': defines whether the nodes supports or opposes the DAO hard-fork                                                 |
| env.chain.eip150Block                        | <a href="/api/docs/classes/proto.UintData.html" target="_blank">UintData</a>               | Retrieve the Ethereum chain configuration for the '**eip150_block**': EIP150 implements the Gas price changes (https://github.com/ethereum/EIPs/issues/150) EIP150 HF block (nil no fork) |
| env.chain.eip155Block                        | <a href="/api/docs/classes/proto.UintData.html" target="_blank">UintData</a>               | Retrieve the Ethereum chain configuration for the '**eip155_block**'.                                                                                                                     |
| env.chain.eip158Block                        | <a href="/api/docs/classes/proto.UintData.html" target="_blank">UintData</a>               | Retrieve the Ethereum chain configuration for the '**eip158_block**'.                                                                                                                     |
| env.chain.byzantiumBlock                     | <a href="/api/docs/classes/proto.UintData.html" target="_blank">UintData</a>               | Retrieve the Ethereum chain configuration for the '**byzantium_block**': Byzantium switch block (nil no fork, 0 =already on byzantium)                                                    |
| env.chain.constantinopleBlock                | <a href="/api/docs/classes/proto.UintData.html" target="_blank">UintData</a>               | Retrieve the Ethereum chain configuration for the '**constantinople_block**': Constantinople switch block (nil no fork, 0 = already activated)                                            |
| env.chain.petersburgBlock                    | <a href="/api/docs/classes/proto.UintData.html" target="_blank">UintData</a>               | Retrieve the Ethereum chain configuration for the '**petersburg_block**': Petersburg switch block (nil no fork, 0 = already activated)                                                    |
| env.chain.istanbulBlock                      | <a href="/api/docs/classes/proto.UintData.html" target="_blank">UintData</a>               | Retrieve the Ethereum chain configuration for the '**istanbul_block**': Istanbul switch block (nil no fork, 0 = already on istanbul)                                                      |
| env.chain.muirGlacierBlock                   | <a href="/api/docs/classes/proto.UintData.html" target="_blank">UintData</a>               | Retrieve the Ethereum chain configuration for the '**muir_glacier_block**': Eip-2384 (bomb delay) switch block ( nil no fork, 0 = already activated).                                     |
| env.chain.berlinBlock                        | <a href="/api/docs/classes/proto.UintData.html" target="_blank">UintData</a>               | Retrieve the Ethereum chain configuration for the '**berlin_block**': Berlin switch block (nil = no fork, 0 = already on berlin)                                                          |
| env.chain.londonBlock                        | <a href="/api/docs/classes/proto.UintData.html" target="_blank">UintData</a>               | Retrieve the Ethereum chain configuration for the '**london_block**': London switch block (nil = no fork, 0 = already on london)                                                          |
| env.chain.arrowGlacierBlock                  | <a href="/api/docs/classes/proto.UintData.html" target="_blank">UintData</a>               | Retrieve the Ethereum chain configuration for the '**arrow_glacier_block**': Eip-4345 (bomb delay) switch block (nil = no fork, 0 = already activated)                                    |
| env.chain.grayGlacierBlock                   | <a href="/api/docs/classes/proto.UintData.html" target="_blank">UintData</a>               | Retrieve the Ethereum chain configuration for the '**gray_glacier_block**': EIP-5133 (bomb delay) switch block (nil = no fork, 0 = already activated)                                     |
| env.chain.mergeNetSplitBlock                 | <a href="/api/docs/classes/proto.UintData.html" target="_blank">UintData</a>               | Retrieve the Ethereum chain configuration for the '**merge_netsplit_block**': Virtual fork after The Merge to use as a network splitter.                                                  |
| env.chain.shanghaiTime                       | <a href="/api/docs/classes/proto.UintData.html" target="_blank">UintData</a>               | Retrieve the Ethereum chain configuration for the '**shanghaiTime**': Shanghai switch time (nil = no fork, 0 = already on shanghai).                                                      |
| env.chain.cancunTime                         | <a href="/api/docs/classes/proto.UintData.html" target="_blank">UintData</a>               | Retrieve the Ethereum chain configuration for the '**CancunTime**': Cancun switch time (nil = no fork, 0 = already on cancun).                                                            |
| env.chain.pragueTime                         | <a href="/api/docs/classes/proto.UintData.html" target="_blank">UintData</a>               | Retrieve the Ethereum chain configuration for the '**PragueTime**': Prague switch time (nil = no fork, 0 = already on prague).                                                            |
| env.consensusParams.block.maxGas             | <a href="/api/docs/classes/proto.IntData.html" target="_blank">IntData</a>                 | Retrieve the max gas per block.                                                                                                                                                           |
| env.consensusParams.block.maxBytes           | <a href="/api/docs/classes/proto.IntData.html" target="_blank">IntData</a>                 | Retrieve the max block size, in bytes.                                                                                                                                                    |
| env.consensusParams.evidence.maxAgeDuration  | <a href="/api/docs/classes/proto.IntData.html" target="_blank">IntData</a>                 | Retrieve the max age duration.It should correspond with an app's "unbonding period" or other similar mechanism for handling.                                                              |
| env.consensusParams.evidence.maxAgeNumBlocks | <a href="/api/docs/classes/proto.IntData.html" target="_blank">IntData</a>                 | The basic formula for calculating this is: MaxAgeDuration / {average block time}.                                                                                                         |
| env.consensusParams.evidence.maxBytes        | <a href="/api/docs/classes/proto.IntData.html" target="_blank">IntData</a>                 | Retrieve the maximum size of total evidence in bytes that can be committed in a single block.                                                                                             |
| env.consensusParams.validator.pubKeyTypes    | <a href="/api/docs/classes/proto.StringArrayData.html" target="_blank">StringArrayData</a> | Restrict the public key types validators can use.                                                                                                                                         |
| env.consensusParams.appVersion               | <a href="/api/docs/classes/proto.UintData.html" target="_blank">UintData</a>               | Get the ABCI application version.                                                                                                                                                         |
| aspect.id                                    | <a href="/api/docs/classes/proto.BytesData.html" target="_blank">BytesData</a>             | Returns current aspect id.                                                                                                                                                                |
| aspect.version                               | <a href="/api/docs/classes/proto.UintData.html" target="_blank">UintData</a>               | Returns current aspect version.                                                                                                                                                           |

## 触发

要触发 'VerifyTx' 连接点，需要自定义一个没有签名的验证交易。此交易的格式如下：

```shell
magic prefix + checksum(encodedData) +codedData(validation data + raw calldata)
```

**说明：**

> * 0xCAFECAFE 为带 magic 前缀的固定值。
> * checksum(encodedData) 表示编码数据的 4 字节校验和。
> *codedData 是 ABI 编码的结果，使用 `ABI.encode(
> ValidationData, CallData)" 将验证数据和原始调用数据组合在一起。

示例：

```shell
0xCAFECAFE75bac07d00000000000000000000000000000000000000000000000000000000000000400000000000000000000000000000000000000
0000000000000000000000000c000000000000000000000000000000000000000000000000000000000000000557d7cfdeff97cbabdc4124c779d51
bb2520cbdc769840c6fee4566e23c7c22ea2a3e071dc794283b70608de4566c9dc0fd5f1854d7032676996fd95284693ecad5b2eeca9864734af5131
ed015c52cd1b5b3d9f841c000000000000000000000000000000000000000000000000000000000000000000000000000000000000241003e2d2000
000000000000000000000000000000000000000000000000000000000000100000000000000000000000000000000000000000000000000000000
```

* 0xCAFECAFE 是一个神奇的前缀。
* 75bac07d 是编码数据的 4 字节校验和。计算校验和，例如 `keccak256(encodedData).slice(2, 10)`。
* 剩余的字节表示 ValidationData 和 CallData 的 ABI 编码结果。例如 `abi.encodeParameters(['bytes', 'bytes'], [validationData, contractCallData])`。

这里有一个关于如何 [创建自定义验证交易](/develop/guides/verify-aspect) 的案例演示。
通过这个例子，你可以更清楚地了解如何使用 Verify Tx。