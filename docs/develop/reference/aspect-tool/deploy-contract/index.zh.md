# 部署合同

智能合约的部署涉及发布合同的二进制代码，应用程序二进制界面（ABI），
以及其他必要的元数据 `Artela` 区块链，从而生成合同的关联地址，使其
在网络上可用。

## 命令

您可以使用以下命令部署合同：

```bash
npm run contract:deploy -- --skfile {privateKey-path} \
                           --abi ./build/contract/xxx.abi \
                           --bytecode ./build/contract/xxx.bin \
                           --args [..] \
                           --gas 200000
```

 **选项：** 
> *  -  Skfile：发送者的私人关键路径。 （可选，默认值 `./privateKey.txt` ）。
> * -abi：合同ABI路径。
> *  -  bytecode：合同字节路径。
> *  -  args：如果您的合同的构造函数需要输入参数，请使用 `--args '[1, "a"]'` （选修的）。
> *  - 加气：例如，`200000` （可选，默认值 `7000000` ）
---

### Example

```shell
## usage 1: deploy a contract 'xxx.sol' using default private key './privateKey.txt'
NPM运行合同：部署-Abi ./build/contract/xxx.abi \
                            --bytecode ./build/contract/xxx.bin

## usage 2: deploy a contract 'xxx.sol' using private key './privateKey2.txt'
NPM运行合同：部署 -  -Skfile ./privatekey2.txt \
-abi ./build/contract/xxx.abi \
                            --bytecode ./build/contract/xxx.bin

## usage 3: deploy a contract 'xxx.sol' with 'constructor(uint num, address owner, string name)' constructor.
NPM运行合同：部署-Abi ./build/contract/xxx.abi \
                                --bytecode ./build/contract/xxx.bin \
                                --args  1  0xAABB...CCDD "a" 
```

### Command Output

如果成功执行命令，将打印以下日志，可以记录 `contractAddress`，一些命令将依靠此值。

```shell
部署合同TX哈希：0xE0BF4ECFD6EFCBF292AFCEB97EC8B3FD2B3EB3EB31A6882F6B79972 ...
{
Blockhash：'0xF9135ED605D53EE2F6D563DA263B40C56D322C00E0F582C2F2 ...'，
blocknumber：304，
contractAddress：'0x489036739CA7E4316EF683B55051ADE ....'，，
累积量：3500000，
来自：'0x773B8DA8DE0D1C9A35DCB74E4C204C4 ....'，
气：7000001，
日志：[]，
logSbloom：'0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000来了0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000来了000000000000000000000000000000000000000000000000000'，，
状态：是的，
到：null，
transActionHash：'0xE0BF4ECFD6EFCBF292AFCEB97EC8B3FD2B3EB31A6882F6B7997 ....'，
transActionIndex：0，
类型：'0x0'
}
合同地址：0x489036739CA7E4316EF683B55051A .....
--contractAccount 0x773B8Da8De01C9a35DCb74E4C204c4b...... --contractAddress 0x489036739ca7e4316ef683B55051ade155...

```

## Customize

create-account命令的逻辑写在 `scripts/contract-deploy.cjs` 文件，主要依靠
由[@artela/web3]提供的实现（/develop/develop/client/artela-web3.js）API。
如果需要，您可以在此文件中修改逻辑以实现您的特定功能。

此外，您可以修改 `project.config.json` 在项目root文件夹中[设置网络配置。]（/开发/参考/appect/expact-tool/config#2network-rpc）。