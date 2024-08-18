---
sidebar_position：3
---

# 智能合约和通过瞬态存储的方面通信

## 简介

这是使用智能合约和方面之间数据互换的详细过程 `transientStorage`.

在这种情况下，数据写在 `preTxExecute` 加入点，允许合同通过
 `getAspectContext` 方法。 此外，数据写在合同的 `setAspectContext` 方法，书面数据是
随后在 `preTxExecute` 加入点。

## 1. 初始化dapp

```bash
   npm install -g @artela/aspect-tool
   
   mkdir contract-aspect && cd contract-aspect
   
   aspect-tool init
   
   npm install
   
```

## 2. 创建区块链帐户（可选）。

如果您还没有一个帐户，请在项目文件夹下执行以下命令以创建两个帐户。

```bash
npm run account:create 

```

✅如果成功创建帐户，其私钥将被丢弃为 `privateKey.txt` 在当前目录中。

如果您的帐户中没有测试令牌，请加入 [我们的丢弃](https://discord.com/invite/artela) 
，需要测试网龙头。

## 3. 创建智能合约

在 `contracts` 项目目录，用一个创建智能合约源文件 `.sol` 扩大。

例如，创建一个 `Storage.sol` 文件：

```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.8.0 <0.9.0;

/**
 * @title Storage
 * @dev Store & retrieve value in a variable
 */
contract Storage {
    address private deployer;

    constructor() {
        deployer = msg.sender;
    }

    function isOwner(address user) external view returns (bool result) {
        if (user == deployer) {
            return true;
        } else {
            return false;
        }
    }

    function getAspectContext(address aspectId, string calldata key) public returns (string memory validationData) {
        bytes memory contextKey = abi.encodePacked(aspectId, key);
        (bool success, bytes memory returnData) = address(0x64).call(contextKey);
        validationData = success ? string(returnData) : '';
    }

    function setAspectContext(string calldata key, string calldata value) public returns (bool) {
        bytes memory contextKey = abi.encode(key, value);
        (bool success,) = address(0x66).call(contextKey);
        return success;
    }
}
```

在执行智能合约期间，存储需要在瞬态存储中共享的数据。

* getAspectContext（...）：GetAspectContext函数允许通过呼叫访问预编码地址（0x64）
检索方面上下文。 此上下文包括已写入的数据
presxececute和Preftractcall联接点。

* SetAspectContext（...）：SetAspectContext函数通过制作一个来促进对预编码地址（0x66）的访问
调用以设置方面上下文。 此操作涉及将数据写入瞬态，并且数据变为
可通过“订婚后”和“ txexecute”联接点访问。

## 4. 部署合同

```shell

mkdir -p ./build/contract

npm run contract:build

## deploy contract
npm run contract:deploy -- --abi ./build/contract/Storage.abi  --bytecode ./build/contract/Storage.bin 
```

✅成功部署后，终端将显示 `contractAddress`。 请注意，它将是
在即将到来的命令中使用。

```shell
...
--contractAccount 0xaa19F4957C890518b577205c41C706F1c07fa0cc --contractAddress 0xFe4b65F17554B45eF7D146B86E030da7A4e250bb
```

## 5. 创建方面

方面源文件可以在 `aspect/index.ts`。 我们将添加以下逻辑以与SMART交换数据
合同。

*在presxececute Pointcut中，键值对 `ToContract => HelloWorld` 在
合同被执行。 随后，致电 `getAspectContext({aspectId},'ToContract')` 合同中的方法可以
检索“ helloworld”的值。

*在遵循的入口点之后 `postTxExecute` 发生并执行合同，您可以获得
这
 `setAspectContext()` 致电合同方法 `transientStorage.get()` 在这个角度。

```typescript
import {
    allocate,
    entryPoint,
    ethereum,
    execute,
    IPostTxExecuteJP,
    IPreTxExecuteJP,
    PostTxExecuteInput,
    PreTxExecuteInput,
    sys, BytesData,
    uint8ArrayToHex,
} from '@artela/aspect-libs';
import {Protobuf} from "as-proto/assembly";

class StoreAspect
    implements IPostTxExecuteJP, IPreTxExecuteJP {
    isOwner(sender: Uint8Array): bool {
        return true
    }

    preTxExecute(input: PreTxExecuteInput): void {
        //for smart contract call
        sys.aspect.transientStorage.get<string>('ToContract').set<string>('HelloWorld');
    }

    postTxExecute(input: PostTxExecuteInput): void {
        const to = uint8ArrayToHex(input.tx!.to);
        let txData = sys.hostApi.runtimeContext.get("tx.data");
        const txDataPt = Protobuf.decode<BytesData>(txData, BytesData.decode);
        const parentCallMethod = ethereum.parseMethodSig(txDataPt.data);
        const value = sys.aspect.transientStorage.get<string>('ToAspect', to).unwrap();
        // setAspectContext method signature value is `9cf3ef1e` 
        if (parentCallMethod == "9cf3ef1e") {
            //'HelloAspect' here is set from smart contract
            sys.require(value == "HelloAspect", "failed to get value by contract setting.");
        }
    }

}

// 2.register aspect Instance
const aspect = new StoreAspect();
entryPoint.setAspect(aspect);

// 3.must export it
export {execute, allocate};

```

## 6. 部署方面

建立您的方面：

```shell
npm run aspect:build
```

结果 `release.wasm` 在构建文件夹中包含必要的WASM字节码。

部署您的编译方面：

```shell
 npm run aspect:deploy -- --wasm ./build/release.wasm  \
                           --joinPoints PreTxExecute PostTxExecute 
```

> ✅成功执行后，终端将显示 `Aspect address`。 重要的是要注意这一点
> 地址，因为它以后将很有用。

:::注意💡
有关此命令的更多详细用法信息，请参考
这 [部署命令](/develop/reference/aspect-tool/deploy-aspect) 文档。
:::

## 7. 约束合同和方面

部署该方面不会自动激活它。 为了使其正常工作，请将其绑定到智能合约：

```shell
   npm run contract:bind -- --contract {contractAddress} \
                            --abi ./build/contract/Storage.abi \
                            --aspectId {aspect-Id}
```

* r将占位符{ContractAddress}提供从步骤4获得的信息。部署智能合约。
* r将占位符{faction-id}带有从步骤6获得的信息。部署该方面。

如果成功执行命令，将看到 `== aspect bind success == `.

## 8. 测试GetAspectContext

现在，让我们检查一下是否 `GetAspectContext` 合同中的方法能够检索写在
这 `preTxExecute` 加入点。

```shell
 npm run contract:call -- --contract {contractAddress} \
                          --abi ./build/contract/Storage.abi  \
                          --method getAspectContext \
                          --args {aspectId} ToContract
```

如果成功执行命令，将看到

```shell
==== reuslt=== HelloWorld
```

## 9. 测试SetAspectContext

现在，让我们验证是否 `SetAspectContext` 合同中的方法已成功执行和编写。

```shell
 npm run contract:call -- --contract {attackAddress} \
                          --abi ./build/contract/Storage.abi  \
                          --method setAspectContext \
                          --args ToAspect HelloAspect
```

如果成功执行命令，将看到

```shell
==== reuslt=== true
```
 