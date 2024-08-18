# 创建一个帐户

此命令可以在项目中快速创建一个新帐户，您将在需要签名的其他命令中使用它
交易，您也可以在测试脚本中使用它。

## 命令

当您没有区块链帐户时，可以使用以下命令来创建一个帐户：

```shell
npm run account:create  -- --skfile {file_path}
```

 **选项：** 

> *  -  Skfile：发送者的私人关键路径。 （可选，默认值 `./privateKey.txt` ）。
---

### Example
```shell
## usage 1: this command will create a blockchain account and generate a private key file named 'privateKey.txt' in your project dir
NPM运行帐户：创建

## usage 2: this command will create a blockchain account and generate a private key file named 'accounts.txt' in your project dir
NPM运行帐户：创建 -  -Skfile ./accounts.txt

```

### Command Output

如果成功执行命令，将打印以下日志，并将生成一个私有键文件。
{file_path}'。

```shell
> 帐户：创建
> 节点脚本/create-account.cjs

地址：0x773B8DA8DE01C9A35DCB74E4E4C204 ...
```

## Customize

create-account命令的逻辑写在 `scripts/create-account.cjs` 文件，主要依靠
由[@artela/web3]提供的实现（/develop/develop/client/artela-web3.js）API。
如果需要，您可以在此文件中修改逻辑以实现您的特定功能。