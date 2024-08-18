---
sidebar_position：2
---

# 方面结构

 [方面](/develop/core-concepts/aspect)，在 [方面倒流](/develop/core-concepts/aspect-runtime)，遵循特定的开发模式，该模式利用WebAssembly（WASM）语法进行编码，随后
将其编译成Websembly模块。
本指南提供了一个简单的示例，概述了方面结构，从而提供了有关基本的见解
各个方面的组成部分。

## 一个简单的方面

铺路到您的第一个方面模块。

### 1.发起一个新项目

确保最近版本的 [node.js](https://nodejs.org/en) 并安装了其包装管理器NPM，然后
切换到新的目录，并照常初始化一个新的Node.js模块：

```shell
npm install -g @artela/aspect-tool

mkdir -p apsect-demo && cd apsect-demo

aspect-tool init 

npm install
```

init命令会自动创建推荐的目录结构和配置文件。

```shell
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

### 2.创建一个方面

在 `./aspect` 项目目录，用一个 `.ts` 扩大。
例如，创建一个 `HelloWorld.ts` 文件：

```typescript
import {
  allocate,
  entryPoint,
  execute,
  IPostContractCallJP,
  IPostTxExecuteJP,
  PostContractCallInput,
  PostTxExecuteInput,
  sys,
  uint8ArrayToHex,
} from "@artela/aspect-libs";

/**
 * Please describe what functionality this aspect needs to implement.
 *
 * About the concept of Aspect @see [join-point](https://docs.artela.network/develop/core-concepts/join-point) 
 * How to develop an Aspect  @see [Aspect Structure](https://docs.artela.network/develop/reference/aspect-lib/aspect-structure) 
 */
class HelloWorldAspect implements IPostContractCallJP, IPostTxExecuteJP {
  postTxExecute(input: PostTxExecuteInput): void {
    throw new Error("Method not implemented.");
  }
  /**
   * isOwner is the governance account implemented by the Aspect, when any of the governance operation
   * (including upgrade, config, destroy) is made, isOwner method will be invoked to check
   * against the initiator's account to make sure it has the permission.
   *
   * @param sender address of the transaction
   * @return true if check success, false if check fail
   */
  isOwner(sender: Uint8Array): bool {
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
const aspect = new HelloWorldAspect();
entryPoint.setAspect(aspect);

// 3.must export it
export {execute, allocate};

```

只有实施的课程 `join points` 可以称为一个方面。 实施 `Operation interface` 可以由方面的实体（EOA）引用。

加入点

*[iTransactionVerifier](/develop/reference/aspect-lib/join-points/verify-aspect) 
*[ipretxexecutejp](/develop/reference/aspect-lib/join-points/pre-tx-execute) 
*[iporetxexecutejp](/develop/reference/aspect-lib/join-points/post-tx-execute) 
*[iPrectractcalljp](/develop/reference/aspect-lib/join-points/pre-contract-call) 
*[ipostContractCalljp](/develop/reference/aspect-lib/join-points/post-contract-call) 

操作接口

*[异谱](/develop/reference/aspect-lib/operation) 

实施该方面类后，有必要将方面实例注册到入门点。

```typescript

const aspect = new HelloWorldAspect();
// register join point
entryPoint.setAspect(aspect);

// register Operation, if the class implements the Operation interface
entryPoint.setOperationAspect(aspect);
```

### 3.导出入口点

当消息发送到方面 - 倒置时，一个称为称为的函数 "entryPoint" 被称为。 方面有一对对应于不同消息类型的夫妇： `execute`，，，，`allocate`.

```shell
export {execute, allocate};
```

### 4.编译

包含编译为WebAssembly的汇编源的汇编目录现在可以转换为
WebAssembly通过执行汇编/index.ts中的构建命令。

```shell
npm run aspect:build
```
这样做会散发出编译的二进制文件，绑定和定义文件 `build/` 目录。

```shell
├── build
│   ├── debug.d.ts  // debug TypeScript declaration file
│   ├── debug.js    // debug JavaScript declaration file
│   ├── debug.wasm   //debug WebAssembly bytecode file
│   ├── debug.wasm.map  //debug WebAssembly output source map file
│   ├── debug.wat      // debug WebAssembly text output file
│   ├── release.d.ts    // release TypeScript declaration file
│   ├── release.js      // release JavaScript declaration file
│   ├── release.wasm    //release WebAssembly bytecode file
│   ├── release.wasm.map //release WebAssembly output source map file
│   └── release.wat    // release WebAssembly text output file

```

## 概括

*当前仅根据汇编语言提供方面开发包
*要实施一个方面，需要定义和实施 "join points." 另外，通过实施 "Operation interface," 方面的实体（EOA）可以引用该方面。
*开发该方面后，有必要在入口点注册联合点，然后导出入口点。
*编译以检查该方面是否有错误，并获取WebAssembly字节码文件。
*自从 [汇编](https://assemblyscript.bootcss.com/getting-started.html#setting-up-a-new-project) 项目
不提供诸如方面部署，绑定，呼叫等之类的功能。您需要使用其他工具
作为 [web3.js](/develop/client/artela-web3.js) 与Artela区块链交流。 我们建议您
使用 [@artela/fack-tool](/develop/reference/aspect-tool/overview)。 该工具提供了创建方面的功能
开发脚手架和与Artela区块链无缝集成。

  