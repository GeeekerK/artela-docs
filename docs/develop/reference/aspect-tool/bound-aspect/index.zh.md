# 获得约束方面

此命令用于根据帐户地址查询界方面的信息。

## 命令

```shell
npm run bound:aspect  -- --skfile {privateKey-path} \
                         --contract {smart-contract-address}  \
                         --gas 200000 
```

选项：
> *  - 合同：智能合约地址。
> *  -  Skfile：发送者的私人关键路径。 （可选，默认值 `./privateKey.txt` ）。
> *  - 加气：喜欢 `200000` （可选，默认值 `7000000` ）。
---

### Example

```shell
## usage 1
NPM运行绑定：方面 - 合同0x1E904B2409CA1E9B6037248D1DFC75C1882DDD3F

## usage 2
npm运行绑定：方面 - 合同0x1E904B2409CA1E9B60337248D1DFC75C1882DDD3F \ \ \ \
                          --skfile ./curve_accounts.txt

```

### Command Output

如果此命令成功并打印了结果：

```shell
## bound aspects : [{aspect id, aspect version, priority}]
边界方面：0xCFC6E698A9750251127E855BAEBE06729D28B96D，1,1
```

## Customize

create-account命令的逻辑写在 `scripts/get-bound-aspect.cjs` 文件，主要依靠
由[@artela/web3]提供的实现（/develop/develop/client/artela-web3.js）API。
如果需要，您可以在此文件中修改逻辑以实现您的特定功能。

此外，您可以修改 `project.config.json` 在项目root文件夹中[设置网络配置。]（/开发/参考/appect/expact-tool/config#2network-rpc）。