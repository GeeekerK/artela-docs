# 以太坊

>提供用于访问和与智能合约互动的功能，包括ABI（应用程序二进制界面）处理，以及针对各种数据类型的编码和解码。

## 1. 功能

> 定义了几种用于ABI操作的方法，即以太坊名称空间函数列表。

### 1. abiencode

> ABI编码

<！ -  @formatter：off->
```typescript
   function abiEncode(method: string, types: Type[]): string
```
<！ -  @formatter：on->>

* 范围
*字符串：方法
*类型：以ethereum.type数组
*返回
* ABI编码十六进制字符串

* 例子
<！ -  @formatter：off->
```typescript
import {
  ethereum
} from "@artela/aspect-libs";

{
  let payload = ethereum.abiEncode('store', [num]);
}
```
<！ -  @formatter：on->>

### 2.放射术

> 解析方法签名

<！ -  @formatter：off->
```typescript
   function parseMethodSig(calldata: Uint8Array): string
```
<！ -  @formatter：on->>

* 范围
* uint8array：方法名称字节。
*返回
*字符串：字符串方法名称

* 例子
<！ -  @formatter：off->
```typescript
import {ethereum} from "@artela/aspect-libs";

{
    // parseMethodSig to string  "add" 
  let add = ethereum.parseMethodSig([4f2be91f]);
}
```
<！ -  @formatter：on->>

### 3. ComputeMethodsig

> 计算方法签名

<！ -  @formatter：off->
```typescript
   function computeMethodSig(method: string): string
```
<！ -  @formatter：on->>

* 范围
*字符串：方法名称
*返回
*字符串：方法签名十六进制字符串

* 例子
<！ -  @formatter：off->
```typescript
import {ethereum} from "@artela/aspect-libs";

{
    // parseMethodSig to string  "f2be91f" 
  let addSig = ethereum.computeMethodSig("add");
}
```
<！ -  @formatter：on->>

---

## 2. Types

> 在智能合约和数据类型转换，编码和解码操作中定义数据类型。

1. <a href ="/api/docs/classes/common.ethereum.Address.html" 目标="_blank">以太坊</a>
2. <a href ="/api/docs/classes/common.ethereum.ArraySlice.html" 目标="_blank">以太坊。arrayslice</a>
3. <a href ="/api/docs/classes/common.ethereum.Boolean.html" 目标="_blank">以太坊。博林</a>
4. <a href ="/api/docs/classes/common.ethereum.ByteArray.html" 目标="_blank">以太坊bytearray </a>
5. <a href ="/api/docs/classes/common.ethereum.Bytes.html" 目标="_blank">以太坊Bytes </a>
6. <a href ="/api/docs/classes/common.ethereum.BytesN.html" 目标="_blank">以太坊Bytesn </a>
7. <a href ="/api/docs/classes/common.ethereum.Int.html" 目标="_blank">以太坊</a>
8. <a href ="/api/docs/classes/common.ethereum.Number.html" 目标="_blank">以太坊。名称</a>
9. <a href ="/api/docs/classes/common.ethereum.String.html" 目标="_blank">以太坊</a>
10.<a href ="/api/docs/classes/common.ethereum.Tuple.html" 目标="_blank">以太坊</a>
11.<a href ="/api/docs/classes/common.ethereum.Uint.html" 目标="_blank">以太坊</a>


## 3. UseCases

生成函数的ABI编码 `function execute(address account,uint64 num,bytes calldata data) public ` 

```typescript

令ContractAddress ="0x5B38Da6a701c568545dCfcB03FcB875f56beddC4" 
令txdata ="0x112233" 

const calldata = ethereum.abiencode（'execute'，[
ethereum.address.fromhexstring（Contractaddress），
以太坊。number.fromu64（0），，
ethereum.bytes.fromhexstring（txdata），
）;
```