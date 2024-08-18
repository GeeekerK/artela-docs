
# TestNet信息

 **选项1：通过RPC连接到Artela Testnet** 

您可以通过这些URL访问JSON-RPC端口服务：

https://betanet-rpc1.artela.network
https://betanet-rpc2.artela.network
https://api.zan.top/node/v1/artela/testnet/82ec5dae795f4d22a5f8238de7c886fc

 **选项2：建立自己的本地专用网络** 

有关详细的步骤，请参阅 [运行本地节点](./full-node-setup).

启动节点后，请在此处连接到JSON-RPC端口服务：

http://127.0.0.0.1:8545

## 关于测试网的公共信息

### 1.基本信息

```
Network Name : Artela TestNet
New RPC URL : https://betanet-rpc1.artela.network
ChainID (optional): 11822
Symbol (optional) : ART
Block Explorer URL (optional): https://betanet-scan.artela.network/
```

### 2.创世纪

 [创世记](./genesis.json) 


### 3.持续的节点

```json
f809f4fd17a9cf434b059af3e86262bbac3cb809@47.251.32.165:26656
8889b28795e8be109a532464e5cc074e113de780@47.251.54.123:26656
5c4ea81ac7b8a7f5202fcbe5fe790a6d6f61fb22@47.251.14.108:26656
```

### 4.值得信赖的块和高度

- **块高度** ：114011
- **块哈希** ：94077D92B3DE77C3AE94BEBF496601E7CBA3EEF7CD7E17BD8513F16D9D9DDA8712

> ⚠️NOTE：从<https://betanet-scan.artela.network>拿起最新的块和高度作为您值得信赖的块和高度。
>

### 5. RPC服务器

```bash
http://47.251.32.165:26657
http://47.251.54.123:26657
http://47.251.14.108:26657
```