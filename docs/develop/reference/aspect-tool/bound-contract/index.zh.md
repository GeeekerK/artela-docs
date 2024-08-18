# 获取约束帐户

此命令用于根据aSTEACTID查询边界帐户的信息。

## 命令

```shell
npm run bound:account  -- --skfile {privateKey-path} \
                         --aspectId {aspect-id}  \
                         --gas 200000 
```

选项：
> *  - 光谱：aptiveid。 喜欢'0xCFC6E698A9750251127E855BAEBE06729D28B96D'
> *  -  Skfile：发送者的私人关键路径。 （可选，默认值 `./privateKey.txt` ）。
> *  - 加气：喜欢 `200000` （可选，默认值 `7000000` ）。
---

### Example

```shell
## usage 1
npm运行绑定：帐户-AspectID 0xcfc6e698a9750251127E85555BAEBE06729D28B96D

## usage 2
npm运行绑定：帐户-AspectID 0xcfc6e698a9750251127E85555BAEBE06729D28B96D \
                          --skfile ./privateKey2.txt

```

### Command Output

如果此命令成功并打印了结果：

```shell
## bound accounts : account address1, account address2...
边界帐户：0x1E904B2409CA1E9B60337248D1DFC75C18882DD3F

```

## Customize

create-account命令的逻辑写在 `scripts/get-bound-contract.cjs` 文件，主要依靠
由[@artela/web3]提供的实现（/develop/develop/client/artela-web3.js）API。
如果需要，您可以在此文件中修改逻辑以实现您的特定功能。

此外，您可以修改 `project.config.json` 在项目root文件夹中[设置网络配置。]（/开发/参考/appect/expact-tool/config#2network-rpc）。