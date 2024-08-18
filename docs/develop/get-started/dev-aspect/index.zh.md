---
sidebar_position：2
---

# 发展一个方面

本节指导您在Artela上构建具有示例方面的DAPP。
此方面是本机扩展，与智能合约的共同处理，可以在整个过程中注入
交易生命周期。 在此示例中，我们将展示方面如何识别和恢复特定的交易。

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
mkdir my-first-aspect && cd my-first-aspect

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

例如，创建一个 `HelloWorld.sol` 文件：

<！ -  @formatter：off->
```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.8.2 <0.9.0;

contract HelloWorld {
    address private owner;
    constructor() {
        owner = msg.sender;
    }
    function isOwner(address user) external view returns (bool result) {
        return user == owner;
    }

    // print hello message
    function hello() public pure returns (string memory) {
        return "hello";
    }

    // print world message
    function world() public pure returns (string memory) {
        return "world";
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

> ✅成功的汇编将产生 `HelloWorld.abi` 和 `HelloWorld.bin` 文件中的文件 `build/contract` 目录。

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

执行以下命令 `my-first-aspect` 如果您还没有这样做，则可以创建一个帐户：

```bash
npm run account:create
```

> ✅如果成功创建帐户，其私钥将被丢弃为 `privateKey.txt` 在当前目录中。

:::注意💡
有关此命令的更多详细用法信息，请
参考 [create-account命令](/develop/reference/aspect-tool/create-account) 文档。
:::

如果您的帐户缺乏测试令牌，请加入 [不和谐](https://discord.com/invite/artela)，并要求一些
在 `testnet-faucet` 渠道。

#### 2.3.4部署合同

执行以下命令 `my-first-aspect` 文件夹，使用提供的脚本：

```bash
npm run contract:deploy --  --abi ./build/contract/HelloWorld.abi \
                           --bytecode ./build/contract/HelloWorld.bin
```

> ✅成功部署后，终端将显示合同地址。

:::注意💡
有关此命令的更多详细用法信息，请参考
这 [部署合同命令](/develop/reference/aspect-tool/deploy-contract) 文档。
:::

### 2.4致电合同

执行以下命令 `my-first-aspect` 文件夹，致电合同

#### 2.4.1呼叫 `hello` 方法

```bash
npm run contract:call -- --contract {smart-contract-address}  \
                         --abi ./build/contract/HelloWorld.abi   \
                         --method hello                                             
```

*用从步骤获得的信息替换占位符{smart-contract-address}`2 3 deploy the smart contract`.

> ✅成功后，终端将显示呼叫结果。

:::注意💡
有关此命令的更多详细用法信息，请参考
这 [合同电话命令](/develop/reference/aspect-tool/contract-call) 文档。
:::

#### 2.4.2呼叫 `world` 方法

```bash
npm run contract:call -- --contract {smart-contract-address}  \
                         --abi ./build/contract/HelloWorld.abi   \
                         --method world
```

> ✅如果是 `world` 返回字符串，这意味着我们已经成功部署了 `HelloWorld` 合同。

## 3. 创建您的方面

### 3.1。 实现一个方面

方面源文件可以在 `aspect/index.ts`.

例如，要在执行智能合同后添加逻辑，请打开 `index.ts`，找到 `postContractCall` 
功能，并插入您的逻辑：

<！ -  @formatter：off->
```typescript
postContractCall(input:PostContractCallInput):void {
// Implement me...
}
```
<！ -  @formatter：on->>

:::注意💡
有关详细说明，请参阅 [方面文档](/develop/core-concepts/aspect-programming).
:::

### 3.2。 访问智能合约的状态变更

整合 `HelloWorld` 与您的方面签约，请执行以下步骤：

在 `aspect/index.ts`，添加您的方面以检查交易，如果 `world` 调用功能，然后还原：

<！ -  @formatter：off->
```typescript
import {
    allocate,
        entryPoint,
        execute,
        IPostContractCallJP,
        PostContractCallInput,
        sys,
        uint8ArrayToHex,
} from "@artela/aspect-libs";

// 1. implement IPostContractCallJP
class Aspect implements IPostContractCallJP {

    isOwner(sender: Uint8Array): bool {
        // implement me
        // if return false，bind、unbind、upgrade Aspect will be block
        return true;
    }
    /**
     * postContractCall is a join-point which will be invoked after a contract call has finished.
     *
     * @param input input to the current join point
     */
    postContractCall(input: PostContractCallInput): void {

        let txData = uint8ArrayToHex(input.call!.data);

        // if call `world` function then revert, 30b67baa is method signature of `world` 
        if (txData.startsWith("30b67baa")) {
            sys.revert("the function `world` not available");
        }
    }
}

// 2.register aspect Instance
const aspect = new Aspect();
entryPoint.setAspect(aspect);

// 3.must export it
export {execute, allocate};
```
<！ -  @formatter：on->>

### 3.3。 编译方面

建立您的方面：

```bash
npm run aspect:build 
```

> ✅由此产生 `release.wasm` 在 `build` 文件夹包含必要的WASM字节码。

### 3.4。 部署方面

部署您的编译方面：

```shell
npm run aspect:deploy -- --wasm ./build/release.wasm --joinPoints PostContractCall
```

> ✅成功执行后，终端将显示 `Aspect address`。 重要的是要注意这一点
> 地址，因为它以后将很有用。

:::注意💡
有关此命令的更多详细用法信息，请参考
这 [部署命令](/develop/reference/aspect-tool/deploy-aspect) 文档。
:::

## 4. 约束智能合约和方面

部署该方面不会自动激活它。 为了使其正常工作，请将其绑定到智能合约：

```bash
npm run contract:bind -- --contract {smart-contract-address} \
                         --abi ./build/contract/HelloWorld.abi \
                         --aspectId {aspect-Id} 
```

*用从步骤获得的信息替换占位符{smart-contract-address}`2 3 deploy the smart contract`.
* r将占位符{extack-id}带有从步骤获得的信息 `3.4. Deploy the Aspect`.

> ✅结合过程已经成功，并且已打印了交易收据。

:::注意💡
有关此命令的更多详细用法信息，请参考
这 [绑定命令](/develop/reference/aspect-tool/bind-aspect) 文档。
:::

## 5. 测试智能合约和方面集成

现在 `HelloWorld` 合同和方面是约束的，请致电 `world` 测试。

```bash
npm run contract:call -- --contract {smart-contract-address}  \
                         --abi ./build/contract/HelloWorld.abi   \
                         --method world 
```

*用从步骤获得的信息替换占位符{smart-contract-address}`2 3 deploy the smart contract`.

> ✅由于方面拦截，交易被恢复。

 ![img.svg](img.svg) 

恭喜！ 您已经了解了方面发展的基础知识。 要深入研究，请参阅我们的
综合的 [方面文档](https://docs.artela.network/develop/aspect-tools/aspect-docs).