---
sidebar_position: 2
---

# Cosmos API

Artela 网络基于 CosmosSDK 和 CometBft 构建。

我们的网络还支持 Cosmos gRPC 和 CometBFT RPC。

## Cosmos gRPC

Artela 为 Cosmos SDK 中的所有集成模块提供 gRPC 端点。这简化了钱包和区块链浏览器的交互过程，使他们能够轻松参与权益证明机制和原生 Cosmos 交易和查询。

[Cosmos 工具](https://v1.cosmos.network/rpc/v0.45.1) 也适用于 Artela。

### EVM 模块

#### 查询

| 动作   | 方法                                              | 描述                                                         |
| ------ | ------------------------------------------------- | ------------------------------------------------------------ |
| `gRPC` | `artela.evm.v1.Query/Account`                     | 获取以太坊账户                                               |
| `gRPC` | `artela.evm.v1.Query/CosmosAccount`               | 获取以太坊账户的 Cosmos 地址                                 |
| `gRPC` | `artela.evm.v1.Query/ValidatorAccount`            | 从验证器共识地址获取以太坊账户                               |
| `gRPC` | `artela.evm.v1.Query/Balance`                     | 获取单个 EthAccount 的 EVM 面额余额。                        |
| `gRPC` | `artela.evm.v1.Query/Storage`                     | 获取单个账户所有代币的余额                                   |
| `gRPC` | `artela.evm.v1.Query/Code`                        | 获取单个账户所有币的余额                                     |
| `gRPC` | `artela.evm.v1.Query/Params`                      | 获取 x/evm 模块参数                                          |
| `gRPC` | `artela.evm.v1.Query/EthCall`                     | 实现 eth_call rpc api                                        |
| `gRPC` | `artela.evm.v1.Query/EstimateGas`                 | 实现 eth_estimateGas rpc api                                 |
| `gRPC` | `artela.evm.v1.Query/TraceTx`                     | 实现 debug_traceTransaction rpc api                          |
| `gRPC` | `artela.evm.v1.Query/TraceBlock`                  | 实现 debug_traceBlockByNumber 和 debug_traceBlockByHash rpc api |
| `GET`  | `/artela/evm/v1/account/{address}`                | 获取以太坊账户                                               |
| `GET`  | `/artela/evm/v1/cosmos_account/{address}`         | 获取以太坊账户的 Cosmos 地址                                 |
| `GET`  | `/artela/evm/v1/validator_account/{cons_address}` | 从验证器共识地址获取以太坊账户                               |
| `GET`  | `/artela/evm/v1/balances/{address}`               | 获取单个 EthAccount 的 EVM 面额余额。                        |
| `GET`  | `/artela/evm/v1/storage/{address}/{key}`          | 获取单个账户所有代币的余额                                   |
| `GET`  | `/artela/evm/v1/codes/{address}`                  | 获取单个账户所有代币的余额                                   |
| `GET`  | `/artela/evm/v1/params`                           | 获取 x/evm 模块的参数                                        |
| `GET`  | `/artela/evm/v1/eth_call`                         | 实现 eth_call rpc api                                        |
| `GET`  | `/artela/evm/v1/estimate_gas`                     | 实现 eth_estimateGas rpc api                                 |
| `GET`  | `/artela/evm/v1/trace_tx`                         | 实现 debug_traceTransaction rpc api                          |
| `GET`  | `/artela/evm/v1/trace_block`                      | 实现 debug_traceBlockByNumber 和 debug_traceBlockByHash rpc api |

#### 交易

| 动作   | 方法                           | 说明           |
| ------ | ------------------------------ | -------------- |
| `gRPC` | `artela.evm.v1.Msg/EthereumTx` | 提交以太坊交易 |
| `POST` | `/artela/evm/v1/ethereum_tx`   | 提交以太坊交易 |

### FEE 模块

#### 查询

| 动作   | 方法                           | 描述               |
| ------ | ------------------------------ | ------------------ |
| `gRPC` | `artela.fee.v1.Query/Params`   | 获取模块参数       |
| `gRPC` | `artela.fee.v1.Query/BaseFee`  | 获取区块基本费用   |
| `gRPC` | `artela.fee.v1.Query/BlockGas` | 获取使用的区块 gas |
| `GET`  | `/artela/fee/v1/params`        | 获取模块参数       |
| `GET`  | `/artela/fee/v1/base_fee`      | 获取区块基本费用   |
| `GET`  | `/artela/fee/v1/block_gas`     | 获取使用的区块 gas |

## CometBFT RPC

CometBFT 支持以下 RPC 协议：

* HTTP 上的 URI
* HTTP 上的 JSONRPC
* WebSockets 上的 JSONRPC

### 配置

可以通过调整 `$CMTHOME/config/config.toml` 文件中 `[rpc]` 部分下的参数或使用 `--rpc.X` 命令行
标志来配置 RPC。

默认 RPC 监听地址为 `tcp://127.0.0.1:26657`。

要设置其他地址，请将 `laddr` 配置参数设置为所需值。

可以通过设置 `cors_allowed_origins`、`cors_allowed_methods`、`cors_allowed_headers`
配置参数来启用 CORS（跨源资源共享）。

对于本地 RPC 节点测试，请更新 `[rpc]` 部分下的 `cors_allowed_origins` 属性。添加此 OpenAPI 文档托管的 URL，例如：

`cors_allowed_origins = ["http://localhost:8088"]`

或者，如果从官方文档站点进行测试：

`cors_allowed_origins = ["https://docs.cometbft.com"]`

### 参数

对于需要字符串或字节数组的参数，您可以使用带引号的字符串（如“abc”）或以 0x 为前缀的字符串（如 0x616263）。

需要字符串或字节数组的参数可以作为带引号的字符串传递，如 `"abc"` 或以 `0x` 为前缀的字符串（如 `0x616263`）。

### URI/HTTP

类似 REST 的接口。

```bash
curl localhost:26657/block?height=5
```

### JSONRPC/HTTP

JSONRPC 请求可以通过 HTTP POST 到根 RPC 端点。

```bash
curl --header "Content-Type: application/json" --request POST --data '{"method": "block", "params": ["5"], "id": 1}' localhost:26657
```

Artela TestNet 示例：

```bash
curl --header "Content-Type: application/json" --request POST --data '{"method": "block", "params": ["5"], "id": 1}' 47.251.14.47:26657
```

### JSONRPC/WebSockets

JSONRPC 请求也可以通过 WebSockets 进行。
WebSocket 端点位于 `/websocket`，例如 `localhost:26657/websocket`。

异步 RPC 函数（如事件 `subscribe` 和 `unsubscribe`）仅通过 websockets 提供。

例如，使用 [websocat](https://github.com/vi/websocat) 工具，您可以使用以下命令订阅“NewBlock”事件：

```bash
echo '{ "jsonrpc": "2.0","method": "subscribe","id": 0,"params": {"query": "tm.event='"'NewBlock'"'"} }' | websocat -n -t ws://127.0.0.1:26657/websocket
```

**可用 API 列表：** [Cometbft API](https://docs.cometbft.com/v0.38/rpc/#/Info)
