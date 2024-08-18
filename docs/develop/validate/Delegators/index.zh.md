---
sidebar_position：1
---

# 代表们指南

本文档包含了代表们通过命令行界面（CLI）与Artela网络进行交互的所有必要信息。

## 1.加入新的代表们帐户
```bash
artelad keys add <account_name>
```
 ![](./img/d1.png) 

## 2.到验证器
```bash
artelad tx staking delegate <validator_operator> <amount> \
--chain-id=<chain_id> \
--from=<key_name>
```
 ![](./img/d2.png) 

## 3.检查委托TX状态
```bash
artelad query tx <tx_hash>
```
 ![](./img/d3.png) 

## 4.检查验证器的均衡平衡
```bash
artelad query staking validator <validator_operator>
```
 ![](./img/d4.png) 

## 5.快速自我当前代表身份
```bash
artelad query staking delegations <delegator_addr>
```
 ![](./img/d5.png) 

## 6.快速自我奖励
```bash
artelad query distribution rewards <delegator_address> <validator_address>
```
 ![](./img/d6.png) 

## 7.问题目标代表们奖励
```bash
artelad query distribution rewards <delegator_address>
```
 ![](./img/d7.png) 

## 8. INCREASE代表人金额
```bash
artelad tx staking delegate <validator_operator> <amount> \
--chain-id=<chain_id> \
--from=<key_name>
```
 ![](./img/d8.png) 

## 9.确定奖励
```bash
artelad tx distribution withdraw-all-rewards \
  --chain-id=<chain_id> \
--from=<key_name>
```
 ![](./img/d9.png) 

## 10.Redelegate到其他验证器
```bash
artelad tx staking redelegate <srcValidatorAddress> <destValidatorAddress> <amountToRedelegate> 
--from <delegatorKeyName>
```
 ![](./img/d10.png) 

## 11.验证器的undelegate
```bash
artelad tx staking unbond <validatorAddress> <amountToUnbond> --from <delegatorKeyName>
```
 ![](./img/d11.png) 