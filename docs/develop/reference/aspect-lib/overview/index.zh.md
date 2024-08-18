---
sidebar_position：2
---

# 方面库

## 介绍

一个 [汇编](https://assemblyscript.bootcss.com/) 库编写方面要部署到
Artela，`aspect-libs` 提供主机API与方面互动
运行时，使用这组API，您可以访问区块链数据，SMART
合同，加密功能，调用系统调用等等。


## 安装

Leact-Libs库提供了访问Artela节点商店，区块链数据，智能合约，加密的API
功能等等。 要使用它，您所要做的就是添加一个依赖性：

*如果您使用NPM安装
```shell
npm install --dev @artela/aspect-libs # NPM

```
*如果您使用纱线安装
```shell
yarn add --dev @artela/aspect-libs    # Yarn
```


## 用法
---

### Structure

* [方面结构]（/develop/reference/appect-lib/aentivestructure）

### Join-Points
* [verifytx]（/develop/reference/axpect/expact-lib/verify-atept）
* [pretxexecute]（/develop/reference/appect/faction lib/tx级 - 敏感/pre-tx-execute）
* [PredtractCall]（/开发/参考/appect/facte lib/tx-level-apept/pre-cartract-call）
* [ContractCall]（/develop/reference/appect/faction lib/tx级 -  appect/contract-call）
* [posttxexecute]（/开发/参考/facture/facte lib/tx-level-aspect/post-tx-execute）

### Operation Interface
* [操作]（/develop/reference/facture/factep-lib/aperation-ateppect）

### API references
* [sys]（/develop/reference/facture/facte lib/components/sys）
* [sys.hostapi]（/develop/reference/axpect/faction lib/components/sys-hostapi）
* [sys.Aspect]（/开发/参考/axpect-lib/components/sys-Aspect）
* [以太坊]（/develop/reference/axpect/facte lib/components/ethereum）
* [助手]（/develop/reference/facture/facte lib/components/helper）

## Versions

子图清单中的版本指定了由Artela Blockchain节点运行的映射方面版本
版本。

|版本|Artela版本|发行说明|
|：---------|：------------------------|：----------------------------------------------------------------------- ---------------------------------------------------------------|
|0.0.33|V0.4.7-RC6|可以使用第一个Beta，其中已发布了交易级别的功能|

