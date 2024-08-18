---
sidebar_position: 3
---

# Artela Web3.js

`@artela/web3` 扩展了以太坊的 web3.js 1.9.x，支持 Aspect 操作。

此包保留了与原始 [web3.js](https://web3js.readthedocs.io/en/v1.9.0/getting-started.html) 的完全兼容性，并进行了细微调整。

## 安装

要将此包集成到您的 JavaScript 或 TypeScript 项目中，请运行以下命令：

```bash
npm install @artela/web3 --save
```

## 快速入门

在您的 node.js 项目上导入相关库。Web3 类是一个总括包，用于容纳所有以太坊相关模块。

```javascript
import Web3 from '@artela/web3';
```

新实例

```javascript
var web3 = new Web3('http://127.0.0.1:8545');
```

Deploy aspect and to bind a contract.

```typescript
const fs = require('fs')
const Web3 = require("@artela/web3");

// new web3 instance
let web3 = new Web3("http://127.0.0.1:8545");

let privateKey = "..."
// add your private key and to add wallet
const sender = web3.eth.accounts.privateKeyToAccount(privateKey);
web3.eth.accounts.wallet.add(sender.privateKey);

//Load Aspect binary, like fs.readFileSync('./build/release.wasm', {encoding: "hex"});
let aspectBytecode = '0x... ';

//new Aspect Instance
let aspect = new web3.atl.Aspect();

const deploy = await aspect.deploy({
    data: aspectBytecode,
    properties: [ // <-- property to initialize the aspect, key-value pairs
        {'key': 'owner', 'value': accounts[0]}
    ],
    paymaster: account.address,
    proof: '0x0',
    joinPoints: ["PreContractCall", "PostContractCall"]
});

let aspectCore = web3.atl.aspectCore();
const tx = {
    from: sender.address,
    data: deploy.encodeABI(),
    to: aspectCore.options.address,
    gasPrice: 11000,
    gas: 10000
}
const signedTx = await web3.atl.accounts.signTransaction(tx, sender.privateKey);
const aspectReceipt = await web3.atl.sendSignedTransaction(signedTx.rawTransaction);
console.log(aspectReceipt);

// smart Contract abi
let abi = {...};

// smart Contract Addres
let contractAddress = "0x778838Da6a701c568545dCfcB03FcB875f56beaabb";

// do aspect bind
let contract = new web3.eth.Contract(abi, contractAddress);

// Aspect address
let aspectId = aspectReceipt.options.address;

// bind the smart contract with aspect
let bind = await contract.bind({
    priority: 1,
    aspectId: aspectId,
    aspectVersion: 1,
})

//create transaction
let bindTx = {
    from: sender.address,
    data: bind.encodeABI(),
    to: aspectCore.options.address,
    gasPrice: 9000000,
    gas: 9000000
}

let signedBindTx = await web3.eth.accounts.signTransaction(bindTx, sender.privateKey);
let bindReceipt = web3.eth.sendSignedTransaction(signedBindTx.rawTransaction);
console.log(bindReceipt);

```

## web3.atl.Aspect

> ``web3.atl.Aspect`` 函数允许您为此实例创建代理 Contract 类型，因为 Contract 的提供程序存储为类成员而不是实例变量。

```javascript
import Web3 from '@artela/web3';

// 从 aspectsId 新建一个方面实例
// Aspect：new (address?: string, options?: AspectOptions) => Aspect;
let aspects = new Web3.atl.Aspect("0x5B38Da6a701c568545dCfcB03FcB875f56beddC4");

```

``Aspect`` - 一个 Aspect 实例。

- ``aspect.address``：方面实例的地址。
- ``aspect.options``：方面实例的选项，类型为 AspectOptions。

``AspectOptions`` - AspectOptions 实例。

- ``aspectOption.from``：用于合约调用的发送方。

- ``aspectOption.gasPrice``：用于合约调用的 gas 价格。

- ``aspectOption.gas``：用于合约调用的 gas。

- ``aspectOption.data``：aspect WASM 代码。

使用 Aspect 实例，您可以执行以下操作：

* [Deploy a Aspect to blockchain](/develop/client/artela-web3.js#deploy)
* [Upgrade a Aspect to blockchain](/develop/client/artela-web3.js#upgrade)
* [Bind Aspect to Contract](/develop/client/artela-web3.js#bind)
* [UnBind Aspect and Contract](/develop/client/artela-web3.js#unbind)
* [Send/Call Aspect Operation](/develop/client/artela-web3.js#operation)
* [Get Aspect Version](/develop/client/artela-web3.js#versionof)
* [Get Bound Aspect Address](/develop/client/artela-web3.js#aspectsof)
* [Get Bound Account Addresses](/develop/client/artela-web3.js#boundaddressesof)

### 部署

> 在部署 Aspect 时，Aspect 二进制文件会存储在系统合约中，同时还会存储其他详细信息，例如版本、属性和其他相关信息。

#### 参数

- **部署操作**
    - data: 切面的二进制十六进制字符串，**必选**。
    - properties: KVPair[]，**可选**，
    如：`[{ 'key': 'owner', 'value': accounts[0] },{ 'key': 'key1', 'value': 'value1' }]`
    - joinPoints: 指定哪些连接点生效，在列表["VerifyTx","PreTxExecute","PreContractCall","PostContractCall","PostTxExecute"]中可多选填写。
    如`["PreContractCall","PostContractCall"]`
    - paymaster: 支付主地址。**可选**
    - proof: 证明十六进制数据。**可选**

#### 例子

<!-- @formatter:off -->

```jsx
// new Aspect Instance
let aspect = new web3.atl.Aspect();

// Load Aspect binary
let aspectBytecode = '0x ....'
// create deploy instance
const deploy = await aspect.deploy({
    data: aspectBytecode,
    properties: [ // <-- property to initialize the aspect, key-value pairs
        { 'key': 'owner', 'value': accounts[0] }
    ],
    paymaster: account.address,
    proof: '0x0',
    joinPoints:["PreContractCall","PostContractCall"]
});
const tx = {
    data: deploy.encodeABI(),
    ...
}
const signedTx = await web3.atl.accounts.signTransaction(tx, sender.privateKey);
return await web3.atl.sendSignedTransaction(signedTx.rawTransaction);
```
<!-- @formatter:on -->

### 升级

> Aspect Upgrade 根据传入的 AspectId 更新现有的“二进制数据”、“连接点”和“属性”。此
> 操作将覆盖原始值；

#### 参数

- **部署操作**
    - data: 对应的二进制十六进制字符串，会覆盖原有值。**必须**。
    - properties: KVPair[]，会覆盖原有值。**可选**，
    如：`[{ 'key': 'owner', 'value': accounts[0] },{ 'key': 'key1', 'value': 'value1' }]`
    - joinPoints: 指定哪些连接点生效，在列表 ["VerifyTx","PreTxExecute","PreContractCall","PostContractCall","PostTxExecute"] 中可多选填写。
    如 `["PreContractCall","PostContractCall"]`

#### 例子

<!-- @formatter:off -->
```jsx
//Load Aspect binary
let aspectBytecode = '0x...'

//new Aspect Instance by aspectId
let aspect = new web3.atl.Aspect("0x5B38Da6a701c568545dCfcB03FcB875f56beddC4");
// set update parameter
const upgrade = await aspect.upgrade({
    data: aspectBytecode,
    properties: [ // <-- property to initialize the aspect, key-value pairs
        { 'key': 'owner', 'value': accounts[0] }
    ],
    joinPoints:["PreContractCall","PostContractCall"]
});
const tx = {
    data: upgrade.encodeABI(),
    ...
}
const signedTx = await web3.atl.accounts.signTransaction(tx, account.privateKey);
return await web3.atl.sendSignedTransaction(signedTx.rawTransaction);
```
<!-- @formatter:on -->

:::note
升级后的 Aspect 将在下一个区块中激活，其版本将增加 1。
:::

### 绑定

> Aspect 绑定到合约是在 Artela 网络中建立 Aspect 与智能合约之间连接的过程。该关系在系统合约中维护。
> 该函数允许您将合约与某个方面绑定：transactionHash、收据。

#### 参数

- **BindAspectOptions**
    - priority: 切面的二进制十六进制字符串，会覆盖原有值。**可选**。
    - aspectsId: 切面地址。**必须**，如：`0x5B38Da6a701c568545dCfcB03FcB875f56beddC4`
    - aspectsVersion: 指定哪些连接点生效，在列表["VerifyTx","PreTxExecute","PreContractCall","PostContractCall","PostTxExecute"]中可多选填写。
    如`["PreContractCall","PostContractCall"]`

#### 例子

<!-- @formatter:off -->
```javascript

// smart Contract abi
let abi={...};

// smart Contract Addres
let contractAddress="0x778838Da6a701c568545dCfcB03FcB875f56beaabb";

// do aspect bind
let contract = new web3.eth.Contract(abi, contractAddress);

// Aspect address
let aspectId ="0x5B38Da6a701c568545dCfcB03FcB875f56beddC4";

// bind the smart contract with aspect
let bind = await contract.bind({
    priority: 1,
    aspectId: aspectId,
    aspectVersion: 1,
})
//create transaction
let aspectCore = web3.atl.aspectCore();
let tx = {
    from: sender.address,
    data: bind.encodeABI(),
    to: aspectCore.options.address,
    gasPrice:9000000,
    gas: 9000000
}

// sender privateKey ....
let privateKey="0x..."

let signedTx = await web3.eth.accounts.signTransaction(tx, privateKey);
let receipt= await web3.eth.sendSignedTransaction(signedTx.rawTransaction);

```
<!-- @formatter:on -->

:::注意
绑定交易的发送者必须是智能合约的所有者。Artela 系统合约将检查合约的 `isOwner(address)` 方法，以确保发送者有权绑定方面。
:::

### 解除绑定

> 方面可以从智能合约中分离。只有智能合约的所有者（其地址必须通过
> isOwner(address): bool 验证）才能启动解除绑定。

#### 参数

- **UnBindAspectOptions**

    - aspectsId：方面地址。**需要**，例如：`0x5B38Da6a701c568545dCfcB03FcB875f56beddC4`
    - contract：合约地址。**需要**，例如：`0x5B38Da6a701c568545dCfcB03FcB875f56beddC4`

#### 例子

<!-- @formatter:off -->
```javascript
// smart Contract Addres
let contract="0x778838Da6a701c568545dCfcB03FcB875f56beaabb";

// Aspect address
let aspectId ="0x5B38Da6a701c568545dCfcB03FcB875f56beddC4";

const aspectContract = new web3.atl.aspectCore();
// bind the smart contract with aspect
const bind = await aspectContract.methods.unbind(
    aspectId,
    contract
)

let tx = {
    data: bind.encodeABI(),
    ...
}

let privateKey="..."
const signedTx = await web3.eth.accounts.signTransaction(tx, privateKey);

const receipt= await web3.eth.sendSignedTransaction(signedTx.rawTransaction);
```
<!-- @formatter:on -->

:::注意
解除绑定交易的发送者必须是智能合约的所有者。Artela 系统合约将检查合约的 `isOwner(address)` 方法，以确保发送者有权绑定该方面。
:::

### 操作

> 操作方面类似于智能合约，专门响应由外部拥有的账户 (EOA) 发起的交易。这包括由合约交互触发的交易。值得注意的是，这些连接点也会在跨合约交互的情况下激活。

#### 参数

-aspectId：aspect.aspectAddress，**require**。
-codedArgs：十六进制字符串。**require**，如：`0x5B38Da6a701c568545dCfcB03FcB875f`

#### 示例

<!-- @formatter:off -->
```javascript

let privateKey="..."

var aspectCore = web3.atl.aspectCore();
// Aspect address
let aspectId ="0x5B38Da6a701c568545dCfcB03FcB875f56beddC4";
// hex string
let operationData ="0x01..";

const aspectInstance = new web3.atl.Aspect(aspectId);
const encodeABI = aspectInstance.operation(operationData).encodeABI();

const tx = {
    data: encodeABI,
    ...
}
const signedTx = await web3.eth.accounts.signTransaction(tx, sender.privateKey);

//send Operation tx
await web3.eth.sendSignedTransaction(signedTx.rawTransaction);

//call Operation
await web3.eth.call({
    to: aspectCore.options.address,
    data: encodeABI
});
```
<!-- @formatter:on -->

:::note
第一版Aspect操作调用遵循bytes in bytes out方式，用户需要自己对调用数据进行编码和解码。我们将在后续版本中提供更人性化的Aspect调用方式。
:::

### versionOf

获取Aspect的最新版本

#### 参数

- aspectId:aspect.aspect地址,**必要**.

#### 示例

<!-- @formatter:off -->
```javascript
var aspectCore = web3.atl.aspectCore();
// Aspect address
let aspectId ="0x5B38Da6a701c568545dCfcB03FcB875f56beddC4";
// call
let result = await aspectCore.methods.versionOf(aspectId).call()
console.log(result);
```
<!-- @formatter:on -->

### aspectsOf

> 通过合约地址获取Aspect列表，返回地址列表按优先级排序。

#### 参数

- contract: 账户地址，**必要**。

#### 示例

<!-- @formatter:off -->
```javascript
var aspectCore = web3.atl.aspectCore();
// Aspect address
let contract = "0x5B38Da6a701c568545dCfcB03FcB875f56beddC4";
let result = await aspectCore.methods.aspectsOf(contract).call()
console.log(result);

```
<!-- @formatter:on -->

### boundAddressesOf

> 获取与 Aspect 绑定的所有合约或 EOA 帐户

#### 参数

- aspectId：aspect.aspectAddress，**必要**。

#### 例子

<!-- @formatter:off -->

```javascript
var aspectCore = web3.atl.aspectCore();
// Aspect address
let aspectId = "0x5B38Da6a701c568545dCfcB03FcB875f56beddC4";
let result = await aspectCore.methods.boundAddressesOf(aspectId).call();
console.log(result);
```
<!-- @formatter:on -->
