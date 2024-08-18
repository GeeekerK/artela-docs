---
sidebar_position：5
---

# 验证器参考

在设置验证器节点之前，请确保已完成 [运行一个完整的节点](../node/run-full-node) 指导。

## 创建验证器

：：：警告
如果您想成为Artela的验证者 `testnet`，您应该了解更多有关 [安全](./security).
:::

你的 `cosmosvalconspub` 可用于通过Staking代币来创建新的验证器。 您可以通过运行：

```bash
artelad tendermint show-validator
```

：：：警告
不要使用更多 `uart` 比你拥有的！
:::

要创建验证器，只需使用以下命令：

```bash
artelad tx staking create-validator \
  --amount=1000000uart \
  --pubkey=$(artelad tendermint show-validator) \
  --moniker="choose a moniker" \
  --chain-id=<chain_id> \
  --commission-rate="0.10" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --gas="auto" \
  --gas-prices="0.0025uart" \
  --from=<key_name>
```
：：：信息
指定佣金参数时 `commission-max-change-rate` 用于测量％_point_更改 `commission-rate`。 例如。 1％至2％是提高100％，但仅1个百分点。
:::

一开始，您可能不会有足够的艺术品成为积极验证者的一部分。 用户能够使用该验证者（活动集以外的验证者）委派 [Keplr Web应用程序](https://wallet.keplr.app/#/cosmoshub/stake?tab=inactive-validators)。 您可以通过使用第三方资源管理器（如 [mintscan](https://www.mintscan.io/cosmos/validators).

## 编辑验证器描述

您可以编辑验证者的公开描述。 此信息是为了识别您的验证者，并将依靠代表们决定要使用哪些验证者。 确保为下面的每个标志提供输入。 如果命令中未包含标志，则字段将默认为空（ `--moniker` 默认为机器名称），如果该字段从未设置或保持相同，则该字段在过去设置了。

<key_name>指定您要编辑的验证器。 如果您选择不包含以下一些标志，请记住 - 从标志 **必须** 包括以确定要更新的验证器。

这 `--identity` 可用于验证与KeyBase或Uport这样的系统的身份。 使用keybase时，`--identity` 应该用16位字符串填充 [keybase.io](https://keybase.io) 帐户。 这是一种在多个在线网络中验证您的身份的密码安全方法。 钥匙键API允许我们检索您的钥匙键头像。 这就是您可以在验证器配置文件中添加徽标的方式。

```bash
artelad tx staking edit-validator
  --moniker="choose a moniker" \
  --website="https://cosmos.network" \
  --identity=6A0D65E29A4CBC8E \
  --details="To infinity and beyond!" \
  --chain-id=<chain_id> \
  --gas="auto" \
  --gas-prices="0.0025uart" \
  --from=<key_name> \
  --commission-rate="0.10" 
```

：：：警告
请注意，一些参数，例如 `commission-max-rate` 和 `commission-max-change-rate` 一旦验证器启动并运行，就无法更改。
:::

 **笔记** ： 这 `commission-rate` 价值必须遵守以下规则：

- 必须在0和验证者之间 `commission-max-rate` 
- 不得超过验证者的 `commission-max-change-rate` 最大
％点更改率 **每天**。 换句话说，验证器只能更改
它每天一次和内部一次 `commission-max-change-rate` 边界。

## 查看验证器描述

使用此命令查看验证者的信息：

```bash
artelad query staking validator <account_cosmos>
```

## 跟踪验证器签名信息

为了跟踪过去的验证器的签名，您可以使用 `signing-info` 命令：

```bash
artelad query slashing signing-info <validator-pubkey>\
  --chain-id=<chain_id>
```

## Unawail验证器

当验证器为 "jailed" 对于停机时间，您必须提交 `Unjail` 从运营商帐户的交易，以便能够再次获得块提议者奖励（取决于区域费用分布）。

```bash
artelad tx slashing unjail \
 --from=<key_name> \
 --chain-id=<chain_id>
```

## 确认您的验证器正在运行

如果以下命令返回任何内容，则您的验证器处于活动状态：

```bash
artelad query tendermint-validator-set | grep "$(artelad tendermint show-address)" 
```

现在，您应该在Atelad网络探险家之一中查看验证器。 您正在寻找 `bech32` 编码 `address` 在 `~/.gaia/config/priv_validator.json` 文件。

## 停止验证器

在尝试执行日常维护或计划即将进行的协调升级时，将您的验证器系统地，优雅地停止可能很有用。 您可以通过设置任何一个 `halt-height` 到您希望节点关闭或通过传递的高度 `--halt-height` 标记为 `artelad`。 该节点将在合作后以零出口代码关闭
块。

## 高级配置

您可以找到有关在该节点或验证器上运行的更多高级信息 [COMETBFT核心文档](https://docs.cometbft.com/v0.34/core/validators).

## 常见问题

### 问题#1：我的验证器 `voting_power: 0` 

您的验证者已入狱。 验证者被判入狱，即，如果他们至少不投票，就可以从主动验证器集中删除 `500` 最后 `10,000` 块，或者如果它们双重签名。

如果您因停机时间入狱，则可以将投票权恢复到验证者。 首先，如果您不使用 [宇宙](https://docs.cosmos.network/v0.45/run-node/cosmovisor.html) 和 `artelad` 没有运行，再次开始启动：

```bash
artelad start
```

等待您的完整节点赶上最新块。 然后，你可以 [取消验证器](#unjail-validator) 

提交UNHAIGHAIL交易后，再次检查验证器，以查看您的投票能力是否又回来了。

```bash
artelad status
```

您可能会注意到您的投票能力比以前少。 那是因为您被停机了！

### 问题#2：我的 `artelad` 由于 `too many open files` 

linux可以打开的默认文件数字（每个程序）是 `1024`。`artelad` 众所周知开放的开放量超过 `1024` 文件。 这导致该过程崩溃。 快速修复是运行 `ulimit -n 4096` （增加允许的打开文件的数量），然后使用 `artelad start`。 如果您正在使用 `systemd` 或另一个流程管理器启动 `artelad` （例如 [宇宙](https://docs.cosmos.network/v0.45/run-node/cosmovisor.html) ）这可能需要在该级别上进行一些配置。 样本 `systemd` 解决此问题的文件如下：

```toml
# /etc/systemd/system/artelad.service
[Unit]
Description=Cosmos Gaia Node
After=network.target

[Service]
Type=simple
User=ubuntu
WorkingDirectory=/home/ubuntu
ExecStart=/home/ubuntu/go/bin/artelad start
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
```

<！ -
 ![](./img/v1.png) 
 ![](./img/v2.png) 
 ![](./img/v3.png) 
 ![](./img/v4.png) 
 ![](./img/v5.png) 
 ![](./img/v6.png) 
 ![](./img/v7.png) 
 ![](./img/v8.png) 
 ![](./img/v9.png) 
 ![](./img/v10.png) 
 ![](./img/v11.png) 
 ![](./img/v12.png) 
 ![](./img/v13.png) 
 ![](./img/v14.png) 
 ![](./img/v15.png) 
-->