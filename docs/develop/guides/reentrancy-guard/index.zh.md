---
sidebar_position：3
---

# 重新入侵

## 简介

此样本方面可以防止黑客类似
这 [重进入攻击恰好发生在2023.07](https://fortune.com/crypto/2023/07/31/curve-finance-52-million-hack-hacker-helps-return-funds/).

### 攻击方案：

1. 攻击的曲线池用Vyper（将智能合同语言编写为EVM字节码）。
2. 使用的VYPER版本（0.2.15）在编译器中有一个已知的再入锁问题。
3. 攻击者通过打电话绕过了重新进入锁 `add_liquidity` 从 `remove_liquidity` 通过后备功能，利用漏洞。

要深入研究攻击细节，您可以查看攻击
交易 [这里](https://explorer.phalcon.xyz/tx/eth/0xa84aa065ce61dbb1eb50ab6ae67fc31a9da50dd2c74eefd561661bfce2f1620c).


从我们的博客中了解更多信息：

 [方面编程如何通过链上运行时保护来防止重新进入攻击？](https://github.com/artela-network/example/blob/main/curve_reentrance/README.md).

### 先决条件

要重现攻击，请安装 `solc` 以及一个特定版本的 `vyper` 带有重入锁定错误：

```bash
pip install vyper==0.2.16
```

## 1. 初始化dapp

```bash
   npm install -g @artela/aspect-tool
   
   mkdir reentrancy-aspect && cd reentrancy-aspect
   
   aspect-tool init
   
   npm install
   
```

## 2. 创建区块链帐户（可选）。

如果您还没有一个帐户，请在项目文件夹下执行以下命令以创建两个帐户。

```bash
npm run account:create -- --skfile ./curve_accounts.txt
npm run account:create -- --skfile ./attack_accounts.txt

```

如果您的帐户中没有测试令牌，请加入 [我们的丢弃](https://discord.com/invite/artela) 
，需要测试网龙头。

如果您缺乏测试令牌，请在我们的 [不和谐]((https://discord.com/invite/artela) ）testnet-faucet通道。

## 3. 创建智能合约

#### 3.1合同/曲线

这是用Vyper实现的简化曲线智能合约的版本。 与曲线池类似，它有两个
方法： `add_liquidity` 和 `remove_liquidity`。 两者都受到相同的重入锁的保护。`AddLiquidity` 和
 `RemoveLiquidity` 当调用相应的方法时，将发出事件。

``` python
event AddLiquidity:
    executed: uint256

event RemoveLiquidity:
    executed: uint256

deployer: address

@external
def __init__():
    self.deployer = msg.sender

@external
@view
def isOwner(user: address) -> bool:
    return user == self.deployer

@external
@nonreentrant('lock')
def add_liquidity():
    log AddLiquidity(1)

@external
@nonreentrant('lock')
def remove_liquidity():
    raw_call(msg.sender, b "")
    log RemoveLiquidity(1)
```

#### 3.2合同/攻击。

这是以坚固性实施的攻击合同的简化版本。 它将尝试通过
这 `remove_liquidity` 具有后备功能。

```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.8.2 <0.9.0;

interface CurveContract {
    function add_liquidity() external;

    function remove_liquidity() external;
}

contract Attack {
    CurveContract public curve;

    constructor(address _curveContract) {
        curve = CurveContract(_curveContract);
    }

    function attack() external payable {
        curve.remove_liquidity();
    }

    fallback() external {
        curve.add_liquidity();
    }
}

```

#### 3.3部署曲线合同

```shell

mkdir -p ./build/contract

## build curve contract
vyper -f abi ./contracts/curve.vy > build/contract/CurveContract.abi && vyper ./contracts/curve.vy > build/contract/CurveContract.bin
 
## deploy contract
npm run contract:deploy -- --abi ./build/contract/CurveContract.abi  --bytecode ./build/contract/CurveContract.bin  --skfile ./curve_accounts.txt
```

可以从合同地址获得执行的结果，例如

```shell
...
--contractAccount 0xaa19F4957C890518b577205c41C706F1c07fa0cc --contractAddress 0xFe4b65F17554B45eF7D146B86E030da7A4e250bb
```

#### 3.4部署攻击合同

部署攻击合同时，将“ {curveaddress}”替换为真实的曲线合同地址

```shell

## build attack contract
npm run contract:build

## deploy contract
npm run contract:deploy -- --abi ./build/contract/Attack.abi  --bytecode ./build/contract/Attack.bin --args {curveAddress}  --skfile ./attack_accounts.txt
```

可以从合同地址获得执行的结果，例如：

```shell
...
--contractAccount 0xaa19F4957C890518b577205c41C706F1c07fa0bb --contractAddress 0xFe4b65F17554B45eF7D146B86E030da7A4e250ee
```

#### 3.5攻击

```shell
npm run contract:send -- --contract {attack-address}  --abi ./build/contract/Attack.abi   --skfile ./attack_accounts.txt  --method attack  --gas 200000
```

> *用真实地址替换占位符{所有者-Account}。 喜欢：0x08D721275C6DBBBB3BC688B62EF199BBD709154C9

如果重进入攻击成功，您将看到 `AddLiquidity` 和 `RemoveLiquidity` 事件记录。

## 4. 创建方面

接下来，我们部署一个方面代码来停止发生攻击。

#### 4.1在“ expact/index.ts”方法'Prefartractcall'中添加截然逻辑：

```typescript

import {
    allocate,
    entryPoint,
    execute,
    IPreContractCallJP,
    PreContractCallInput, ethereum, CallTreeQuery, sys, EthCallTree,
} from "@artela/aspect-libs";
import {Protobuf} from "as-proto/assembly/Protobuf";

/**
 * Please describe what functionality this aspect needs to implement.
 *
 * About the concept of Aspect @see [join-point](https://docs.artela.network/develop/core-concepts/join-point) 
 * How to develop an Aspect  @see [Aspect Structure](https://docs.artela.network/develop/reference/aspect-lib/aspect-structure) 
 */
class Aspect implements IPreContractCallJP {
    preContractCall(input: PreContractCallInput): void {
        // Get the method of currently called contract.
        const currentCallMethod = ethereum.parseMethodSig(input.call!.data);

        // Define functions that are not allowed to be reentered.
        const noReentrantMethods: Array<string> = [
            ethereum.computeMethodSig('add_liquidity()'),
            ethereum.computeMethodSig('remove_liquidity()')
        ];

        // Verify if the current method is within the scope of functions that are not susceptible to reentrancy.
        if (noReentrantMethods.includes(currentCallMethod)) {
            const callTreeQuery = new CallTreeQuery(-1);

            const queryCallTree = sys.hostApi.trace.queryCallTree(callTreeQuery);
            const ethCallTree = Protobuf.decode<EthCallTree>(queryCallTree, EthCallTree.decode);
            var size = ethCallTree.calls.size;
            var arrayKeys = ethCallTree.calls.keys();

            for (let i = 0; i < size; i++) {
                var key = arrayKeys[i];
                var parentCall = ethCallTree.calls.get(key);
                const parentCallMethod = ethereum.parseMethodSig(parentCall.data);
                if (noReentrantMethods.includes(parentCallMethod)) {
                    // If yes, revert the transaction.
                    sys.revert(`illegal transaction: method reentered from ${currentCallMethod} to ${parentCallMethod}`);
                }
            } 
        }
    }

    /**
     * isOwner is the governance account implemented by the Aspect, when any of the governance operation
     * (including upgrade, config, destroy) is made, isOwner method will be invoked to check
     * against the initiator's account to make sure it has the permission.
     *
     * @param sender address of the transaction
     * @return true if check success, false if check fail
     */
    isOwner(sender: Uint8Array): bool {
        return true;
    }

}

// 2.register aspect Instance
const aspect = new Aspect()
entryPoint.setAspect(aspect)

// 3.must export it
export {execute, allocate}

```

#### 4.2编译方面

建立您的方面：

```shell
npm run aspect:build
```

结果 `release.wasm` 在构建文件夹中包含必要的WASM字节码。

#### 4.3部署方面

部署您的编译方面：

```shell
 npm run aspect:deploy -- --wasm ./build/release.wasm  \
                          --joinPoints PreContractCall \
                          --skfile ./curve_accounts.txt
```

> ✅成功执行后，终端将显示 `Aspect address`。 重要的是要注意这一点
> 地址，因为它以后将很有用。

:::注意💡
有关此命令的更多详细用法信息，请参考
这 [部署命令](/develop/reference/aspect-tool/deploy-aspect) 文档。
:::

#### 4.4绑定曲线合同和方面

部署该方面不会自动激活它。 为了使其正常工作，请将其绑定到智能合约：

```shell
   npm run contract:bind -- --skfile ./curve_accounts.txt \
   --contract {curveAddress} \
   --abi ./build/contract/CurveContract.abi \
   --aspectId {aspect-Id} \
   --gas 200000
```
你会看到的 `== aspect bind success == ` 

## 5. 攻击测试

使用方面保护对简化的曲线合同执行重入攻击，并观察输出。

```shell
 npm run contract:send -- --contract {attackAddress} \
                          --abi ./build/contract/Attack.abi  \
                          --skfile ./attack_accounts.txt \
                          --method attack  \
                          --gas 200000 
```

如果保护成功，您将看到交易已恢复。

 ![img.svg](img.svg) 
 