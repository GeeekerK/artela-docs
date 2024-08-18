---
sidebar_position: 1
---

# EVM 兼容 API

Artela JSON-RPC 服务器提供用于连接 Artela 区块链并与 EVM 交互的 API。JSON-RPC 是一种无状态的轻量级远程过程调用 (RPC) 协议。它定义了各种数据结构及其处理规则。JSON-RPC 与多种传输协议兼容。特别是 Artela，它支持通过 HTTP 和 WebSocket 的 JSON-RPC。

Artela TestNet 示例：

```
   curl https://betanet-rpc1.artela.network -H "Content-Type:application/json" -X POST --data '{"jsonrpc":"2.0","method":"eth_chainId","params":[],"id":1}'
```

Artela 已实现大多数以太坊 RPC 方法（但有些方法仍在开发中），因此您可以使用以太坊基础设施毫无问题地连接到 Artela。下面标有勾号的 RPC 方法就是我们已经实现的方法：

| APIs                                    | Status |
|-----------------------------------------|--------|
| web3_clientVersion                      |        |
| web3_sha3                               |        |
| net_version                             | ✅     |
| net_listening                           |        |
| net_peerCount                           |        |
| debug_traceTransaction                  |        |
| debug_traceBlock                        |        |
| eth_protocolVersion                     |        |
| eth_syncing                             |        |
| eth_coinbase                            |        |
| eth_chainId                             | ✅     |
| eth_mining                              |        |
| eth_hashrate                            |        |
| eth_gasPrice                            | ✅     |
| eth_accounts                            | ✅     |
| eth_blockNumber                         | ✅     |
| eth_getBalance                          | ✅     |
| eth_getStorageAt                        | ✅     |
| eth_getTransactionCount                 | ✅     |
| eth_getBlockTransactionCountByHash      |        |
| eth_getBlockTransactionCountByNumber    |        |
| eth_getUncleCountByBlockHash            |        |
| eth_getUncleCountByBlockNumber          |        |
| eth_getCode                             | ✅     |
| eth_getLogs                             | ✅     |
| eth_sign                                |        |
| eth_signTransaction                     | ✅     |
| eth_sendTransaction                     | ✅     |
| eth_sendRawTransaction                  | ✅     |
| eth_call                                | ✅     |
| eth_estimateGas                         | ✅     |
| eth_getBlockByHash                      | ✅     |
| eth_getBlockByNumber                    | ✅     |
| eth_getTransactionByHash                | ✅     |
| eth_getTransactionByBlockHashAndIndex   |        |
| eth_getTransactionByBlockNumberAndIndex |        |
| eth_getTransactionReceipt               | ✅     |
| eth_getUncleByBlockHashAndIndex         |        |
| eth_getUncleByBlockNumberAndIndex       |        |
| eth_newFilter                           |        |
| eth_newBlockFilter                      |        |
| eth_newPendingTransactionFilter         |        |
| eth_uninstallFilter                     |        |
| eth_getFilterChanges                    |        |
| eth_getFilterLogs                       |        |
| eth_feeHistory                          | ✅     |
