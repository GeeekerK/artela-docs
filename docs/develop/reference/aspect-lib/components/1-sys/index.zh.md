# 系统

> 提供基本的系统级功能，包括日志，恢复，需求等。


## 进口

<！ -  @formatter：off->
```typescript
{
    sys
} from "@artela/aspect-libs";
```
<！ -  @formatter：on->>

## 系统

### 1.还原

> 回滚当前事务并将信息返回到节点。

<！ -  @formatter：off->
```typescript
    revert(message: string)
```
<！ -  @formatter：on->>

* 范围
*字符串：错误消息。

* 例子

<！ -  @formatter：off->
```typescript
    sys.revert("error message");
```
<！ -  @formatter：on->>

执行时 `sys.revert`，该程序将被中断以继续执行，并将在服务器上打印消息日志。

 **警告** 
*如果在联接点中触发“恢复”`PreContractCall, PostContractCall, PreTxExecute, PostTxExecute, and Operation`，它将在区块链中恢复交易。
*如果在 `VerifyTx` 尖端，它将从MEMPOOL中删除交易。

### 2.要求

>要求函数用于确认条件的有效性，如果发生错误，`termination program` 继续运行。

<！ -  @formatter：off->
```typescript
    require(condition: bool, message: string = ''): void
```
<！ -  @formatter：on->>

* 范围
*条件：布尔
*消息：字符串

* 例子

<！ -  @formatter：off->
```typescript
    sys.require(1 == 2, "Not equal")
```
<！ -  @formatter：on->>

如果条件评估为false，则将中断程序，并将在服务器上打印消息日志。

 **警告** 
*如果在联接点中触发“要求”`PreContractCall, PostContractCall, PreTxExecute, PostTxExecute, and Operation`，它将在区块链中恢复交易。
*如果在 `VerifyTx` 尖端，它将从MEMPOOL中删除交易。


### 3.日志

> 将信息记录到服务器。

<！ -  @formatter：off->
```typescript
    log(message: string): void
```
<！ -  @formatter：on->>

* 范围
*字符串：日志消息。

* 例子

<！ -  @formatter：off->
```typescript
{
    sys.log("print message");
}
```
<！ -  @formatter：on->>

日志打印通常用于程序调试中。 但是，打印太多日志将消耗系统性能，并且将在产品模式下限制此功能。