# sys.hostapi

> 提供与WASM Runtimes通信的主机API，包括加密货币，UTILS，SETADEDB和区块链运行时
> 语境。
> 如每种方法中所述，方法输入和退出参数由Protobuf编码和解码。

## 进口

<！ -  @formatter：off->
```javascript
{
    sys
} from "@artela/aspect-libs";
```
<！ -  @formatter：on->>

## sys.hostapi.runtimecontext

> 此API用于查询，更新和删除运行时上下文。
> 此处提供的低级API可能会报告未经数据访问授权的错误，因此请谨慎使用它。

### 1.获取上下文

> 通过字符串键获取上下文

<！ -  @formatter：off->
```typescript
public get(key: string): Uint8Array
```
<！ -  @formatter：on->>

* 范围
*键：字符串，现有的支持密钥是； 每个联接点都有对访问密钥的访问权限控制，例如： [Preftractcall键盘的](/develop/reference/aspect-lib/join-points/pre-contract-call#key-table) 上下文密钥。

*返回
*数据：uint8array，返回的数据类型取决于键
查询，每个联接点的每个键表都有一个列的列 "Value Type" 显示该密钥的相应返回类型。 例如： [Preftractcall键盘的](/develop/reference/aspect-lib/join-points/pre-contract-call#key-table) 价值类型。

* 例子

<！ -  @formatter：off->
```typescript
  import {
          sys
  } from "@artela/aspect-libs";
  import { Protobuf } from "as-proto/assembly/Protobuf";
  
  {    
      // get key
      const number = sys.hostApi.runtimeContext.get('block.header.number');
      if (number){
          // decode get result
          const numberData = Protobuf.decode<UintData>(number, UintData.decode);
      }
  }
```
<！ -  @formatter：on->>

## sys.hostapi.AspectTransientStorage

> 方面上下文本质上是一种瞬态存储，其生命周期仅是当前交易。 方面上下文可以
> 用于启用方面和智能合约之间的双向通信。

### 1.获取

> 通过密钥获取方面上下文。

<！ -  @formatter：off->
```typescript

public get(key: string, aspectId: Uint8Array = new Uint8Array(0)): Uint8Array

```
<！ -  @formatter：on->>

* 范围
*键：字符串上下文密钥
*前缀：键的uint8array前缀。 选修的。 默认方面ind
*返回
*值：uint8array上下文值
* 例子

<！ -  @formatter：off->
```typescript
import {
  sys
} from "@artela/aspect-libs";

let key="test" 
// inline call
let value = sys.hostApi.aspectTransientStorage.get(key);
```
<！ -  @formatter：on->>

### 2.设置

> 通过密钥获取方面上下文。

<！ -  @formatter：off->
```typescript

public set(key: string, value: Uint8Array): void

```
<！ -  @formatter：on->>

* 范围
*值：t设置值。 支持的仿制药类型为：U8 i8 U16 i16 U32 i32 U64 i64 string uint8array bigint
*返回
* 空白
* 例子

<！ -  @formatter：off->
```typescript
import {
  sys,stringToUint8Array
} from "@artela/aspect-libs";
{
  let key="test";
  let data= stringToUint8Array( "value");
  let value = sys.hostApi.aspectTransientStorage.set(key,data);
}
```
<！ -  @formatter：on->>

## sys.hostapi.AspectProperty

### 1.获取

> 获取方面属性。

<！ -  @formatter：off->
```typescript
public get(key: string): Uint8Array
```
<！ -  @formatter：on->>

* 范围
*密钥：字符串属性密钥
*返回
*值：uint8array属性值。
* 例子

<！ -  @formatter：off->
```typescript
import {sys} from "@artela/aspect-libs";

{
  let key="test";
  let value = sys.hostApi.aspectProperty.get(key);
}
```
<！ -  @formatter：on->>


## sys.hostapi.Aspectstate

### 1.获取

> 通过键获取方面状态。

<！ -  @formatter：off->
```typescript

public get(key: string): Uint8Array

```
<！ -  @formatter：on->>

* 范围
*键：字符串状态键
*返回
*值：uint8array状态值
* 例子

<！ -  @formatter：off->
```typescript
import {
  sys
} from "@artela/aspect-libs";

let key="test" 
// inline call
let value = sys.hostApi.aspectState.get(key);
```
<！ -  @formatter：on->>

### 2.设置

> 通过键获取方面状态。

<！ -  @formatter：off->
```typescript

public set(key: string, value: Uint8Array): void

```
<！ -  @formatter：on->>

* 范围
*键：字符串状态键
*值：uint8array状态值
*返回
* 空白
* 例子
<！ -  @formatter：off->
```typescript
import {
  sys,stringToUint8Array
} from "@artela/aspect-libs";
{
  let key="test" 
  let data= stringToUint8Array("value");
  let value = sys.hostApi.aspectState.set(key,data);
}
```
<！ -  @formatter：on->>


## sys.hostapi.crypto

### 1. Keccak

> KECCAK256计算并返回输入数据的Keccak256哈希。

<！ -  @formatter：off->
```typescript
    public keccak(data: Uint8Array): Uint8Array
```
<！ -  @formatter：on->>

* 范围
* uint8array：数据
*返回
* uint8array：计算结果
* 例子

<！ -  @formatter：off->
```typescript
    let data = sys.hostApi.crypto.keccak(sys.utils.stringToUint8Array("test"));
```
<！ -  @formatter：on->>

### 2. SHA256

> 返回数据的SHA256校验和数据。

<！ -  @formatter：off->
```typescript
    public sha256(data: Uint8Array): Uint8Array
```
<！ -  @formatter：on->>

* 范围
* uint8array：数据
*返回
* uint8array：计算结果
* 例子

<！ -  @formatter：off->
```typescript
    let data = sys.hostApi.crypto.sha256(sys.utils.stringToUint8Array("test"));
```
<！ -  @formatter：on->>

### 3. Ripemd160

> 返回RIPEMD160摘要数据。

<！ -  @formatter：off->
```typescript
    public ripemd160(data: Uint8Array): Uint8Array
```
<！ -  @formatter：on->>

* 范围
* uint8array：数据
*返回
* uint8array：计算结果
* 例子

<！ -  @formatter：off->
```typescript
    let data = sys.hostApi.crypto.Uint8Array(sys.utils.stringToUint8Array("test"));
```
<！ -  @formatter：on->>

### 4. Ecretover

> 返回数据编码。

<！ -  @formatter：off->
```typescript
    public ecRecover(hash: string, v: BigInt, r: BigInt, s: BigInt): string
```
<！ -  @formatter：on->>

* 范围
* uint8array：数据
*返回
* uint8array：计算结果
* 例子

<！ -  @formatter：off->
```typescript

let r="8ddbe43ca7d0b8df7a7a6e3c7843f110863542531262ee3958ae5739db5c8eff";
let s="16b68cdef6ab525c8fa96164ff9a471d5b1d8b6d070810a90efd245583045e99";
let v="1b";
let msgHash="b893fcb3622cea25bc02a2491d3c8464df619d2ca0319203f14325208556fc5e";

const recoverResult = sys.hostApi.crypto.ecRecover(
    msgHash,
    BigInt.fromString(v, 16),
    BigInt.fromString(r, 16),
    BigInt.fromString(s, 16),
);

return hexToUint8Array(recoverResult);

```
<！ -  @formatter：on->>

---

## sys.hostApi.evmCall

### 1. staticCall

> 立即执行新消息调用，而无需在块链上创建交易。

<！ -  @formatter：off->
```typescript
public staticcall（请求：staticcallrequest）：staticcallresult
```
<！ -  @formatter：on->>

* 范围
* <a href ="/api/docs/classes/proto.StaticCallRequest.html" 目标="_blank"> staticcallrequest </a>：请求
*返回
* <a href ="/api/docs/classes/proto.StaticCallResult.html" 目标="_blank"> staticcallresult </a>：调用结果
* 例子

<！ -  @formatter：off->
```typescript
const来自= sys.aspect.property.get <uint8array>（'from'）;
const to = sys.aspect.property.get <uint8array>（'to'）;
const data = sys.aspect.property.get <uint8array>（'data'）;

const staticCallRequest = new staticCallRequest（从，到数据，1000000000）;
const staticcallresult = sys.hostapi.evmcall.staticcall（staticCallRequest）;

```
<！ -  @formatter：on->>

### 2. jitCall

> 立即执行新消息调用，而无需在块链上创建交易。

<！ -  @formatter：off->
```typescript
公共jitcall（请求：jitinherentrequest）：jitinherentresponse
```
<！ -  @formatter：on->>

* 范围
* <a href ="/api/docs/classes/proto.JitInherentRequest.html" 目标="_blank"> jitinherentrequest </a>：请求
*返回
* <a href ="/api/docs/classes/proto.JitInherentResponse.html" 目标="_blank"> jitinherentresponse </a>：调用结果
* 例子

<！ -  @formatter：off->
```typescript
const sender = sys.aspect.property.get <uint8array>（'from'）;
const to = sys.aspect.property.get <uint8array>（'to'）;
const calldata = sys.aspect.property.get <uint8array>（'data'）;
const request = jitcallbuilder.simple（发送者，to，calldata）.build（）;
//提交JIT电话
const响应= sys.hostapi.evmcall.jitcall（请求）;
```
<！ -  @formatter：on->>

## sys.hostApi.stateDb

### 1. balance

> 从给定地址检索余额或如果找不到对象

<！ -  @formatter：off->
```typescript
公共余额（addr：uint8array）：uint8array
```
<！ -  @formatter：on->>

* 范围
* addr：uint8array帐户地址
* 返回
*结果：uint8array余额值
* 例子

<！ -  @formatter：off->
```typescript
const合同= hextouint8array（ "0x5B38Da6a701c568545dCfcB03FcB875f56beddC4" ）
const Balance = sys.hostapi.statedb.balance（合同）;
```
<！ -  @formatter：on->>

### 2. stateAt

> 从给定帐户的存储trie检索值。

<！ -  @formatter：off->
```typescript
公共雕像（addr：uint8array，哈希：uint8array）：uint8array
```
<！ -  @formatter：on->>

* 范围
*地址：uint8array帐户地址
*哈希：uint8array插槽键哈希
* 返回
* uint8array：状态价值
* 例子

<！ -  @formatter：off->
```typescript
const合同= hextouint8array（ "0x5B38Da6a701c568545dCfcB03FcB875f56beddC4" ）
const hash = stringtouint8array（ "aabbcc6a701c568545dCfcB03FcB875f56beaabb" ）
令状态= sys.hostapi.statedb.stateat（合同，哈希）；
```
<！ -  @formatter：on->>

### 3. hasSuicided

> 如果合同是在当前TXS中自杀的，则返回。

<！ -  @formatter：off->
```typescript
公共Hassuicided（addr：uint8array）：bool
```
<！ -  @formatter：on->>

* 范围
*地址：uint8array帐户地址
* 返回
*布尔：自杀
* 例子

<！ -  @formatter：off->
```typescript
const合同= hextouint8array（ "0x5B38Da6a701c568545dCfcB03FcB875f56beddC4" ）
const hassuicided = sys.hostapi.statedb.hassuicided（合同）;
```
<！ -  @formatter：on->>

### 4. codeHash

> 返回帐户的代码哈希。

<！ -  @formatter：off->
```typescript
公共Codehash（addr：uint8array）：uint8array
```
<！ -  @formatter：on->>

* 范围
*地址：uint8array帐户地址
* 返回
* uint8array：退款计数器的当前值
* 例子

<！ -  @formatter：off->
```typescript
const合同= hextouint8array（ "0x5B38Da6a701c568545dCfcB03FcB875f56beddC4" ）
让哈希= sys.hostapi.statedb.codehash（合同）;
```
<！ -  @formatter：on->>

### 5. nonce

> 返回帐户的nonce，0如果不存在。

<！ -  @formatter：off->
```typescript
公共nonce（addr：uint8array）：u64
```
<！ -  @formatter：on->>

* 范围
*地址：uint8array帐户地址
* 返回
*（i64）：nonce
* 例子

<！ -  @formatter：off->
```typescript
const合同= hextouint8array（ "0x5B38Da6a701c568545dCfcB03FcB875f56beddC4" ）
令nonce = sys.hostapi.statedb.nonce（合同）;
```
<！ -  @formatter：on->>

### 6. codeSize

> 返回帐户的代码大小。

<！ -  @formatter：off->
```typescript
公共代码（addr：uint8array）：U64
```
<！ -  @formatter：on->>

* 范围
*地址：uint8array帐户地址
* 返回
*（i64）：nonce
* 例子

<！ -  @formatter：off->
```typescript
const合同= hextouint8array（ "0x5B38Da6a701c568545dCfcB03FcB875f56beddC4" ）
令size = sys.hostapi.statedb.codesize（合同）;
```
<！ -  @formatter：on->>

## sys.hostApi.trace

> 各个方面可以追踪智能合约状态的变化，但是通过ASOLC编译器生成的额外opcodes和IR方法促进了此跟踪。

### 1. queryStateChange

> 查询状态更改。

<！ -  @formatter：off->
```typescript
public querystatechange（查询：statechangequery）：uint8array
```
<！ -  @formatter：on->>

* 范围
*查询：statechangequery;
*帐户：uint8array; 帐户十六进制字符串，例如0xxabccccxxxxddddd
* stateVarname：字符串; 状态变量名称，例如“ honeypot.balances”
*索引：[] uint8array; 指数可选，，
* 返回
*数据：uint8array; <a href ="/api/docs/classes/proto.EthCallTree.html" 目标="_blank"> ethstatechangeindices </a>或<a href ="/api/docs/classes/proto.EthStateChange.html" 目标="_blank"> EthStateChange </a>
* 例子

<！ -  @formatter：off->
```typescript
const account = hextouint8array（ "0x5B38Da6a701c568545dCfcB03FcB875f56beddC4" ）；
const statevar ='honeypot.balances';

const query = new StateChangeQuery（account，statevar，[]）;
const响应= sys.hostapi.trace.querystatechange（query）;
const indicesResult = protobuf.decode <EthStateChangeIndices>（响应，ethstatechangeindices.decode）;
//或设置索引
const ChangeQuery = new StateChangeQuery（account，statevar，indicesResult.indices）;
const ResponseChange = sys.hostapi.trace.querystatechange（ChangeQuery）;
const ethstatechange = protobuf.decode <Ethstatechange>（响应，ethstatechange.decode）;
```
<！ -  @formatter：on->>

### 2. queryCallTree

> 查询跟踪呼叫树通过消息索引。

<！ -  @formatter：off->
```typescript
public queryCalltree（查询：calltreequery）：uint8array
```
<！ -  @formatter：on->>

* 范围
*查询：<a href ="/api/docs/classes/proto.CallTreeQuery.html" 目标="_blank"> calltreequery </a>
* callIDX：i64; 呼叫索引，如果传递-1，则获得整个呼叫树； 否则，它将返回给定索引的呼叫消息。 喜欢：10
* 返回
*数据：uint8array; <a href ="/api/docs/classes/proto.EthCallTree.html" 目标="_blank"> ethcalltree </a>或<a href ="/api/docs/classes/proto.EthCallMessage.html" 目标="_blank"> ethcallmessage </a>
* 例子

<！ -  @formatter：off->
```typescript
var calltreequery = new calltreequery（-1）;
让响应= sys.hostapi.trace.querycalltree（calltreequery）
//如果查询索引==  -  1结果ethcalltree
const calltree = protobuf.decode <thcalltree>（响应，ethcalltree.decode）;

//或获取一条呼叫消息

var calltreequery = new calltreequery（2）;
让响应2 = sys.hostapi.trace.querycalltree（calltreequery）
//如果查询索引==呼叫消息索引，则结果EthCallMessage
const callmessage = protobuf.decode <EthCallMessage>（response2，ethcallmessage.decode）;
```
<！ -  @formatter：on->>