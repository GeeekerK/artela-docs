# 合同解答方面

解开方面与智能合约之间的关系； 看 [概念的详细信息](/develop/core-concepts/lifecycle#binding).

## 命令

使用以下命令绑定合同方面：

```bash
npm run contract:unbind -- --skfile {privateKey-path} \
                         --contract {smart-contract-address} \
                         --aspectId {aspect-Id} \
                         --gas 200000
```

 **选项：** 
> *  - 合同：智能合约地址。
> *  - 光谱：方面ID。
> *  -  Skfile：发送者的私人关键路径。 （可选，默认值 `./privateKey.txt` ）。
> *  - 加气：喜欢 `200000` （可选，默认值 `7000000` ）。
---

### Example

```shell
## usage 1: xxx contract bind aspect use using default private key './privateKey.txt'
NPM运行合同：Unbind -ncon -narvers 0x4F59C931FB8B1138348C950110D484B07007F1AF \
                         --aspectId 0xA7d8497480b28B90f2327F6bD6E588A7e2733BBf

## usage 2: xxx contract unbind aspect use using default private key './privateKey2.txt'
NPM运行合同：绑定 - 合同0x4F59C931FB8B1138348C950110D484B07007F1AF \
                         --aspectId 0xA7d8497480b28B90f2327F6bD6E588A7e2733BBf \
                         --skfile ./privateKey2.txt
```

### Command Output

绑定是成功的，并打印了交易的收据。

```shell
发送签名交易...
{
Blockhash：'0x5B661A010625DE7BDEABC1F505B2819393936CBDE012BF3D ...'，
blocknumber：1128845，
合同编辑：null，
累积加used：0，
来自：'0x554EB2F94386FDCE289B8323A0F5D9D7C ...'，
气体：9000001，
日志：[]，
logsbloom：'0x0000000000000000000000000000000 ...'，
状态：是的，
到：'0x000000000000000000000000000000000000A27E14'，
transActionHash：'0x77785AE0AC85FDCE90D0B1E67C39F7AF21918B4FFD636B1CE71F4BC5 ...'，
transActionIndex：0，
类型：'0x0'
}
==方面揭开成功==

```

## Customize

create-account命令的逻辑写在 `scripts/unbind.cjs` 文件，主要依靠
由[@artela/web3]提供的实现（/develop/develop/client/artela-web3.js）API。

如果需要，您可以在此文件中修改逻辑以实现您的特定功能。

此外，您可以修改 `project.config.json` 在项目root文件夹中[设置网络配置。]（/开发/参考/appect/expact-tool/config#2Network-RPC）
