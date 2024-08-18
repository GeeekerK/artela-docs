---
sidebar_position：4
---

# 代表们参考

本文档包含了代表们通过命令行界面（CLI）与Artela网络进行交互的所有必要信息。

它还包含有关如何管理帐户，还原筹款人帐户的说明。

## 安装 `artelad` 

 `artelad` ：这是与Artela网络完整节点交互的命令行接口。

：：：警告
 **请检查您是否下载了最新的稳定版本`artelad`可用** 
:::

[**下载二进制文件** 这是给出的
尚不可用。

 [**从源安装**](../node/join-testnet#1-prepare-artelad) 

 `artelad` 从终端使用。 要打开终端，请执行以下步骤：

- **macos** ： `Finder`>`Applications`>`Utilities`>`Terminal` 
- **Linux** ： `Ctrl`+`Alt`+`T` 

## 宇宙帐户

每个宇宙帐户的核心，都有一个种子，它采用12或24个字助记符的形式。 通过此助记符，可以创建任何数量的宇宙帐户，即私钥/公共密钥对。 这称为HD钱包（请参阅 [BIP32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki) 有关HD钱包规范的更多信息）。

存储在帐户中的资金由私钥控制。 该私钥是使用助记符的单向函数生成的。 如果丢失了私钥，则可以使用助记符检索它。 但是，如果您失去了助记符，您将失去对所有派生的私钥的访问权限。 同样，如果有人可以访问您的助记符，他们可以访问所有相关帐户。

：：：警告
 **不要与任何人失去或分享您的12个单词。 为了防止盗窃或损失资金，最好确保您保留助记符的多个副本，并将其存储在安全，安全的地方，并且只有您知道如何访问。 如果某人能够访问您的助记符，他们将能够访问您的私钥并控制与之相关的帐户。** 
:::

该地址是具有人类可读前缀的公共字符串（例如 `artela10snjt8dmpr5my0h76xj48ty80uzwhraqalu4eg` ）标识您的帐户。 当某人想向您发送资金时，他们将其发送到您的地址。 在计算上找不到与给定地址相关的私钥。

### 还原帐户

您应该拥有12个字的助记符。 新生成的助记符使用24个单词，但是12字的助记符也与所有Artela工具兼容。

#### 在计算机上

：：：警告
 **注意：在离线计算机上执行此操作更加安全** 
:::

要还原帐户助记符并将关联的加密私钥存储在计算机上，请使用以下命令：

```bash
artelad keys add <yourKeyName> --recover
```

- `<yourKeyName>` 是帐户的名称。 它是用于从助记符中得出密钥对的帐号的引用。 当您要发送交易时，您将使用此名称来标识您的帐户。
- 您可以添加可选 `--account` 标记以指定路径（ `0`，，，，`1`，，，，`2`，...）您想用来生成帐户。 默认情况下，帐户 `0` 生成。

帐户的私钥 `0` 将保存在操作系统的凭据存储中。
每次您要发送交易时，都需要解锁系统的凭据商店。
如果您无法访问凭据存储，则可以随时恢复使用的私钥
疯子。

### 创建一个帐户

要创建一个帐户，您只需要拥有 `artelad` 安装。 在创建它之前，您需要知道打算在哪里存储并与私钥进行交互。 最好的选择是将它们存储在离线专用计算机或分类帐设备中。 将它们存储在常规的在线计算机上会涉及更多风险，因为任何通过互联网渗透到计算机的人都可以剥离您的私钥并窃取您的资金。

#### 使用计算机

：：：警告
 **注意：在离线计算机上执行此操作更加安全** 
:::

要生成帐户，只需使用以下命令：

```bash
artelad keys add <yourKeyName>
```

该命令将生成24个字的助记符，并保存私钥和公共钥匙以获取帐户 `0` 
同时。
每次您要发送交易时，都需要解锁系统的凭据商店。
如果您无法访问凭据存储，则可以随时恢复使用的私钥
疯子。

：：：警告
 **不要与任何人失去或分享您的12个单词。 为了防止盗窃或损失资金，最好确保您保留助记符的多个副本，并将其存储在安全，安全的地方，并且只有您知道如何访问。 如果某人能够访问您的助记符，他们将能够访问您的私钥并控制与之相关的帐户。** 
:::

```bash
history -c
rm ~/.bash_history
```

- `<yourKeyName>` 是帐户的名称。 它是用于从助记符中得出密钥对的帐号的引用。 当您要发送交易时，您将使用此名称来标识您的帐户。
- 您可以添加可选 `--account` 标记以指定路径（ `0`，，，，`1`，，，，`2`，...）您想用来生成帐户。 默认情况下，帐户 `0` 生成。

您可以使用以下命令从同一助记符中生成更多帐户：

```bash
artelad keys add <yourKeyName> --recover --account 1
```

此命令将提示您输入密码以及助记符。 更改帐号以生成不同的帐户。

## 访问Artela网络

为了查询状态并发送交易，您需要一种访问网络的方法。 为此，您可以运行自己的全节点，也可以连接到他人的全节点。

### 运行自己的全节点

这是最安全的选择，但具有相对较高的资源要求。 为了运行自己的全节点，您需要良好的带宽和至少1TB的磁盘空间。

您会找到有关如何安装的教程 `artelad`  [这里](../getting-started/installation.md)，以及运行全节点的指南 [这里](../hub-tutorials/join-mainnet.md).

### 连接到远程全节点

如果您不想要或无法运行自己的节点，则可以连接到他人的全节点。 您应该选择信任的操作员，因为恶意操作员可能会返回不正确的查询结果或审查您的交易。 但是，由于您的私钥存储在计算机或分类帐设备上，因此他们将永远无法窃取您的资金。 全节点操作员的可能选项包括验证者，钱包提供商或交易所。

为了连接到全节点，您将需要以下表格的地址： `https://77.87.106.33:26657` （*注意：这是一个占位符*）。 您选择信任的全节点操作员必须传达此地址。 您将在 [以下部分](#setting-up-artelad).

## 设置 `artelad` 

：：：信息
 **设置之前`artelad`，确保您设置了一种方法[访问Artela网络](../node/access-testnet)** 
:::

：：：警告
 **请检查您始终使用的最新稳定版本`artelad`** 
:::

 `artelad` 是使您能够与Artela网络上运行的节点进行交互的工具，无论您是否自己运行。 让我们正确设置它。

为了设置 `artelad`，使用以下命令：

```bash
artelad config <flag> <value>
```

它允许您为每个给定标志设置一个默认值。

首先，设置要连接到的全节点的地址：

```bash
artelad config node <host>:<port

// example: artelad config node https://77.87.106.33:26657 (note: this is a placeholder)
```

如果您自己运行全节点，只需使用 `tcp://localhost:26657` 作为地址。

最后，让我们设置 `chain-id` 我们想与区块链互动的区块链：

```bash
artelad config chain-id <chain_id>
```

## 查询国家

 `artelad` 让您从区块链中查询所有相关信息，例如帐户余额，债券代币，未偿还的奖励，治理建议等。 接下来是代表们最有用的命令的列表。

```bash
// query account balances and other account-related information
artelad query account <yourAddress>

// query the list of validators
artelad query staking validators

// query the information of a validator given their address (e.g. cosmosvaloper1n5pepvmgsfd3p2tqqgvt505jvymmstf6s9gw27)
artelad query staking validator <validatorAddress>

// query all delegations made from a delegator given their address (e.g. cosmos10snjt8dmpr5my0h76xj48ty80uzwhraqalu4eg)
artelad query staking delegations <delegatorAddress>

// query a specific delegation made from a delegator (e.g. cosmos10snjt8dmpr5my0h76xj48ty80uzwhraqalu4eg) to a validator (e.g. cosmosvaloper1n5pepvmgsfd3p2tqqgvt505jvymmstf6s9gw27) given their addresses
artelad query staking delegation <delegatorAddress> <validatorAddress>

// query the rewards of a delegator given a delegator address (e.g. cosmos10snjt8dmpr5my0h76xj48ty80uzwhraqalu4eg)
artelad query distribution rewards <delegatorAddress>

// query all proposals currently open for depositing
artelad query gov proposals --status deposit_period

// query all proposals currently open for voting
artelad query gov proposals --status voting_period

// query a proposal given its proposalID
artelad query gov proposal <proposalID>
```

有关更多命令，只需键入：

```bash
artelad query
```

对于每个命令，您可以使用 `-h` 或者 `--help` 标志以获取更多信息。

## 发送交易

：：：警告
在Artela网络上，公认的Denom是 `uart`， 在哪里 `1art = 1x18^uart` 
:::

### 关于汽油和费用的注释

Artela网络上的交易需要包括交易费，以便处理。 该费用为运行交易所需的汽油支付。 公式如下：

```
fees = ceil(gas * gasPrices)
```

这 `gas` 取决于交易。 不同的交易需要不同的 `gas`。 这 `gas` 交易的金额是在处理过程时计算的，但是有一种方法可以通过使用 `auto` 值的价值 `gas` 旗帜。 当然，这只会给出一个估计。 您可以用标志调整此估计 `--gas-adjustment` （默认 `1.0` ）如果您想确保您提供足够的 `gas` 用于交易。 在本教程的其余部分中，我们将使用 `--gas-adjustment` 的 `1.5`.

这 `gasPrice` 是每个单位的价格 `gas`。 每个验证器设置一个 `min-gas-price` 价值，仅包括具有 `gasPrice` 大于他们 `min-gas-price`.

The交易 `fees` 是 `gas` 和 `gasPrice`。 作为用户，您必须输入2分的2。`gasPrice`/`fees`，您的交易将包含在一个块中的机会越高。

### 发送令牌

```bash
// Send a certain amount of tokens to an address
// Ex value for parameters (do not actually use these values in your tx!!): <to_address>=cosmos16m93fezfiezhvnjajzrfyszml8qm92a0w67ntjhd3d0 <amount>=1000000uatom
// Ex value for flags: <gasPrice>=0.0025uatom

artelad tx bank send [from_key_or_address] [to_address] [amount] [flags]
```

### 粘结原子并提取奖励

```bash
// Bond a certain amount of Atoms to a given validator
// ex value for flags: <validatorAddress>=cosmosvaloper18thamkhnj9wz8pa4nhnp9rldprgant57pk2m8s, <amountToBound>=10000000uatom, <gasPrice>=0.0025uatom

artelad tx staking delegate <validatorAddress> <amountToBond> --from <delegatorKeyName> --gas auto --gas-adjustment 1.5 --gas-prices <gasPrice>


// Redelegate a certain amount of Atoms from a validator to another
// Can only be used if already bonded to a validator
// Redelegation takes effect immediately, there is no waiting period to redelegate
// After a redelegation, no other redelegation can be made from the account for the next 3 weeks
// ex value for flags: <stcValidatorAddress>=cosmosvaloper18thamkhnj9wz8pa4nhnp9rldprgant57pk2m8s, <amountToRedelegate>=100000000uatom, <gasPrice>=0.0025uatom

artelad tx staking redelegate <srcValidatorAddress> <destValidatorAddress> <amountToRedelegate> --from <delegatorKeyName> --gas auto --gas-adjustment 1.5 --gas-prices <gasPrice>

// Withdraw all rewards
// ex value for flag: <gasPrice>=0.0025uatom

artelad tx distribution withdraw-all-rewards --from <delegatorKeyName> --gas auto --gas-adjustment 1.5 --gas-prices <gasPrice>


// Unbond a certain amount of Arts from a given validator
// You will have to wait 3 weeks before your Arts are fully unbonded and transferrable
// ex value for flags: <validatorAddress>=cosmosvaloper18thamkhnj9wz8pa4nhnp9rldprgant57pk2m8s, <amountToUnbound>=10000000uatom, <gasPrice>=0.0025uatom

artelad tx staking unbond <validatorAddress> <amountToUnbond> --from <delegatorKeyName> --gas auto --gas-adjustment 1.5 --gas-prices <gasPrice>
```

为了确认您的交易完成，您可以使用以下查询：

```bash
// your balance should change after you bond Arts or withdraw rewards
artelad query account

// you should have delegations after you bond Atom
artelad query staking delegations <delegatorAddress>

// this returns your tx if it has been included
// use the tx hash that was displayed when you created the tx
artelad query tx <txHash>

```

如果您通过受信任的全节点与网络进行交互，请与Block Explorer仔细检查。

## 参与治理

#### 治理入门

Artela网络具有内置的治理系统，可让债券持有人对提案进行投票。 提案有三种类型：

- `Text Proposals` ：这些是最基本的建议类型。 它们可用于对特定主题的网络意见。
- `Parameter Proposals` ：这些用于更新现有参数的值。
- `Software Upgrade Proposal` ：这些用于提出枢纽软件的升级。

任何原子持有人都可以提交建议。 为了使该提议开放投票，它必须带有 `deposit` 大于称为的参数 `minDeposit`。 这 `deposit` 不必由提交者整体提供。 如果最初的提议者 `deposit` 该提案进入 `deposit_period` 地位。 然后，任何原子持有人都可以通过发送 `depositTx`.

一旦 `deposit` 到达 `minDeposit`，提案进入 `voting_period`，持续2周。 任何 **结合** 然后，Atom Holder可以对此提案进行投票。 选项是 `Yes`，，，，`No`，，，，`NoWithVeto` 和 `Abstain`。 投票的权重基于发件人的债券艺术数量。 如果他们不投票，代表们将继承其验证者的投票。 但是，代表们可以通过自己发送投票来覆盖验证者的投票。

在投票期结束时，如果有超过50％ `Yes` 投票（不包括 `Abstain` 投票），少于33.33％ `NoWithVeto` 投票（不包括 `Abstain` 选票）。

#### 实践

```bash
// Submit a Proposal
// <type>=text/parameter_change/software_upgrade
// ex value for flag: <gasPrice>=0.0025uatom

artelad tx gov submit-proposal --title "Test Proposal" --description "My awesome proposal" --type <type> --deposit=10000000uatom --gas auto --gas-adjustment 1.5 --gas-prices <gasPrice> --from <delegatorKeyName>

// Increase deposit of a proposal
// Retrieve proposalID from $artelad query gov proposals --status deposit_period
// ex value for parameter: <deposit>=10000000uatom

artelad tx gov deposit <proposalID> <deposit> --gas auto --gas-adjustment 1.5 --gas-prices <gasPrice> --from <delegatorKeyName>

// Vote on a proposal
// Retrieve proposalID from $artelad query gov proposals --status voting_period
// <option>=yes/no/no_with_veto/abstain

artelad tx gov vote <proposalID> <option> --gas auto --gas-adjustment 1.5 --gas-prices <gasPrice> --from <delegatorKeyName>
```

### 从离线计算机签署交易

如果您没有分类帐设备，并且想在离线计算机上与您的私钥进行交互，则可以使用以下过程。 首先，在 **在线计算机** 使用以下命令（带有键合交易的示例）：

```bash
// Bond Arts
// ex value for flags: <amountToBound>=10000000uatom, <bech32AddressOfValidator>=cosmosvaloper18thamkhnj9wz8pa4nhnp9rldprgant57pk2m8s, <gasPrice>=0.0025uatom, <delegatorAddress>=cosmos10snjt8dmpr5my0h76xj48ty80uzwhraqalu4eg

artelad tx staking delegate <validatorAddress> <amountToBond> --from <delegatorAddress> --gas auto --gas-adjustment 1.5 --gas-prices <gasPrice> --generate-only > unsignedTX.json
```

为了签名，您还需要 `chain-id`，，，，`account-number` 和 `sequence`。 这 `chain-id` 是您提交交易的区块链的唯一标识符。 这 `account-number` 是当您的帐户首次收到资金时生成的标识符。 这 `sequence` 数字用于跟踪您发送的交易数量并防止重播攻击。

从Genesis文件中获取链ID（ `artela_11822-1` ），以及使用帐户查询的其他两个字段：

```bash
artelad query account <yourAddress> --chain-id artela_11822-1
```

然后，复制 `unsignedTx.json` 并将其（例如通过USB）转移到离线计算机。 如果尚未完成，[在离线计算机上创建一个帐户](#using-a-computer)。 为了获得其他安全性，您可以在使用以下命令签名之前仔细检查交易的参数：

```bash
cat unsignedTx.json
```

现在，使用以下命令签署事务。 你需要 `chain-id`，，，，`sequence` 和 `account-number` 以前获得：

```bash
artelad tx sign unsignedTx.json --from <delegatorKeyName> --offline --chain-id artela_11822-1 --sequence <sequence> --account-number <account-number> > signedTx.json
```

复制 `signedTx.json` 并将其转回在线计算机。 最后，使用以下命令广播交易：

```bash
artelad tx broadcast signedTx.json
```