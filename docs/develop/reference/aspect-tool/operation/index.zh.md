# 手术

本指南提供了使用命令通过操作启动EOA交易的说明。 有关其他详细信息
关于与操作相关的概念，请参阅：
*[处理电话](/develop/core-concepts/aspect#processing-calls) 
*[操作接口](/develop/reference/aspect-lib/operation) 

## 命令

### 1.操作电话

此命令允许您查询方面操作接口的状态或执行只读操作，而无需在区块链上创建新事务。

```bash
npm run operation:call -- --skfile {privateKey-path} \
                         --callData {call-data} \
                         --aspectId {aspect-Id} \
                         --gas 200000
```

 **选项：** 
> *  -  calldata：十六进制字符串呼叫数据：类似于' -  calldata 0x1167C2E50DFE34B9AD593D2C6694731097147317'
> *  - 光谱：方面ID。
> *  -  Skfile：发送者的私人关键路径。 （可选，默认值 `./privateKey.txt` ）。
> *  - 加气：喜欢 `200000` （可选，默认值 `7000000` ）。
---

#### Example

```shell
## usage 1: operation call using default private key './privateKey.txt'
NPM运行操作：调用-ASPECTID 0x06E883A11888E4A03B45298D132D870AAEB04C34 \
                          --callData 0x1167c2e50dFE34b9Ad593d2c6694731097147317  \

## usage 2: operation call using private key './curve_accounts.txt'                       
NPM运行操作：调用-ASPECTID 0x06E883A11888E4A03B45298D132D870AAEB04C34 \
                          --callData 0x1167c2e50dFE34b9Ad593d2c6694731097147317  \
                           --skfile ./curve_accounts.txt
```

#### Command Output

绑定是成功的，并打印了交易的收据。

```shell
操作呼叫结果：测试
```

### 2. Operation Send

该命令发送操作交易是指在以太坊区块链上启动和广播交易的过程。

```bash
NPM运行操作：发送 -  -Skfile {privateKey-Path} \
                         --callData {call-data} \
                         --aspectId {aspect-Id} \
                         --gas 200000

```

 **选项：** 
> *  -  calldata：十六进制字符串呼叫数据：类似于' -  calldata 0x1167C2E50DFE34B9AD593D2C6694731097147317'
> *  - 光谱：方面ID。
> *  -  Skfile：发送者的私人关键路径。 （可选，默认值 `./privateKey.txt` ）。
> *  - 加气：喜欢 `200000` （可选，默认值 `7000000` ）。
---

#### 例子

```shell

## usage 1: operation send using default private key './privateKey.txt'
npm run operation:send -- --aspectId 0x06e883A11888E4A03b45298D132D870AaeB04C34 \
                          --callData 0x1167c2e50dFE34b9Ad593d2c6694731097147317  \

## usage 2: operation send using private key './curve_accounts.txt'                         
npm run operation:send -- --aspectId 0x06e883A11888E4A03b45298D132D870AaeB04C34 \
                          --callData 0x1167c2e50dFE34b9Ad593d2c6694731097147317  \
                           --skfile ./curve_accounts.txt
```

#### 命令输出

绑定是成功的，并打印了交易的收据。

```shell
sending signed transaction...
{
  blockHash: '0x5b661a010625de7bdeabc1f505b28193936cbde012bf3d...',
  blockNumber: 1128845,
  contractAddress: null,
  cumulativeGasUsed: 0,
  from: '0x554eb2f94386fdce289b8323a0f5d9d7c...',
  gasUsed: 9000001,
  logs: [],
  logsBloom: '0x00000000000000000000000000000...',
  status: true,
  to: '0x0000000000000000000000000000000000a27e14',
  transactionHash: '0x77785ae0ac85fdce90d0b1e67c39f7af21918b4ffd636b1ce71f4bc5...',
  transactionIndex: 0,
  type: '0x0'
}
== aspect unbind success ==

```

## 定制

create-account命令的逻辑写在 `scripts/operation.cjs` 文件，主要依靠
实施由 [@artela/web3](/develop/client/artela-web3.js) API。

如果需要，您可以在此文件中修改逻辑以实现您的特定功能。

此外，您可以修改 `project.config.json` 在项目根中
文件夹 [设置网络配置。](/develop/reference/aspect-tool/config#2network-rpc) 
