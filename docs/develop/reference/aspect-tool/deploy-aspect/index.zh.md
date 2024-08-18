# 部署方面

部署一个方面类似于部署智能合约，涉及二进制代码和其他的发布
区块链的必要元数据。 此过程导致创建一个方面地址，使其可以在
网络。

## 命令

您可以使用以下命令部署方面：

```bash
  npm run aspect:deploy -- --skfile {privateKey-path} \
                         --wasm ./build/release.wasm \
                         --properties [{\"key\":\"k\",\"value\":\"v\"},{\"key\":\"k1\",\"value\":\"v1\"}] \
                         --joinPoints {join point} \
                         --gas 200000
```
 **选项：** 
> *  -   - 瓦斯： * WASM路径。 喜欢： `--wasm ./build/release.wasm ` 
> *  -  joinpoints：指定哪个联接点生效，点列表 `PreContractCall PostContractCall PreTxExecute PostTxExecute VerifyTx`。 需要，例如： `--joinPoints PreContractCall PostContractCall PreTxExecute PostTxExecute VerifyTx` 
> *  -  Skfile：发送者的私人关键路径。**选修的**，默认值 `./privateKey.txt`.
> *  -  Properties：方面属性。**选修的**， 喜欢： `--properties [{\"key\":\"k\",\"value\":\"v\"},{\"key\":\"k1\",\"value\":\"v1\"}]` 
> *  - 加气：喜欢 `200000`，，，，**选修的**，默认值 `7000000`.

---

### Example

```shell
## usage 1: deploy a aspect './build/release.wasm' using default private key './privateKey.txt'
npm运行方面：部署 -   - 沃斯./build/release.wasm \
                          --joinPoints PreContractCall PostContractCall

## usage 2: deploy a aspect './build/release.wasm' using private key './privateKey2.txt'
NPM运行方面：部署 -  -Skfile ./privatekey2.txt \
-   - 瓦斯./build/release.wasm \
                          --JOINPOINTS PREFTRACTCALL POSTCOANCALL

## usage 3: deploy a aspect './build/release.wasm' with  properties
NPM运行方面：部署 -  -Skfile ./privatekey2.txt \
-   - 瓦斯./build/release.wasm \
                          --joinPoints PreContractCall PostContractCall
                          --properties [{\"key\" ：\"k\"，\"value\" ：\"v\"}，{\"key\" ：\"k1\"，\"value\" ：\"v1\"]]

```

### Command Output

当前部署成功，并打印了交易的收据。 可以记录 `aspectID`，一些命令将依靠此值。

```shell
{
Blockhash：'0x03D2A48D6F6281C0A32A20718B475F260D82068342F81CCF7EF8 ....'，
blocknumber：377，
合同编辑：null，
累积加used：0，
来自：'0x773B8DA8DE01C9A35DCB74E4E4C204C4B59 ....'，，
气体：9000001，
日志：[]，
logsbloom：'0x0000000000000000000000000 ....'，
状态：是的，
到：'0x000000000000000000000000000000000000A27E14'，
transActionHash：'0x4731CAFF311FC29146F61F93DB1DED27DCE4EC266A165029D3D283F83 ...'，
transActionIndex：0，
类型：'0x0'，
axpackAddress：'0xef3A9A495A80EA681797A721FD4708DB0E ....'
}
==部署appectid == 0xef3A9A495A80EA681797A721FD4708DB0E ...
```

## Customize

create-account命令的逻辑写在 `scripts/aspect-deploy.cjs` 文件，主要依靠
由[@artela/web3]提供的实现（/develop/develop/client/artela-web3.js）API。
如果需要，您可以在此文件中修改逻辑以实现您的特定功能。

此外，您可以修改 `project.config.json` 在项目root文件夹中[设置网络配置。]（/开发/参考/appect/expact-tool/config#2network-rpc）。