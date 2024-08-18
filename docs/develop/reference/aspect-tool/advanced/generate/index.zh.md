# 状态访问包装器

Artela允许方面访问该州的智能合同执行。 它提供了一个主机API来访问它。 但是它的水平太低，而且很难使用它。

这是访问该州更改的示例。

```tsx
// access a state 'balances' in a contract 
// which contract name is HoneyPot
// and address is 0x5B38Da6a701c568545dCfcB03FcB875f56beddC4
const contract = hexToUint8Array("0x5B38Da6a701c568545dCfcB03FcB875f56beddC4");
const stateVar = 'HoneyPot.balances';

// call host API to access it.
const query= new StateChangeQuery(contract,stateVar,[]);
const response = sys.hostApi.trace.queryStateChange(query);
const indicesResult = Protobuf.decode<EthStateChangeIndices>(response, EthStateChangeIndices.decode);
```

此状态访问包装器可以生成包装器类，您可以更轻松地访问该州的更改。

```tsx
//import wrapper class
import {HoneyPotState} from "./honeypot_storage";
...
// access it by a sweet api   
let contract = '0x5B38Da6a701c568545dCfcB03FcB875f56beddC4'
let sysBalance = new HoneyPotState._balance_(contract);
```

本指南指示用户使用 `aspect-tool generate` 并提供有关其利用的指导。

## 命令

```shell
USAGE
  $ aspect-tool generate [-i <value>] [-o <value>]
FLAGS
  -i, --in=<value>
  -o, --out=<value>
```

 **选项：** 

> *  -  I：输入存储布局JSON文件路径或目录，例如 ` -i storage_layout.json`  `-i ./build/contract` 
> *  -  o：目标生成的TS文件输出路径或目录，例如 `-o xx.ts`  `-o ./aspect/contract` 

## 例子

### 1.创建智能合同

在我一面的项目中，将智能合约添加到“合同”目录（请参阅 [初始化项目](/develop/reference/aspect-tool/guide/init) ）， 例如 `HoneyPot.sol` ：

```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.8.2 <0.9.0;

contract HoneyPot {
    mapping(address => uint256) public balances;
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

    function deposit() public payable {
        balances[msg.sender] += msg.value;
    }

    function withdraw() public {
        uint bal = balances[msg.sender];
        require(bal > 0);

        (bool sent,) = msg.sender.call{value: bal}("");
        require(sent, "Failed to send Ether");
        address sender = msg.sender;
        balances[sender] = 0;
    }
}
```

### 2.设置ASOLC
合同的汇编需要利用 `asolc`。 有关更多信息 `asolc`，请参考 [什么是Asolc](/develop/core-concepts/asolc) 

下载最新的ASOLC版本 [在这里](https://github.com/artela-network/artela-solidity/releases).

```shell
## set up ASOLC to environment variables
$ export PATH= {your asolc path}:$PATH

## confirm that ASOLC is installed successfully
$ asolc --version 
solc, the solidity compiler commandline interface
Version: 0.8.21-develop.2023.10.27+commit.d545edb7.Darwin.appleclang

```

### 3.编译合同

更新软件包。json的合同：使用asolc构建'：

```json
{
 "contract:build": "asolc -o ./build/contract/ --via-ir --abi --storage-layout --bin ./contracts/*.sol  --overwrite" 
}

```

执行合同汇编命令

```shell
npm run contract:build
```

如果成功执行该命令，则将在'./build/contract'目录中生成以下文件。

```shell
.
├── build
│   ├── contract
│   │   ├── HoneyPot.abi
│   │   ├── HoneyPot.bin
│   │   └── HoneyPot_storage.json

```

### 4.生成类

```shell
npm run aspect:gen
```

具体来说，该命令将在命令下执行，并生成 `./assembly/aspect/honeypot_storage.ts` 班级。

```shell
aspect-tool generate -i ./build/contract -o ./assembly/aspect
```

### 5.如何工作

> 以下代码显示了如何判断当前合同的余额变化并比较国家的变化
> 平衡图 `key==currentCall.from` 在合同中，如果不一样，请恢复交易

```typescript
import {
    allocate,
    BigInt,
    entryPoint,
    execute,
    IPostContractCallJP,
    PostContractCallInput,
    sys,
    uint8ArrayToHex,
} from '@artela/aspect-libs';
import { HoneyPotState } from './contract/honeypot-storage';

class GuardBTraceAspect implements IPostContractCallJP {
    isOwner(sender: Uint8Array): bool {
        const value = sys.aspect.property.get<Uint8Array>('owner');
        return uint8ArrayToHex(value).includes(uint8ArrayToHex(sender));
    }

    postContractCall(input: PostContractCallInput): void {
        const mytest = sys.aspect.property.get<string>('mytest-key');
        sys.require(mytest === 'test abc ', 'failed to get property key.');

        // 1.Calculate the eth balance change of DeFi SmartContract(HoneyPot) before and after tx.
        const to = uint8ArrayToHex(input.call!.to);
        const from = uint8ArrayToHex(input.call!.from);
        const sysBalance = new HoneyPotState._balance_(to);
        const deltaSys = sysBalance.current()!.sub(sysBalance.original());

        // 2.Calculate the financial change of withdrawer in DeFi SmartContract(HoneyPot) before and after tx.
        const contractState = new HoneyPotState.balances(to);

        let deltaUser = BigInt.ZERO;

        const fromState = contractState.get(from);

        const current = fromState.current();
        const original = fromState.original();
        if (current && original) {
            deltaUser = current.sub(original);
        }
        // 3.Verify if the above two values are equal.
        if (deltaSys.compareTo(deltaUser) != 0) {
            sys.revert('risky transaction');
        }
    }
}

// 2.register aspect Instance
const aspect = new GuardBTraceAspect();
entryPoint.setAspect(aspect);

// 3.must export it
export { execute, allocate };
```

### 6.编译方面

执行方面汇编命令

```shell
npm run aspect:build
```

如果命令成功执行，则在WebAssembly中生成 `build` 目录