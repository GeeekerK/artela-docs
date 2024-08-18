# 配置

## 介绍

通常，开发人员的部署和调试环境可能与默认环境有所不同。 这涉及
针对开发量身定制的特定配置和设置，允许用户在以下
项目配置。
1. 合同编译器
2. 网络RPC

## 选项

### 1.合同编译器

有时，您需要更改汇编方法以获取不同的功能，例如，使用Artela-evm功能，
您需要使用ASOLC来编译合同，因此您需要修改智能合约的汇编方法。

修改“合同：构建”在“ package.json”中以支持它：

1. **Solc模式（默认）** 

 [Solc](https://docs.soliditylang.org/en/v0.8.20/installing-solidity.html) 是必需的，更新“合同：构建”
下列的，

```json
    {
        "contract:build": "solc -o ./build/contract/ --via-ir --abi --storage-layout --bin ./contracts/*.sol  --overwrite" 
    }
   ```

2. **SOLCJS模式** 

该项目依赖于“@Openzeppelin/Contracts”的第三方NPM软件包，需要编译
在 [SOLCJS](https://github.com/ethereum/solc-js)。 更新“合同：构建”以下，

安装solcjs
```壳
NPM安装-G SOLC
solcjs- version
```
   Update 'contract:build' to the following:

   ```JSON
{
 "contract:build" ： "solcjs --abi --bin --include-path ./node_modules/ --base-path . -o ./build/contract/  ./contracts/*.sol" 
}
```

3. **ASOLC模式** 

开发了ASOLC来生成与与
Artela EVM。 因此，可以将ASOLC视为Solc的增强版本，并保持与Solc的完全兼容性，而
引入开创性功能。

下载 [Asolc](https://github.com/artela-network/artela-solidity/tags) 并设置本地环境变量；

更新“合同：构建”到以下：
```json
    {
        "contract:build": "solc -o ./build/contract/ --via-ir --abi --storage-layout --bin ./contracts/*.sol  --overwrite" 
    }
   ```

### 2.网络RPC

修改 `project.config.json` 在项目root文件夹中以将网络配置设置为以下（假设
我们正在使用 [Artela Testnet](/develop/node/access-testnet)，如果您使用自己的节点，请更改配置
因此）：

```json
{
    "node": "https://betanet-rpc1.artela.network" 
}
```