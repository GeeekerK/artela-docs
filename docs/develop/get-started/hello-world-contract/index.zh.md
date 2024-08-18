---
sidebar_position：2
---

# 您好与Web3.js的智能合同

与Artela上的Web3.js制定智能合同时，该合同与以太坊虚拟机（EVM）兼容时，
典型的工作流程涉及以坚固性创建智能合约，将其部署到Artela
区块链，然后使用Web3.js与它进行交互 以下是一个简单的示例：

 **先决条件：** 

*[node.js](https://nodejs.org/) 
*[Solc](https://docs.soliditylang.org/en/v0.8.20/installing-solidity.html) 

## 1. 创建聪明的合同

用一个 `.sol` 扩大。 例如，创建一个 `hello.sol` 文件：

<！ -  @formatter：off->
```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.8.12 <0.9.0;

contract Hello {
    // print hello message
    function say(string calldata name) public pure returns (string memory) {
        return string.concat("hello"," ",name);
    }
}
```
<！ -  @formatter：on->>

## 2. 编译智能合同

此步骤依靠 `solc`，首先检查是否 [Solc](https://docs.soliditylang.org/en/v0.8.20/installing-solidity.html) 是
正确安装

```bash
solc --version
```

使用以下方式编译合同：

```bash
solc --abi --bin --hashes --overwrite -o ./ hello.sol
```

> ✅成功的汇编将产生 `Hello.abi` 和 `Hello.bin` 目前目录中的文件。

## 3. 部署智能合约

使用 `web3.js`，确保您已经安装了 `web3` 使用以下命令的包装：

```shell
npm install web3
```

接下来，创建一个 `deploy.mjs` 文件，您可以使用以下代码示例编写连接到一个
以太坊节点，编译智能合约并部署它。

```javascript
 "use strict";

// import required libs
import {readFileSync} from "fs";
import Web3 from "web3";

async function deploy() {
    //  Replace with the node connection to Artela
    let node = '<Artela TestNet>';

    // Replace with the path to your smart contract abi file
    let abiPath = '<Your Contract ABI File>';

    // Replace with the path to your smart contract byte code file 
    let byteCodePath = '<Your Contract BIN File>';

    // Replace with your private key
    let privateKey = '<Your Private Key>';

    const web3 = new Web3(node);

    const deployParams = {
        data: null,
        arguments: null,
    };

    let byteTxt = readFileSync(byteCodePath, "utf-8").toString().trim();
    if (byteTxt.startsWith("0x")) {
        byteTxt = byteTxt.slice(2);
    }

    deployParams.data = byteTxt.trim();

    let abiTxt = readFileSync(abiPath, "utf-8").toString().trim();
    const contractAbi = JSON.parse(abiTxt);

    let account = web3.eth.accounts.privateKeyToAccount(privateKey.trim());
    web3.eth.accounts.wallet.add(account.privateKey);

    // instantiate an instance of demo contract
    let tokenContract = new web3.eth.Contract(contractAbi);

    // deploy contract
    let tokenDeploy = tokenContract.deploy(deployParams);
    let nonceVal = await web3.eth.getTransactionCount(account.address);

    let tokenTx = {
        from: account.address,
        data: tokenDeploy.encodeABI(),
        nonce: nonceVal,
        gasPrice: 1000,

        gas: 7000000,
    };

    let signedTokenTx = await web3.eth.accounts.signTransaction(tokenTx, account.privateKey);
    await web3.eth.sendSignedTransaction(signedTokenTx.rawTransaction).on("receipt", (receipt) => {
        console.log(receipt);
        console.log("contract address: ", receipt.contractAddress);
    });
}

deploy().then();

```

确保用您的实际值替换脚本中的占位符值。

 ***Artela testnet：** 与Artela的节点连接，您可以填写'https://betanet-rpc1.artela.network'。 更多
有关测试网的信息，请参考 [TestNet信息](/develop/node/access-testnet).
 ***您的私钥：** 私钥对于签署和执行与其关联的交易至关重要
帐户。 通过创建钱包或导入现有帐户来获取它。 参考
到 [钱包配置](/develop/guides/wallet-configuration) 有关更多详细信息。
 ***您的合同ABI文件：** 您的智能合约ABI文件的路径。 喜欢'../hello.abi'。
 ***合同字节代码文件：** 智能合约字节代码文件的路径。 喜欢'../hello.bin'。

运行部署脚本。

```shell
node deploy.mjs
```

如果部署成功，脚本将显示交易收据。

```shell
{
  blockHash: '0x101473b29461db4d632a194d8ad5d07dabc258b5a2b27f229c784...',
  blockNumber: 210996n,
  contractAddress: '0x65c5c637326a33df07d520b88c671e...',  // Make a note of this value, you will need it.
  cumulativeGasUsed: 3500000n,
  from: '0x58c1b539b469fd15a02da47b52a3...',
  gasUsed: 7000000n,
  logs: [],
  logsBloom: '0x0000000000000000000....',
  status: 1n,
  transactionHash: '0xd4025995d764f7e1c944bab3f048f346c428ee91225f21....',
  transactionIndex: 0n,
  type: 0n
}
```

记下 `contractAddress` 成功部署后的价值，因为要调用智能合约所必需。

## 4. 致电智能合约

创建一个 `call.mjs` 文件，您可以使用以下代码示例编写连接到以太坊的脚本
节点，然后称呼它。

```javascript
 "use strict";

// import required libs
import {readFileSync} from "fs";
import Web3 from "web3";

async function call() {

    // Replace with the node connection to Artela
    let node = '<Artela TestNet>';
    
    // Replace with your private key
    let privateKey = '<Your Private Key>';
    
    // Replace with the path to your smart contract abi file
    let abiPath = '<Your Contract ABI File>';

    // Replace with the path to your smart contract address
    let contractAddr = '<Your Contract Address>';


    // init connection to Artela node
    const web3 = new Web3(node);

    let account = web3.eth.accounts.privateKeyToAccount(privateKey.trim());
    web3.eth.accounts.wallet.add(account.privateKey);

    let abiTxt = readFileSync(abiPath, "utf-8").toString().trim();
    const contractAbi = JSON.parse(abiTxt);

    // you can replace 'world' here to say others
    let storageInstance = new web3.eth.Contract(contractAbi, contractAddr);
    let instance = await storageInstance.methods ["say"]("world").call();
    console.log("call reuslt: " + instance);
}

call().then();

```

确保用您的实际值替换脚本中的占位符值。

 ***Artela testnet：** 与Artela的节点连接，您可以填写'https://betanet-rpc1.artela.network'。 更多
有关测试网的信息，请参考 [TestNet信息](/develop/node/access-testnet).
 ***您的私钥：** 私钥对于签署和执行与其关联的交易至关重要
帐户。 通过创建钱包或导入现有帐户来获取它。 参考
到 [钱包配置](/develop/guides/wallet-configuration) 有关更多详细信息。
 ***您的合同ABI文件：** 您的智能合约ABI文件的路径。
 ***您的合同地址：** 部署后生成的合同地址。

运行呼叫脚本。

```shell
node call.mjs
```

如果智能合约电话成功，脚本将显示结果：

```shell
call reuslt: hello world
```

恭喜！