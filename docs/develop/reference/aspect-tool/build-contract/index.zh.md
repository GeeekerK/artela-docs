# 建立合同

完成合同制定后，有必要使用合同汇编命令验证正确性
合同代码并生成二进制（BIN），应用程序二进制接口（ABI）和存储类文件。

## 命令

您可以使用以下命令来建立合同：

```shell
npm run contract:build
```

### 例子

```shell
npm run contract:build
```

### 命令输出

编译的产品放在 `build/contract` directory.it将生成xxx.bin，xxx.abi，xxx_storage.json文件。
```shell
.
├── build
│   ├── contract
│   │   ├── xxx.abi
│   │   ├── xxx.bin
│   │   └── xxx_storage.json

```

## 定制

此命令依靠 `solc`。 具体来说，将执行
```shell
solc -o ./build/contract/ --via-ir --abi --storage-layout --bin ./contracts/*.sol  --overwrite
```
关于合同编译器，您可以根据当前环境自定义配置。 有关详细信息，请参考 [配置文档](/develop/reference/aspect-tool/config#1-contract-compiler).