---
sidebar_position：2
---

# 初始化项目

## 介绍

这 `@artela/aspect-tool init ` 命令帮助开发人员基于
汇编语言。 这种脚手架带有来自Artela图书馆的依赖性，包括基本的发展
库和常见脚本。 它支持诸如编译，测试和部署智能合约等操作以及
WebAssembly，从而提高了区块链开发的效率。
本指南概述了启动方面开发项目的过程


## 初始化项目

```shell

mkdir my-first-aspect && cd  my-first-aspect

npm install -g @artela/aspect-tool

aspect-tool init
```

## 项目

### 结构

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

该命令会自动创建推荐的目录结构和配置文件：

*`./aspect` 目录用于存储您写的方面。 这些资源被编译到WebAssembly，更多的详细信息以学习 [如何写一个方面](/develop/reference/aspect-lib/aspect-structure).

*`./aspect/index.ts` 默认条目文件被编译到WebAssembly，以使您启动。

*`./scripts/*.cjs` 方面和合同汇编，部署，绑定和其他脚本。

### package.json

#### 核心依赖性

*[@artela/web3*](/develop/client/artela-web3.js) ：扩展以太坊的Web3.js 1.x，支持方面操作。
*[敬意](https://github.com/piotr-oles/as-proto) ：编码和解码Protobuf消息

```json
{
   "dependencies": {
     "@artela/aspect-libs": "last Version",
     "@artela/web3": "last Version",
     "@assemblyscript/loader": "last Version",
     "as-proto": "last Version",
  }
}
```

#### 发展依赖性

* @artela/feack-tool：无缝管理方面开发工具的协助开发人员。

```json
{
   "devDependencies": {
     "@artela/aspect-tool": "last Version",
     "assemblyscript": "last Version",
     "as-proto-gen": "last Version",
     "yargs": "last Version" 
  }
}
```