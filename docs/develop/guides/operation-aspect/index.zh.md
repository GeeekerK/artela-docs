---
sidebar_position：3
---

# 操作电话

## 简介

操作是一个特殊接口。 该接口可以通过EOA签署的操作交易直接调用。 这
操作交易将触发该方面的执行。

本指南提供了有关如何创建和执行直接操作的分步演练
全面探索该过程。

## 1. 初始化dapp

```bash
   npm install -g @artela/aspect-tool
   
   mkdir operation-aspect && cd operation-aspect
   
   aspect-tool init
   
   npm install
   
```

## 2. 创建区块链帐户（可选）。

如果您还没有一个帐户，请在项目文件夹下执行以下命令以创建两个帐户。

```bash
npm run account:create 

```

如果成功执行命令，将打印以下日志并 `privateKey.txt` 文件将
生成。

如果您的帐户中没有测试令牌，请加入 [我们的丢弃](https://discord.com/invite/artela) 
，需要测试网龙头。

## 3. 创建方面

方面源文件可以在 `aspect/index.ts`。 现在，我们添加了操作的详细逻辑，以确保它
与预期功能保持一致。

```typescript
// The entry file of your WebAssembly module.

import {
    allocate,
    entryPoint,
    execute,
    IAspectOperation,
    OperationInput,
    stringToUint8Array,
    sys,
} from '@artela/aspect-libs';

class AspectTest implements IAspectOperation {
    operation(input: OperationInput): Uint8Array {
        sys.require(input.callData.length > 0, 'data is lost');
        //todo something
        return stringToUint8Array('HelloWorld');
    }
}

// 2.register aspect Instance
const aspect = new AspectTest();
entryPoint.setOperationAspect(aspect);

// 3.must export it
export {execute, allocate};

```

这里的代码首先验证输入参数不是空的，然后在todo之后返回“ helloworld”
某物;

应当注意，注册操作实例与其他Joinpoint方法不同。 它需要打电话
entrypoint.setoperationAspect（..）用于配置。

您可以执行一些复杂的逻辑：有一个白名单方面将触发交易前执行； 你
可以通过操作接口插入和更新白名单。

## 4. 编译方面

建立您的方面：

```shell
npm run aspect:build
```

结果 `release.wasm` 在构建文件夹中包含必要的WASM字节码。

## 5. 部署方面

部署您的编译方面：

```shell
 npm run aspect:deploy -- --wasm ./build/release.wasm 
```

> ✅成功执行后，终端将显示 `Aspect address`。 重要的是要注意这一点
> 地址，因为它以后将很有用。

:::注意💡
有关此命令的更多详细用法信息，请参考
这 [部署命令](/develop/reference/aspect-tool/deploy-aspect) 文档。
:::

## 6. 添加ABI操作

在您的项目根目标中创建一个ABI文件，例如 'expact.abi'，然后将以下内容复制到文件。

```json
[
  {
     "inputs": [
      {
         "internalType": "address",
         "name": "aspectId",
         "type": "address" 
      },
      {
         "internalType": "bytes",
         "name": "data",
         "type": "bytes" 
      }
    ],
     "name": "entrypoint",
     "outputs": [
      {
         "internalType": "bytes",
         "name": "result",
         "type": "bytes" 
      }
    ],
     "stateMutability": "view",
     "type": "function" 
  }
]

```

## 7. 通话测试

```shell

npm run contract:call -- --contract 0x0000000000000000000000000000000000a27e14 \
                          --abi ./aspect.abi  \
                          --method entrypoint \
                          --args {aspectId} 0x1167c2e50dFE34b9Ad593d2c6694731097147317
```

如果命令成功，您将看到

```shell
==== reuslt=== 0x48656c6c6f576f726c64
```


## 8. 发送交易测试

```shell
 npm run contract:send -- --contract 0x0000000000000000000000000000000000a27e14 \
                          --abi ./aspect.abi  \
                          --method entrypoint \
                          --args {aspectId}  0x1167c2e50dFE34b9Ad593d2c6694731097147317
```

如果命令成功，您将看到这样的收据：
```json
{
  blockHash: '0x8779c15cca64b2c92d19b00c2b61124cea3c3f4b8fd11bc...',
  blockNumber: 244493,
  contractAddress: null,
  cumulativeGasUsed: 0,
  from: '0x08d721275c6dbb33bc688b62ef....',
  gasUsed: 4000001,
  logs: [],
  logsBloom: '0x0000000000000000000000000000...',
  status: true,
  to: '0x0000000000000000000000000000000000a27e14',
  transactionHash: '0x2b50ff9acca780257ea1e70c574f4584ae3c9956cbfe8eb51...',
  transactionIndex: 0,
  type: '0x0'
}

```