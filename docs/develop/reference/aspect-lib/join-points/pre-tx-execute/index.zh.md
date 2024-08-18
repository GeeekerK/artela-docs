---
sidebar_position：2
---

# presxececute

## 介绍

在Delivertx阶段触发presxececute联接点 [交易生命周期](https://docs.cosmos.network/v0.47/learn/beginner/tx-lifecycle).
以下表示呼叫图：

*`ApplyTransaction` 
*⮕ `ApplyMessageWithConfig` 
*⚙ [presxececute加入点](/develop/reference/aspect-lib/tx-level-aspect/pre-tx-execute) 
*⮕ `evm.Call` 
*⮕ `loop opCodes` 
*|`evm.Interpreter.Run 0` 
*|`evm.Interpreter.Run 1` 
* ... ...
*⮕ `RefundGas` 

在此阶段，帐户状态仍然是原始的，可以根据需要进行预加载信息。

## 界面

```
interface IPreTxExecuteJP extends IAspectBase {
  preTxExecute(input: PreTxExecuteInput): void;
}
```
 ***范围** 
*输入：pretxexecuteInput; 基础层将将prestxececuteInput对象传递到此联接点中的方面。
    - `input.block.number` ：当前块号。
    - `input.tx.from` ：交易的呼叫者。
    - `input.tx.to` ：解决交易。
    - `input.tx.hash` ：交易的哈希。
 ***返回** 
* 空白; 如果方面正常返回，则交易将继续执行。 如果方面呼叫 `sys.revert` 为了恢复事务，基本层将恢复事务。

## 例子

<！ -  @formatter：off->
```typescript
/**
 * preTxExecute is a join-point that gets invoked before the execution of a transaction.
 *
 * @param input Input of the given join-point
 * @return void
 */
preTxExecute(input: PreTxExecuteInput): void {
  // In this method, constrain the execution of the aspect-bound contract to be invoked no more than 100 times.

  // import {sys} from "@artela/aspect-libs";
  const val = sys.aspect.mutableState.get<i32>("counter");
  let count = val.unwrap();

  // revert this transaction if it reaches the specified limit.
  sys.require(count <= 100, "The contract is invoked more than 100 times.");

  val.set<i32>(count + 1);
}

```
<！ -  @formatter：on->>

## 编程指南

在这种方法中可以使用两种编程模式：

1. 1.利用“输入”输入参数，它为交易和块处理提供了基本见解。 看 [如何使用输入](#how-to-use-input).

2. 2.使用“ SYS”名称空间，它提供了Hight Level API和低级API访问系统数据和区块链运行时生成的上下文信息的访问，包括有关环境，块，交易和公用事业类（例如Crypto和Abi）的详细信息 编码/解码。 看 [更多细节](#how-to-use-sys-apis).

 **重要的一点** ：由于联接点在EVM执行过程中，因此使用 [sys.revert（）](/develop/reference/aspect-lib/components/sys#1-revert)，，，，[sys.require（）](/develop/reference/aspect-lib/components/sys#3-require) 在此联接点中，实际上将恢复交易。


## 主机API

有关所有API及其用法的全面概述，请参阅 [API参考](/develop/reference/aspect-lib/components/overview).

每个联接点都可以访问其他主机API，并且在下表上可以找到当前断点中的主机API。

|系统API|可用性|描述|
|-----------------------------------------------------------------------------------------------------------------------------|--------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
|[sys.revert](/develop/reference/aspect-lib/components/sys#1-revert)|✅|迫使当前交易失败。|
|[sys.require](/develop/reference/aspect-lib/components/sys#2-require)|✅|检查是否满足某些条件； 如果不是，则迫使整个交易失败。|
|[sys.log](/develop/reference/aspect-lib/components/sys#3-log)|✅|包装纸 `sys.hostApi.util.log`，将日志消息打印到Artela输出中，以在LocalNet上调试。|
|[sys.aspect.id](/develop/reference/aspect-lib/components/sys-aspect#1-sysaspectid)|✅|检索该方面的ID。|
|[sys.aspect.version](/develop/reference/aspect-lib/components/sys-aspect#2-sysaspectversion)|✅|检索该方面的版本。|
|[Sys.Aspect.MutableState](/develop/reference/aspect-lib/components/sys-aspect#4-sysaspectmutablestate)|✅|包装纸 `sys.hostApi.aspectState` 这有助于更轻松地阅读或写入特定类型的值对方面状态。|
|[sys.aspect.property](/develop/reference/aspect-lib/components/sys-aspect#5-sysaspectproperty)|✅|包装纸 `sys.hostApi.aspectProperty` 这有助于更轻松地从Aspect属性读取指定类型的值。|
|[sys.Aspect.Readonlystate](/develop/reference/aspect-lib/components/sys-aspect#3-sysaspectreadonlystate)|✅|包装纸 `sys.hostApi.aspectState` 这有助于从方面状态更容易阅读指定类型的值。|
|[sys.aspect.transientstorage](/develop/reference/aspect-lib/components/sys-aspect#6-sysaspecttransientstorage)|✅|包装纸 `sys.hostApi.aspectTransientStorage` 这有助于更轻松地阅读或写入指定类型的值对方面瞬态存储。|
|[sys.hostapi.AspectProperty](/develop/reference/aspect-lib/components/sys-hostapi#syshostapiaspectproperty)|✅|检索方面的属性，如各个方面部署所写。|
|[sys.hostapi.Aspectstate](/develop/reference/aspect-lib/components/sys-hostapi#syshostapiaspectstate)|✅|检索或写入该方面的状态。|
|[sys.hostapi.AspectTransientStorage](/develop/reference/aspect-lib/components/sys-hostapi#syshostapiaspecttransientstorage)|✅|检索或写入该方面的瞬态存储。 此存储仅在当前事务生命周期内有效。|
|[sys.hostapi.crypto.ecrecover](/develop/reference/aspect-lib/components/sys-hostapi#4-ecrecover)|✅|调用加密方法 `ecRecover`。|
|[sys.hostapi.crypto.keccak](/develop/reference/aspect-lib/components/sys-hostapi#1-keccak)|✅|调用加密方法 `keccak`。|
|[sys.hostapi.crypto.ripemd160](/develop/reference/aspect-lib/components/sys-hostapi#3-ripemd160)|✅|调用加密方法 `ripemd160`。|
|[sys.hostapi.crypto.sha256](/develop/reference/aspect-lib/components/sys-hostapi#2-sha256)|✅|调用加密方法 `sha256`。|
|[sys.hostapi.runtimecontext](/develop/reference/aspect-lib/components/sys-hostapi#1-get-context)|✅|通过密钥检索运行时上下文。 参考 [运行时上下文](#runtime-context) 查看当前联接点可以访问哪些键。|
|[sys.hostapi.StatedB.Balance](/develop/reference/aspect-lib/components/sys-hostapi#1-balance)|❌|从EVM状态数据库中获取指定地址的余额。|
|[sys.hostapi.statedb.codehash](/develop/reference/aspect-lib/components/sys-hostapi#4-codehash)|❌|从EVM状态数据库中获取代码的哈希。|
|[sys.hostapi.statedb.sodesize](/develop/reference/aspect-lib/components/sys-hostapi#6-codesize)|❌|从EVM状态数据库获取代码的大小。|
|[sys.hostapi.statedb.hassuicided](/develop/reference/aspect-lib/components/sys-hostapi#3-hassuicided)|❌|从EVM状态数据库中获取代码检查。|
|[sys.hostapi.StatedB.Nonce](/develop/reference/aspect-lib/components/sys-hostapi#5-nonce)|❌|检查指定地址的合同是否在当前交易中自杀。|
|[sys.hostapi.statedb.stateat](/develop/reference/aspect-lib/components/sys-hostapi#2-stateat)|❌|在特定点获得状态。|
|[sys.hostapi.evmcall.jitcall](/develop/reference/aspect-lib/components/sys-hostapi#2-jitcall)|❌|创建合同电话并立即执行。|
|[sys.hostapi.evmcall.staticcall](/develop/reference/aspect-lib/components/sys-hostapi#1-staticcall)|❌|创建一个静态调用并立即执行。|
|[sys.hostapi.trace.querycalltree](/develop/reference/aspect-lib/components/sys-hostapi#2-querycalltree )|❌|返回EVM执行的呼叫树。|
|[sys.hostapi.trace.querystatechange](/develop/reference/aspect-lib/components/sys-hostapi#1-querystatechange)|❌|返回指定密钥的EVM执行状态更改。|


## 运行时上下文

方面运行时上下文封装了通过共识过程生成的数据。 在获得的运行时上下文
对象，通过指定相关上下文密钥来检索特定数据。 每个上下文密钥都与特定的
数据类型或信息。

### 用法

```javascript
const txUnHash = sys.hostApi.runtimeContext.get('tx.unsigned.hash');
const txUnHashData = Protobuf.decode<BytesData>(txUnHash, BytesData.decode);
sys.log('tx.unsigned.hash' + ' ' + uint8ArrayToHex(txUnHashData.data));


const extraEIPs = sys.hostApi.runtimeContext.get('env.extraEIPs');
const extraEIPsData = Protobuf.decode<IntArrayData>(extraEIPs, IntArrayData.decode);
for (let i = 0; i < extraEIPsData.data.length; i++) {
  sys.log(
          logPrefix +
          ' ' +
          'env.extraEIPs ' +
          i.toString(10) +
          ' ' +
          extraEIPsData.data[i].toString(10),
  );
}
```

### 钥匙表

|上下文密钥|价值类型|描述|
|----------------------------------------------|--------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|iscall|<a href ="/api/docs/classes/proto.BoolData.html" 目标="_blank"> booldata </a>|获得当前的交易是 **称呼** 或者 **发送**。 如果是 **称呼**，返回true。|
|tx.type|<a href ="/api/docs/classes/proto.UintData.html" 目标="_blank"> uintdata </a>|返回事务类型ID。 legacytxtype = 0x00 accessListTxType = 0x01 DynamicFeetXtype = 0x02 blobtxtype = 0x03|
|tx。链|<a href ="/api/docs/classes/proto.BytesData.html" 目标="_blank"> bytesdata </a>|返回交易的EIP155链ID。 返回值将始终为非nil。 对于不被重播保护的旧事务，返回值为零。|
|TX.AccessList|<a href ="/api/docs/classes/proto.EthAccessList.html" 目标="_blank"> ethaccesslist </a>|AccessListTX是EIP-2930访问列表交易的数据。|
|TX.Nonce|<a href ="/api/docs/classes/proto.UintData.html" 目标="_blank"> uintdata </a>|返回交易的发件人帐户nonce。|
|tx.gasprice|<a href ="/api/docs/classes/proto.BytesData.html" 目标="_blank"> bytesdata </a>|返回交易的气价。|
|tx.gas|<a href ="/api/docs/classes/proto.UintData.html" 目标="_blank"> uintdata </a>|返回交易的气体限制。|
|tx.gastipcap|<a href ="/api/docs/classes/proto.BytesData.html" 目标="_blank"> bytesdata </a>|返回每笔交易气体的GastiPCAP。|
|TX.GASFEECAP|<a href ="/api/docs/classes/proto.BytesData.html" 目标="_blank"> bytesdata </a>|返回交易的费用限额。|
|tx.to|<a href ="/api/docs/classes/proto.BytesData.html" 目标="_blank"> bytesdata </a>|返回交易的收件人地址。 用于合同创造交易，以返回零。|
|tx。值|<a href ="/api/docs/classes/proto.BytesData.html" 目标="_blank"> bytesdata </a>|返回交易的以太量。|
|tx.Data|<a href ="/api/docs/classes/proto.BytesData.html" 目标="_blank"> bytesdata </a>|返回交易的输入数据。|
|TX.Bytes|<a href ="/api/docs/classes/proto.BytesData.html" 目标="_blank"> bytesdata </a>|返回交易元帅二进制。|
|TX.Hash|<a href ="/api/docs/classes/proto.BytesData.html" 目标="_blank"> bytesdata </a>|返回交易哈希。|
|tx.unsigned.bytes|<a href ="/api/docs/classes/proto.BytesData.html" 目标="_blank"> bytesdata </a>|返回未签名的交易元帅二进制。|
|tx.unsigned.hash|<a href ="/api/docs/classes/proto.BytesData.html" 目标="_blank"> bytesdata </a>|返回未签名的交易哈希。|
|tx.sig.v|<a href ="/api/docs/classes/proto.BytesData.html" 目标="_blank"> bytesdata </a>|返回交易的V签名值。|
|tx.sig.r|<a href ="/api/docs/classes/proto.BytesData.html" 目标="_blank"> bytesdata </a>|返回交易的r签名值。|
|TX.Sig.s|<a href ="/api/docs/classes/proto.BytesData.html" 目标="_blank"> bytesdata </a>|返回交易的S签名值。|
|tx.从|<a href ="/api/docs/classes/proto.BytesData.html" 目标="_blank"> bytesdata </a>|返回交易的地址。|
|tx.index|<a href ="/api/docs/classes/proto.UintData.html" 目标="_blank"> uintdata </a>|返回当前块的事务索引。|
|block.header.parenthash|<a href ="/api/docs/classes/proto.BytesData.html" 目标="_blank"> bytesdata </a>|获取当前的块标题父母哈希。|
|Block.Header.Miner|<a href ="/api/docs/classes/proto.BytesData.html" 目标="_blank"> bytesdata </a>|获取当前的块标头矿工。|
|Block.Header.TransactionsRoot|<a href ="/api/docs/classes/proto.BytesData.html" 目标="_blank"> bytesdata </a>|获取当前的块TransactionsRoot哈希。|
|块|<a href ="/api/docs/classes/proto.UintData.html" 目标="_blank"> uintdata </a>|获取当前的块号。|
|Block.header.Timestamp|<a href ="/api/docs/classes/proto.UintData.html" 目标="_blank"> uintdata </a>|获取当前的块标头时间戳。|
|Env.Extraeips|<a href ="/api/docs/classes/proto.IntArrayData.html" 目标="_blank"> intarraydata </a>|检索'的EVM模块参数 **extra_eips**'：定义VM.Config的额外EIP。|
|Env.EnableCreate|<a href ="/api/docs/classes/proto.BoolData.html" 目标="_blank"> booldata </a>|检索'的EVM模块参数 **enable_create**'：切换使用vm.create函数的过渡。|
|Env.EnableCall|<a href ="/api/docs/classes/proto.BoolData.html" 目标="_blank"> booldata </a>|检索'的EVM模块参数 **enable_call**'：切换使用vm.call函数的过渡。|
|env.lowerAllowunProtectedTXS|<a href ="/api/docs/classes/proto.BoolData.html" 目标="_blank"> booldata </a>|检索'的EVM模块参数 **laster_unprotected_txs**'：定义是否可以在状态计算机上执行受重播保护（即非EIP155 //签名）的交易。|
|Env.Chain.Chainid|<a href ="/api/docs/classes/proto.UintData.html" 目标="_blank"> uintdata </a>|检索以太坊链配置ID。|
|Env.Chain.HomesteadBlock|<a href ="/api/docs/classes/proto.UintData.html" 目标="_blank"> uintdata </a>|检索'**HOMESTEAD_BLOCK**'：switch（no no fork，0 =已经是宅基地）|
|Env.Chain.Daoforkblock|<a href ="/api/docs/classes/proto.UintData.html" 目标="_blank"> uintdata </a>|检索'**dao_fork_block**'：对应于thedao硬盘开关块（无叉）|
|Env.Chain.daoforksupport|<a href ="/api/docs/classes/proto.BoolData.html" 目标="_blank"> booldata </a>|检索'**dao_fork_support**'：定义节点是支持还是反对DAO硬叉|
|Env.Chain.EIP150Block|<a href ="/api/docs/classes/proto.UintData.html" 目标="_blank"> uintdata </a>|检索'**EIP150_Block**'：EIP150实现汽油价格变化（https://github.com/ethereum/eips/issues/150）|
|Env.Chain.EIP155Block|<a href ="/api/docs/classes/proto.UintData.html" 目标="_blank"> uintdata </a>|检索'**eip155_block**'。|
|Env.Chain.EIP158Block|<a href ="/api/docs/classes/proto.UintData.html" 目标="_blank"> uintdata </a>|检索'**eip158_block**'。|
|Env.Chain.ByzantiumBlock|<a href ="/api/docs/classes/proto.UintData.html" 目标="_blank"> uintdata </a>|检索'**Byzantium_block**'：拜占庭开关块（no no fork，0 =已经在拜占庭上）|
|Env.Chain.Constantinopleblock|<a href ="/api/docs/classes/proto.UintData.html" 目标="_blank"> uintdata </a>|检索'**Constantinople_block**'：君士坦丁堡开关块（无叉，0 =已经激活）|
|Env.Chain.Petersburgblock|<a href ="/api/docs/classes/proto.UintData.html" 目标="_blank"> uintdata </a>|检索'**Petersburg_block**'：彼得斯堡开关块（NON NO fork，0 =已经激活）|
|env.chain.istanbulblock|<a href ="/api/docs/classes/proto.UintData.html" 目标="_blank"> uintdata </a>|检索'**istanbul_block**'：伊斯坦布尔开关块（NON NO FORK，0 =已经在伊斯坦布尔上）|
|Env.Chain.MuirglacierBlock|<a href ="/api/docs/classes/proto.UintData.html" 目标="_blank"> uintdata </a>|检索'**muir_glacier_block**'：EIP-2384（炸弹延迟）开关块（no no fork，0 =已经激活）。|
|Env.Chain.BerlinBlock|<a href ="/api/docs/classes/proto.UintData.html" 目标="_blank"> uintdata </a>|检索'**berlin_block**'：柏林开关块（nil = no fork，0 =已经在柏林上）|
|Env.Chain.LondonBlock|<a href ="/api/docs/classes/proto.UintData.html" 目标="_blank"> uintdata </a>|检索'**伦敦_Block**'：伦敦开关块（nil = no fork，0 =已经在伦敦上）|
|Env.Chain.ArrowGlacierBlock|<a href ="/api/docs/classes/proto.UintData.html" 目标="_blank"> uintdata </a>|检索'**arrow_glacier_block**'：EIP-4345（炸弹延迟）开关块（nil = no fork，0 =已经激活）|
|Env.Chain.GrayGlacierBlock|<a href ="/api/docs/classes/proto.UintData.html" 目标="_blank"> uintdata </a>|检索'**gray_glacier_block**'：EIP-5133（炸弹延迟）开关块（nil = no fork，0 =已经激活）|
|Env.Chain.Mergenetsplitblock|<a href ="/api/docs/classes/proto.UintData.html" 目标="_blank"> uintdata </a>|检索'**MERGE_NETSPLIT_BLOCK**'：合并后的虚拟叉作为网络分离器。|
|Env.Chain.Shanghaitime|<a href ="/api/docs/classes/proto.UintData.html" 目标="_blank"> uintdata </a>|检索'**上海**'：上海开关时间（nil = no fork，0 =已经在上海上）。|
|Env.Chain.Cancuntime|<a href ="/api/docs/classes/proto.UintData.html" 目标="_blank"> uintdata </a>|检索'**Cancuntime**'：坎昆开关时间（nil = no fork，0 =已经在坎昆上）。|
|env.chain.praguetime|<a href ="/api/docs/classes/proto.UintData.html" 目标="_blank"> uintdata </a>|检索'**Praguetime**'：布拉格开关时间（nil = no fork，0 =已经在布拉格上）。|
|env.consensusparams.block.maxgas|<a href ="/api/docs/classes/proto.IntData.html" 目标="_blank"> intdata </a>|检索每个块的最大气体。|
|env.consensusparams.block.maxbytes|<a href ="/api/docs/classes/proto.IntData.html" 目标="_blank"> intdata </a>|在字节中检索最大块大小。|
|Env.Consensusparams.evence.maxageduration|<a href ="/api/docs/classes/proto.IntData.html" 目标="_blank"> intdata </a>|检索最大年龄的持续时间。它应与应用程序相对应 "unbonding period" 或其他类似的处理机制。|
|env.Consensususparams.vence.maxagenumblocks|<a href ="/api/docs/classes/proto.IntData.html" 目标="_blank"> intdata </a>|计算此计算的基本公式是：最大化 / {平均块时间}。|
|Env.Consensusparams.vende.maxbytes|<a href ="/api/docs/classes/proto.IntData.html" 目标="_blank"> intdata </a>|检索可以在单个块中投入的字节中总证据的最大大小。|
|Env.Consensususparams.validator.pubkeytypes|<a href ="/api/docs/classes/proto.StringArrayData.html" 目标="_blank"> StringArrayData </a>|限制验证器可以使用的公共密钥类型。|
|Env.Consensusparams.Appversion|<a href ="/api/docs/classes/proto.UintData.html" 目标="_blank"> uintdata </a>|ABCI应用程序版本。|
|expact.id|<a href ="/api/docs/classes/proto.BytesData.html" 目标="_blank"> bytesdata </a>|返回当前方面ID。|
|expact.version|<a href ="/api/docs/classes/proto.UintData.html" 目标="_blank"> uintdata </a>|返回当前的方面版本。|