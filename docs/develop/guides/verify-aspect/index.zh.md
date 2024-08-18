---
sidebar_position：3
---

# 通过密码验证TX

## 简介

本指南提供了有关如何使用的详细说明 "Verify tx" 功能以获得写入智能合约的功能
鉴于管理员提供的密码的了解，无需管理员的密钥。

 **先决条件：** 

*[node.js](https://nodejs.org/) 
*[Solc](https://docs.soliditylang.org/en/v0.8.20/installing-solidity.html) 

## 1. 建立一个新项目

确保您有最近版本的 [node.js](https://nodejs.org/) 和NPM安装，
首先安装 `aspect-tool` ：

```bash
npm install -g @artela/aspect-tool
```

 **项目初始化**，启动您的项目 `aspect-tool`，请执行以下步骤：

```bash
# Create a new directory and navigate into it
mkdir verify-aspect&& cd verify-aspect

# Set up the npm project with aspect-tool 
aspect-tool init

# Install the necessary dependencies
npm install
```

这将创建一个具有以下结构的项目目录：

```
.
├── README.md
├── asconfig.json
├── aspect                   <-- Your aspect code resides here
│   └── index.ts       <-- Entry functions for the aspect
├── contracts                <-- Place your smart contracts here
├── package.json
├── project.config.json
├── scripts                  <-- Utility scripts, including deploying, binding and etc.
│   ├── aspect-deploy.cjs
│   ├── bind.cjs
│   ├── contract-call.cjs
│   ├── contract-deploy.cjs
│   ├── contract-send.cjs
│   └── create-account.cjs
├── tests
└── tsconfig.json

```

## 2. 部署智能合同

### 2.1。 添加智能合同

在 `contracts` 项目目录，用一个创建智能合约源文件 `.sol` 扩大。

例如，创建一个 `Counter.sol` 文件：

<！ -  @formatter：off->
```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.8.2 <0.9.0;

contract Counter {
  uint256 private counter;
  address private owner;

  constructor() {
    owner = msg.sender;
  }
  function isOwner(address user) external view returns (bool result) {
    return user == owner;
  }
  modifier onlyOwner() {
    require(msg.sender == owner);
    _;
  }
  // only owner can do add
  function add(uint256 number) public onlyOwner {
    counter = counter + number;
  }
  function get() external view returns (uint256 result)  {
    return counter;
  }
}
```
<！ -  @formatter：on->>


### 2.2。 编译智能合同

此步骤依靠 `solc`，首先检查是否 [Solc](https://docs.soliditylang.org/en/v0.8.20/installing-solidity.html) 是
正确安装

```bash
solc --version
```

使用以下方式编译合同：

```bash
npm run contract:build
```

> ✅成功的汇编将产生 `Counter.abi` 和 `Counter.bin` 文件中的文件 `build/contract` 目录。

### 2.3。 部署智能合约

#### 2.3.1更新project.config.json

更新 `project.config.json` 在Root Directory中，具有适当的网络配置：

```json
{
   "node": "https://betanet-rpc1.artela.network" 
}
```

:::注意💡
有关开发环境设置的更多详细信息，请参考 [Artela Devnet](/develop/node/access-testnet) 
:::

#### 2.3.2创建一个区块链帐户（可选）。

执行以下命令 `verify-aspect` 如果您还没有这样做，则可以创建一个帐户：

```bash
npm run account:create
```

> ✅如果成功创建帐户，其私钥将被丢弃为 `privateKey.txt` 在电流中
> 目录。

:::注意💡
有关此命令的更多详细用法信息，请
参考 [create-account命令](/develop/reference/aspect-tool/create-account) 文档。
:::

如果您的帐户缺乏测试令牌，请加入 [不和谐](https://discord.com/invite/artela)，并要求一些
在 `testnet-faucet` 渠道。

#### 2.3.4部署合同

执行以下命令 `verify-aspect` 文件夹，使用提供的脚本：

<！ -  @formatter：off->
```bash
npm run contract:deploy --  --abi ./build/contract/Counter.abi \
                           --bytecode ./build/contract/Counter.bin
```
<！ -  @formatter：on->>

> ✅成功部署后，终端将显示合同地址。

:::注意💡
有关此命令的更多详细用法信息，请参考
这 [部署合同命令](/develop/reference/aspect-tool/deploy-contract) 文档。
:::

## 3. 创建您的方面

### 3.1。 实现一个方面

在 `aspect/index.ts`，添加您的方面以检查交易，如果验证数据不相等，请恢复：

<！ -  @formatter：off->
```typescript
import {
    allocate,
    entryPoint,
    execute,
    ITransactionVerifier,
    sys,
    TxVerifyInput, uint8ArrayToHex,
} from "@artela/aspect-libs";


class Aspect implements ITransactionVerifier {


    isOwner(sender: Uint8Array): bool {
        return true;
    }

    verifyTx(input: TxVerifyInput): Uint8Array {
        const Passwd: string = "123456";
        const validation = uint8ArrayToHex(input.validationData);
        // Verify whether the password matches the expected value.
        sys.require(validation == Passwd, 'invalid data');
        return sys.aspect.property.get<Uint8Array>("Owner");
    }

}

// 2.register aspect Instance
const aspect = new Aspect()
entryPoint.setAspect(aspect)

// 3.must export it
export {execute, allocate}

```
<！ -  @formatter：on->>

### 3.2。 编译方面

建立您的方面：

```bash
npm run aspect:build 
```

> ✅由此产生 `release.wasm` 在 `build` 文件夹包含必要的WASM字节码。

### 3.3。 部署方面

部署您的编译方面：

<！ -  @formatter：off->
```shell
npm run aspect:deploy -- --wasm ./build/release.wasm \
                         --joinPoints VerifyTx \
                         --properties '[{"key":"Owner","value":"{owner-account}"}]' 

```
<！ -  @formatter：on->>

用真实的付款帐户替换占位符{所有者-Account}。 喜欢：
0x08D721275C6DBBBB33BC688B62EF199BBD709154C9


> ✅成功执行后，终端将显示 `Aspect address`。 重要的是要注意这一点
> 地址，因为它以后将很有用。

:::注意💡
有关此命令的更多详细用法信息，请参考
这 [部署命令](/develop/reference/aspect-tool/deploy-aspect) 文档。
:::

## 4. 绑定合同

部署该方面不会自动激活它。 为了使其正常工作，请将其绑定到智能合约：

```bash
npm run contract:bind -- --contract {smart-contract-address} \
                         --abi ./build/contract/Counter.abi \
                         --aspectId {aspect-Id} 
```

*用从
步 `2.3.4 deploy the smart contract`.
* r将占位符{extack-id}带有从步骤获得的信息 `3.4. Deploy the Aspect`.

> ✅结合过程已经成功，并且已打印了交易收据。

:::注意💡
有关此命令的更多详细用法信息，请参考
这 [绑定命令](/develop/reference/aspect-tool/bind-aspect) 文档。
:::

## 5. 绑定EOA

```bash
npm run contract:bind -- --contract {owner-account} \
                         --abi ./build/contract/Counter.abi \
                         --aspectId {aspect-Id} \
                         --skfile {owner-private-key}
```

*用所有者帐户地址替换占位符{所有者-Account}。
*用所有者帐户私有密钥文件替换占位符{所有者 - 私人 - 键}。
* r将占位符{extack-id}带有从步骤获得的信息 `3.4. Deploy the Aspect`.

> ✅结合过程已经成功，并且已打印了交易收据。

:::注意💡
有关此命令的更多详细用法信息，请参考
这 [绑定命令](/develop/reference/aspect-tool/bind-aspect) 文档。
:::

## 6. 测试智能合约和方面集成

在 `scripts` 项目目录，创建一个验证tx呼叫脚本，该脚本生成不是
由{所有者}签名，并使用{所有者}提供的密码进行交易。
例如，创建一个 `verify.cjs` 文件：

```javascript
 "use strict" 

// import required libs
const fs = require('fs');
const Web3 = require('@artela/web3');
var argv = require('yargs')
    .string('node')
    .string('skfile')
    .array('args')
    .string('contract')
    .string('method')
    .string('abi')
    .string('password')
    .parserConfiguration({
         "parse-numbers": false,
    })
    .argv;
const {LegacyTransaction: EthereumTx} = require('@ethereumjs/tx')

const {numberToHex} = require("@artela/web3-utils");

async function call() {
    // init connection to Artela node
    const configJson = JSON.parse(fs.readFileSync('./project.config.json', "utf-8").toString());
    let node = (argv.node) ? String(argv.node) : configJson.node;
    if (!node) {
        console.log("'node' cannot be empty, please set by the parameter or artela.config.json")
        process.exit(0)
    }
    const web3 = new Web3(node);

    //--skfile ./build/privateKey.txt
    let senderPriKey = String(argv.skfile)
    if (!senderPriKey || senderPriKey === 'undefined') {
        senderPriKey = "privateKey.txt" 
    }
    if (!fs.existsSync(senderPriKey)) {
        console.log("'account' cannot be empty, please set by the parameter ' --skfile ./build/privateKey.txt'")
        process.exit(0)
    }
    let pk = fs.readFileSync(senderPriKey, 'utf-8');
    let sender = web3.eth.accounts.privateKeyToAccount(pk.trim());
    console.log("from address: ", sender.address);
    web3.eth.accounts.wallet.add(sender.privateKey);


    // --contract 0x9999999999999999999999999999999999999999
    const contractAddr = argv.contract;
    if (!contractAddr) {
        console.log("'contract address' cannot be empty, please set by the parameter ' --contract 0x9999999999999999999999999999999999999999'")
        process.exit(0)
    }

    // --abi xxx/xxx.abi
    const abiPath = String(argv.abi)
    let abi = null
    if (abiPath && abiPath !== 'undefined') {
        abi = JSON.parse(fs.readFileSync(abiPath, "utf-8").toString());
    } else {
        console.log("'abi' cannot be empty, please set by the parameter' --abi xxx/xxx.abi'")
        process.exit(0)
    }
    // --args [55]
    const inputs = argv.args;
    let parameters = [];
    if (inputs && inputs !== 'undefined') {
        parameters = inputs;
    }
    //--method count
    const method = argv.method;
    if (!method || method === 'undefined') {
        console.log("'method' cannot be empty, please set by the parameter ' --method {method-name}'")
        process.exit(0)
    }

    let storageInstance = new web3.eth.Contract(abi, contractAddr);
    let contractCallData = await storageInstance.methods [method](...parameters).encodeABI();
    //--method count
    const password = argv.password;
    if (!password || password === 'undefined') {
        console.log("'password' cannot be empty, please set by the parameter ' --password {password}'")
        process.exit(0)
    }

    let encodedData = web3.eth.abi.encodeParameters(['bytes', 'bytes'],
        [password, contractCallData]);
    // Append magic prefix and checksum to the encoded data
    encodedData = '0xCAFECAFE' + web3.utils.keccak256(encodedData).slice(2, 10) + encodedData.slice(2);
    let nonce = await web3.eth.getTransactionCount("{owner-account}");
    let gasPrice = await web3.eth.getGasPrice();
    let chainId = await web3.eth.getChainId();

    // Set gas and gas limit
    let gas = 8000000;
    let gasLimit = 20000000;
    // Update the transaction object with the encoded data
    let tx =
        {
            from: sender.address,
            nonce: numberToHex(nonce),
            gasPrice: numberToHex(gasPrice),
            gasLimit: numberToHex(gasLimit),
            gas: numberToHex(gas),
            data: encodedData,
            to: contractAddr,
            chainId: numberToHex(chainId)
        }

    // Return the serialized unsigned transaction
    let rawTx = '0x' + bytesToHex(EthereumTx.fromTxData(tx).serialize());
    let receipt = await web3.eth.sendSignedTransaction(rawTx);
    console.log(`call contract with result: `);
    console.log(receipt);
}

function bytesToHex(bytes) {
    return bytes.reduce((str, byte) => str + byte.toString(16).padStart(2, '0'), '');
}

call().then();

```

*用所有者帐户地址替换占位符{所有者-Account}。

此脚本用于创建
一个 [带有验证数据的未签名以太坊交易](/develop/reference/aspect-lib/join-points/verify-aspect#to-trigger).

然后创建一个新帐户：

```shell
npm run account:create  -- --skfile ./test_account.txt

```

> ✅如果成功创建帐户，其私钥将被丢弃为 `test_account.txt` 在电流中
> 目录。 地址将打印，您需要对其进行节点。

现在打电话 `verify.cjs` 和 `test_account` 测试。

```bash
node scripts/verify.cjs --skfile ./test_account.txt \
    --contract  {smart-contract-address} \
    --abi ./build/contract/Counter.abi \
    --args 1000 \
    --method add \
    --password 0x123456
```

*用从
步 `2.3.4  Deploy your contract`.

如果成功，它将像这样打印结果：

```json
call contract with result:
{
blockHash: '0x40208524a15ba7d65a91fb4e7c06f87e5ac1276d...',
blockNumber: 271998,
contractAddress: null,
cumulativeGasUsed: 10000000,
from: '0x08d721275c6dbb33bc688b62ef199bb...',
gasUsed: 20000000,
logs: [],
logsBloom: '0x00000000000000000000000000000000000000000000000000...',
status: true,
to: '0xd52bd5b358de33fc126ff50e845973c...',
transactionHash: '0x7ea944b07825e0afbb6924246dcfbeef2da13ae0d2970...',
transactionIndex: 0,
type: '0x0'
}

```

现在，让我们检查合同中的计数器值是否已更改；

```shell
npm run contract:call -- --contract {smart-contract-address} \
                        --abi ./build/contract/Counter.abi \
                        --method get \
                        --skfile ./test_account.txt
```
*用从
步 `2.3.4  Deploy your contract`.
  
如果成功执行命令，将看到
```shell
 ==== reuslt===1000
```

恭喜！ 您已经了解了方面发展的基础知识。 要深入研究，请参阅我们的
综合的 [方面文档](https://docs.artela.network/develop/aspect-tools/aspect-docs).