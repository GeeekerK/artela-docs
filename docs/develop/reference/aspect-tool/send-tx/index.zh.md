# 发送交易

通过在区块链上发送交易来触发智能合约的执行。 这可能涉及状态变化，
事件触发，或执行其他合同逻辑。

## 命令

您可以使用以下命令发送事务：

```shell
  npm run contract:send -- --skfile {privateKey-path}    \
                         --contract {smart-contract-address} \
                         --abi ./build/contract/xxx.abi \
                         --method {method-name}  \
                         --args [..]
                         --gas 200000 
```

 **选项：** 
> *  -  Skfile：发送者的私人关键路径。 （可选，默认值 `./privateKey.txt` ）。
> * -abi：合同ABI路径。
> *  - 合同：智能合约地址。
> *  - 方法：方法名称。
> *  -  args：如果您的合同的构造函数需要输入参数，请使用 `--args '[1, "a"]'` （选修的）。
> *  - 加气：喜欢 `200000` （可选，默认值 `7000000` ）。
---

### Example

```shell
## usage 1: send a contract 'xxxx.sol' with hello() method, using default private key './privateKey.txt'
NPM运行合同：发送 - 合同0xA1AB92B67C4BD8BB0FA1C08F29A9A90B375C260185 \
                          --abi ./build/contract/HelloWorld.abi \
                          --method hello

## usage 3: call a contract 'xxxx.sol' with 'add(address aspectId, address account)' method, using private key './privateKey2.txt'.
NPM运行合同：发送 - 合同0xA1AB92B67C4BD8BB0FA1C08F29A9A90B375C260185 \
                          --abi ./build/xxxx.abi \
                          --method add \
                          --args 100

## usage 3: call a contract 'xxxx.sol' with 'unbind(address aspectId, address account)' method, using private key './privateKey2.txt'.
NPM运行合同：发送 - 合同0xA1AB92B67C4BD8BB0FA1C08F29A9A90B375C260185 \
                          --abi ./build/xxxx.abi \
                          --method unbind \
                          --args 0xCE3ccD4a308f25B4c1B36cC883A9bEd76Bc24627 0xA90927a72F1A6c8EFAfa0cc1b432f75eCc2086d8 \
                          --skfile ./privateKey2.txt
```

### Command Output

当前部署成功，并打印了交易的收据。

```shell
致电合同TX Hash：0x739effa2f1817baa2171C271C7307180C12D73A04D99BD6 ...
{
Blockhash：'0x7FF306F6AEEEC87AB32AAB67D7BA0E478A57F8E95419B91E9DE0DE0DBE5D ...'，
blocknumber：1129168，
合同编辑：null，
累积加工：2000000，
来自：'0x554EB2F94386FDCE289B8323A0F5D9D7C3C ...'，，
气体：4000001，
日志：[]，
logSbloom：'0x0000000000000000000000000000000000000 ...'，
状态：是的，
至：'0xc54f9bedf4bcab6770d5821ca41c53458 ...'，
transActionHash：'0x739effa2f1817baa2171C271C7307180C12D73A04D99BD666812A2 ...'，，
transActionIndex：0，
类型：'0x0'
}

```

## Customize

create-account命令的逻辑写在 `scripts/contract-send.cjs` 文件，主要依靠
由[@artela/web3]提供的实现（/develop/develop/client/artela-web3.js）API。
如果需要，您可以在此文件中修改逻辑以实现您的特定功能。

此外，您可以修改 `project.config.json` 在项目根中
文件夹[设置网络配置。]（/develop/referent/reference/faction/expact-tool/config#2network-rpc）。