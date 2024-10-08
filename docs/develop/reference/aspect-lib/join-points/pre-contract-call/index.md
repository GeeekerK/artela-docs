---
sidebar_position: 2
---

# PreContractCall

## 简介

PreContractCall 连接点发生在 [交易生命周期](https://docs.cosmos.network/v0.47/learn/beginner/tx-lifecycle) 的 `DeliverTx` 阶段。
此连接点将在执行合约调用之前调用。以下是调用图：

* `ApplyTransaction`
  * ⮕ `ApplyMessageWithConfig`
    * ⮕ `evm.Call`
      * ⮕ `loop opCodes`
        * | ⚙ [PreContractCall join point](/develop/reference/aspect-lib/tx-level-aspect/pre-contract-call)
        * | `evm.Interpreter.Run 0`
        * | ⚙ [PostContractCall join point](/develop/reference/aspect-lib/tx-level-aspect/post-contract-call)
        *
        * | ⚙ [PreContractCall join point](/develop/reference/aspect-lib/tx-level-aspect/pre-contract-call)
        * | `evm.Interpreter.Run 1`
        * | ⚙ [PostContractCall join point](/develop/reference/aspect-lib/tx-level-aspect/post-contract-call)
        * ....
        *
  * ⮕ `RefundGas`

在此阶段，帐户状态保持原始状态，允许 Aspect 根据需要预加载信息。

## 界面

```
interface IPreContractCallJP extends IAspectBase {
  preContractCall(input: PreContractCallInput): void;
}
```
* **Parameter**
  * 输入：PostContractCallInput；基础层将把PostContractCallInput对象传递给此连接点中的Aspect。
    - `input.block.number`：当前区块编号。
    - `input.call.from`：合约调用的调用者。
    - `input.call.to`：合约调用的目标地址。
    - `input.call.data`：合约调用的输入字节。
    - `input.call.gas`：合约调用的 gas 限制。
    - `input.call.index`：合约调用的索引。
    - `input.call.value`：合约调用的转移值。

* **Returns**
  * void; 如果 Aspect 正常返回，事务将继续执行。如果 Aspect 调用 `sys.revert` 来恢复事务，则基础层将恢复事务。


## 例子

<!-- @formatter:off -->
```typescript

/**
 * preContractCall is a join-point that gets invoked before the execution of a contract call.
 *
 * @param input Input of the given join-point
 * @return void
 */
preContractCall(input: PreContractCallInput): void {
  // call to a method 'save', with value of 100
  let callData = ethereum.abiEncode('save', [
    ethereum.Number.fromU32(100, 32)
  ]);

  let sender = hexToUint8Array("0xE2AF7C239b4F2800a2F742d406628b4fc4b8a0d4");
  let request = JitCallBuilder.simple(
      sender,
      input.call!.to,
      hexToUint8Array(callData)
  ).build();

  // Submit the JIT call
  let response = sys.hostApi.evmCall.jitCall(request);
  if (!response.success) {
      sys.revert(response.errorMsg);
  }
}

```
<!-- @formatter:on -->

## 编程指南

此方法有两种编程模式：

1. 通过利用“input”输入参数，它提供了对交易和块处理的重要见解。请参阅[如何使用输入](#how-to-use-input)。

2. 使用“sys”命名空间，它提供对系统数据和区块链运行时生成的上下文信息的高级 API 和低级 API 访问，包括有关环境、块、交易和实用程序类（如加密和 ABI 编码/解码）的详细信息。请参阅[更多详细信息](#how-to-use-sys-apis)。

**重点**：由于连接点位于 EVM 执行过程中，因此在此连接点中使用 [sys.revert()](/develop/reference/aspect-lib/components/sys#1-revert)、[sys.require()](/develop/reference/aspect-lib/components/sys#3-require) 实际上会恢复交易。

## 主机 API

有关所有 API 及其用法的全面概述，请参阅 [API 参考](/develop/reference/aspect-lib/components/overview)。

每个连接点都可以访问不同的主机 API，当前断点内可用的主机 API 可在下表中找到。

| [sys.revert](/develop/reference/aspect-lib/components/sys#1-revert) | ✅            | Forces the current transaction to fail.                      |
| ------------------------------------------------------------ | ------------ | ------------------------------------------------------------ |
| System APIs                                                  | Availability | Description                                                  |
| [sys.require](/develop/reference/aspect-lib/components/sys#2-require) | ✅            | Checks if certain conditions are met; if not, forces the entire transaction to fail. |
| [sys.log](/develop/reference/aspect-lib/components/sys#3-log) | ✅            | A wrapper for `sys.hostApi.util.log`, prints log messages to Artela output for debugging on the localnet. |
| [sys.aspect.id](/develop/reference/aspect-lib/components/sys-aspect#1-sysaspectid) | ✅            | Retrieves the ID of the aspect.                              |
| [sys.aspect.version ](/develop/reference/aspect-lib/components/sys-aspect#2-sysaspectversion) | ✅            | Retrieves the version of the aspect.                         |
| [sys.aspect.mutableState](/develop/reference/aspect-lib/components/sys-aspect#4-sysaspectmutablestate) | ✅            | A wrapper for `sys.hostApi.aspectState` that facilitates easier reading or writing of values of a specified type to aspect state. |
| [sys.aspect.property](/develop/reference/aspect-lib/components/sys-aspect#5-sysaspectproperty) | ✅            | A wrapper for `sys.hostApi.aspectProperty` that facilitates easier reading of values of a specified type from aspect property. |
| [sys.aspect.readonlyState](/develop/reference/aspect-lib/components/sys-aspect#3-sysaspectreadonlystate) | ✅            | A wrapper for `sys.hostApi.aspectState` that facilitates easier reading of values of a specified type from aspect state. |
| [sys.aspect.transientStorage](/develop/reference/aspect-lib/components/sys-aspect#6-sysaspecttransientstorage) | ✅            | A wrapper for `sys.hostApi.aspectTransientStorage` that facilitates easier reading or writing of values of a specified type to aspect transient storage. |
| [sys.hostApi.aspectProperty](/develop/reference/aspect-lib/components/sys-hostapi#syshostapiaspectproperty) | ✅            | Retrieves the property of the aspect as written in aspect deployment. |
| [sys.hostApi.aspectState](/develop/reference/aspect-lib/components/sys-hostapi#syshostapiaspectstate) | ✅            | Retrieves or writes the state of the aspect.                 |
| [sys.hostApi.aspectTransientStorage](/develop/reference/aspect-lib/components/sys-hostapi#syshostapiaspecttransientstorage) | ✅            | Retrieves or writes to the transient storage of the aspect. This storage is only valid within the current transaction lifecycle. |
| [sys.hostApi.crypto.ecRecover](/develop/reference/aspect-lib/components/sys-hostapi#4-ecrecover) | ✅            | Calls crypto methods `ecRecover`.                            |
| [sys.hostApi.crypto.keccak](/develop/reference/aspect-lib/components/sys-hostapi#1-keccak) | ✅            | Calls crypto methods `keccak`.                               |
| [sys.hostApi.crypto.ripemd160](/develop/reference/aspect-lib/components/sys-hostapi#3-ripemd160) | ✅            | Calls crypto methods `ripemd160`.                            |
| [sys.hostApi.crypto.sha256](/develop/reference/aspect-lib/components/sys-hostapi#2-sha256) | ✅            | Calls crypto methods `sha256`.                               |
| [sys.hostApi.runtimeContext](/develop/reference/aspect-lib/components/sys-hostapi#1-get-context) | ✅            | Retrieves runtime context by the key. Refer to the [runtime context](#runtime-context)  to see which keys can be accessed by the current join point. |
| [sys.hostApi.stateDb.balance](/develop/reference/aspect-lib/components/sys-hostapi#1-balance) | ✅            | Gets the balance of the specified address from the EVM state database. |
| [sys.hostApi.stateDb.codeHash](/develop/reference/aspect-lib/components/sys-hostapi#4-codehash) | ✅            | Gets the hash of the code from the EVM state database.       |
| [sys.hostApi.stateDb.codeSize](/develop/reference/aspect-lib/components/sys-hostapi#6-codesize) | ✅            | Gets the size of the code from the EVM state database.       |
| [sys.hostApi.stateDb.hasSuicided](/develop/reference/aspect-lib/components/sys-hostapi#3-hassuicided) | ✅            | Gets the codehash from the EVM state database.               |
| [sys.hostApi.stateDb.nonce](/develop/reference/aspect-lib/components/sys-hostapi#5-nonce) | ✅            | Checks if the contract at the specified address is suicided in the current transactions. |
| [sys.hostApi.stateDb.stateAt](/develop/reference/aspect-lib/components/sys-hostapi#2-stateat) | ✅            | Gets the state at a specific point.                          |
| [sys.hostApi.evmCall.jitCall](/develop/reference/aspect-lib/components/sys-hostapi#2-jitcall) | ✅            | Creates a contract call and executes it immediately.         |
| [sys.hostApi.evmCall.staticCall](/develop/reference/aspect-lib/components/sys-hostapi#1-staticcall) | ✅            | Creates a static call and executes it immediately.           |
| [sys.hostApi.trace.queryCallTree](/develop/reference/aspect-lib/components/sys-hostapi#2-querycalltree ) | ✅            | Returns the call tree of EVM execution.                      |
| [sys.hostApi.trace.queryStateChange](/develop/reference/aspect-lib/components/sys-hostapi#1-querystatechange) | ✅            | Returns the state change in EVM execution for the specified key. |


## Runtime context

The Aspect Runtime Context encapsulates data generated through the consensus process. With the acquired Runtime Context
object, retrieve specific data by specifying the relevant Context Key. Each Context Key is associated with a particular
type of data or information.

### Usage

```javascript
const chainId = sys.hostApi.runtimeContext.get('env.chain.chainId');
//decode UintData
const chainIdData = Protobuf.decode<UintData>(chainId, UintData.decode);
sys.log('env.chain.chainId' + ' ' + chainIdData.data.toString(10));

const enableCall = sys.hostApi.runtimeContext.get('env.enableCall');
//decode boolData
const enableCallData = Protobuf.decode<BoolData>(enableCall, BoolData.decode);
sys.log('env.enableCall' + ' ' + enableCallData.data.toString());

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
| tx.sig.v                                     | <a href="/api/docs/classes/proto.BytesData.html" target="_blank">BytesData</a>             | Returns the V signature values of the transaction.                                                                                                                                        |
| tx.sig.r                                     | <a href="/api/docs/classes/proto.BytesData.html" target="_blank">BytesData</a>             | Returns the R signature values of the transaction.                                                                                                                                        |
| tx.sig.s                                     | <a href="/api/docs/classes/proto.BytesData.html" target="_blank">BytesData</a>             | Returns the S signature values of the transaction.                                                                                                                                        |
| tx.from                                      | <a href="/api/docs/classes/proto.BytesData.html" target="_blank">BytesData</a>             | Returns the from address of the transaction.                                                                                                                                              |
| tx.index                                     | <a href="/api/docs/classes/proto.UintData.html" target="_blank">UintData</a>               | Returns the transaction index of current block.                                                                                                                                           |
| block.header.parentHash                      | <a href="/api/docs/classes/proto.BytesData.html" target="_blank">BytesData</a>             | Get the current block header parent hash.                                                                                                                                                 |
| block.header.miner                           | <a href="/api/docs/classes/proto.BytesData.html" target="_blank">BytesData</a>             | Get the current block header miner.                                                                                                                                                       |
| block.header.transactionsRoot                | <a href="/api/docs/classes/proto.BytesData.html" target="_blank">BytesData</a>             | Get the current block TransactionsRoot hash.                                                                                                                                              |
| block.header.number                          | <a href="/api/docs/classes/proto.UintData.html" target="_blank">UintData</a>               | Get the current block number.                                                                                                                                                             |
| block.header.timestamp                       | <a href="/api/docs/classes/proto.UintData.html" target="_blank">UintData</a>               | Get the current block header timestamp.                                                                                                                                                   |
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
| msg.from                                     | <a href="/api/docs/classes/proto.BytesData.html" target="_blank">BytesData</a>             | Returns the sender address of the EVM call message.                                                                                                                                       |
| msg.to                                       | <a href="/api/docs/classes/proto.BytesData.html" target="_blank">BytesData</a>             | Returns the recipient address of the EVM call message.                                                                                                                                    |
| msg.value                                    | <a href="/api/docs/classes/proto.BytesData.html" target="_blank">BytesData</a>             | Returns the ether amount of the EVM call message.                                                                                                                                         |
| msg.gas                                      | <a href="/api/docs/classes/proto.UintData.html" target="_blank">UintData</a>               | Returns the gas limit of the EVM call message.                                                                                                                                            |
| msg.input                                    | <a href="/api/docs/classes/proto.BytesData.html" target="_blank">BytesData</a>             | Returns the input data of the EVM call message.                                                                                                                                           |
| msg.index                                    | <a href="/api/docs/classes/proto.UintData.html" target="_blank">UintData</a>               | Returns the index of the EVM call message.                                                                                                                                                |
| msg.result.ret                               | <a href="/api/docs/classes/proto.BytesData.html" target="_blank">BytesData</a>             | Returns the result of the EVM call.                                                                                                                                                       |
| msg.result.gasUsed                           | <a href="/api/docs/classes/proto.UintData.html" target="_blank">UintData</a>               | Returns the gas used of the EVM call.                                                                                                                                                     |
| msg.result.error                             | <a href="/api/docs/classes/proto.StringData.html" target="_blank">StringData</a>           | Returns the result error message of the EVM call.                                                                                                                                         |
