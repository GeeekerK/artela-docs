# 合同电话

 `Contract call` 指在区块链上执行智能合约的操作。 通过合同电话，用户
可以与目标智能合约进行交互，并执行合同中定义的功能或方法。

## 命令

您可以使用以下命令收取呼叫：

```shell
  npm run contract:call -- --skfile {privateKey-path} \
                         --contract {smart-contract-address}  \
                         --abi ./build/contract/xxx.abi   \
                         --method {method-name}  \
                         --args [..] \
                         --gas 200000 
```

选项：
> * -abi：合同ABI路径。
> *  - 合同：智能合约地址。
> *  - 方法：方法名称。
> *  -  Skfile：发送者的私人关键路径。 （可选，默认值 `./privateKey.txt` ）。
> *  -  args：如果您的合同的构造函数需要输入参数，请使用 `--args '[1, "a"]'` （选修的）。
> *  - 加气：喜欢 `200000` （可选，默认值 `7000000` ）。
---

### Example

```shell
## usage 1: call a contract 'xxxx.sol' with hello() method, using default private key './privateKey.txt'
NPM运行合同：呼叫-Contract 0xa1ab92b67c4bd8bb0fa1c08​​f29a90b375c260185 \
                          --abi ./build/contract/xxxx.abi \
                          --method hello

## usage 3: call a contract 'xxxx.sol' with 'add(int32 num)' method, using private key './privateKey2.txt'.
NPM运行合同：呼叫-Contract 0xa1ab92b67c4bd8bb0fa1c08​​f29a90b375c260185 \
                          --abi ./build/contract/xxxx.abi \
                          --method add \
                          --args 100

## usage 3: call a contract 'xxxx.sol' with 'unbind(address aspectId, address account)' method, using private key './privateKey2.txt'.
NPM运行合同：呼叫-Contract 0xa1ab92b67c4bd8bb0fa1c08​​f29a90b375c260185 \
                          --abi ./build/contract/xxxx.abi \
                          --method unbind \
                          --args 0xCE3ccD4a308f25B4c1B36cC883A9bEd76Bc24627 0xA90927a72F1A6c8EFAfa0cc1b432f75eCc2086d8 \
                          --skfile ./privateKey2.txt
```

### Command Output

当前部署成功，并打印了交易的收据。

```shell
从地址：0x554EB2F94386FDCE289B8323A0F5D9D7 ...
==== reuslt ===
{reuslt数据}
```

## Customize

create-account命令的逻辑写在 `scripts/contract-call.cjs` 文件，主要依靠
由[@artela/web3]提供的实现（/develop/develop/client/artela-web3.js）API。
如果需要，您可以在此文件中修改逻辑以实现您的特定功能。

此外，您可以修改 `project.config.json` 在项目root文件夹中[设置网络配置。]（/开发/参考/appect/expact-tool/config#2network-rpc）。