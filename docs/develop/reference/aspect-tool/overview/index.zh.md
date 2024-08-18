---
sidebar_position：2
---

# 方面工具

方面工具是一种内置汇编的开发工具，旨在协助开发人员无缝管理方面
发展。 该工具提供了创建方面开发脚手架的功能，并与
Artela区块链。 开发人员可以利用方面工具来完成诸如编译，测试和部署SMART之类的任务
合同和WebAssembly，提高区块链开发的效率。
此工具发表在 [NPM存储库](https://www.npmjs.com/package/@artela/aspect-tool).

## 要求

1. 1。[node.js](https://nodejs.org/en/download/) 版本18.0或更高版本（可以通过运行node -v进行检查）。 你可以
使用 [NVM](https://github.com/nvm-sh/nvm) 用于在已安装的一台计算机上管理多个节点版本。
安装node.js时，建议您检查与依赖关系相关的所有复选框。

*确认Node.js已通过运行正确安装

```shell
node --version
```

2. 2。[汇编](https://assemblyscript.bootcss.com/introduction.html) 是一种构建的编程语言
打字稿的基础，专为WebAssembly（WASM）设计。

3. 3。[sloc](https://docs.soliditylang.org/en/latest/installing-solidity.html) 安装坚固的编译器。

## 安装

```shell
npm install @artela/aspect-tool
```

## 命令

该工具包含两个命令：

1. 在目录中启动一个方面项目。

```jsx
USAGE
$
aspect - tool
init [-d < value >]
FLAGS
- d, --dir = <value> [default: The current folder directory]
```

2. 为方面生成状态跟踪代码。

```jsx
USAGE
$
aspect - tool
generate [-i < value >] [-o < value >]
FLAGS
- i, -- in = <value>
    -o, --out=
    <value>
```

## 工具指南

*[初始化项目](/develop/reference/aspect-tool/init) 
*[配置](/develop/reference/aspect-tool/config) 
*[创建一个帐户](/develop/reference/aspect-tool/create-account) 
*[建立合同](/develop/reference/aspect-tool/build-contract) 
*[部署合同](/develop/reference/aspect-tool/deploy-contract) 
*[建立方面](/develop/reference/aspect-tool/build-aspect) 
*[部署方面](/develop/reference/aspect-tool/deploy-aspect) 
*[合同绑定方面](/develop/reference/aspect-tool/bind-aspect) 
*[合同解答方面](/develop/reference/aspect-tool/unbind-aspect) 
*[合同电话](/develop/reference/aspect-tool/contract-call) 
*[发送交易](/develop/reference/aspect-tool/send-tx) 
*[呼叫/发送操作](/develop/reference/aspect-tool/operation) 
*[获取约束帐户](/develop/reference/aspect-tool/bound-contract) 
*[获得约束方面](/develop/reference/aspect-tool/bound-aspect) 

## 先进的

*[状态访问包装器](/develop/reference/aspect-tool/advanced/generate) 

## 版本

子图清单中的版本指定发行说明。

|版本|发行说明|
|：---------|：----------------------------------------------------------------------- -------------------------|
|0.0.57|添加Unbrine，操作，获取绑定方面，获取绑定帐户命令。|
|0.0.56|第一个beta可用。|