# 环境准备
## 适用操作系统声明
系统支持以及版本建议：Centos（7.2+）、Ubuntu（16.04、18.04）、Redhat (7.4)
## 硬件配置建议
配置取决于自身业务，请根据实际情况搭配，以下是建议最低配置：
- 体验配置：CPU：2核心、内存：4GB、硬盘：30G
- 生产配置：CPU：4核心、内存：8GB、硬盘：200G

## 软件依赖声明
- 依赖 Docker，安装 Docker 参见 [Docker在线资料](https://yeasy.gitbooks.io/docker_practice/content/install/)。
- CITA 的 Docker 镜像托管在 [DockerHub](https://hub.docker.com/r/cita/cita-build)。 因为 CITA 是基于 Ubuntu 18.04 稳定版开发的，因此该镜像中封装了 Ubuntu 18.04 还有其他一些 CITA 运行所需要的配置和文件。
## 安装 CITA 客户端工具
1. 创建目录<br>
   `$ mkdir -p /data/cita`
2. 切换目录<br>
   `$ cd /data/cita/`
3. 下载CITA-CLI安装包<br>
   `$ wget https://github.com/cryptape/cita-cli/releases/download/0.19.6/cita-cli-x86_64-musl-tls-0.19.6.tar.gz`<br><br>
   **注：这里所下载的是 CITA-CLI 是 linux 系统下的二进制发布包，如果你使用 MacOS，请按以下命令下载对应系统的 CITA-CLI：**<br>
    `$ wget https://github.com/cryptape/cita-cli/releases/download/0.19.6/cita-cli-x86_64-mac-osx-tls-0.19.6.tar.gz`

4. 解压程序<br>
   `$ tar zxvf cita-cli-x86_64-musl-tls-0.19.6.tar.gz`
5. 复制 CITA-CLI 到 系统可执行文件目录下<br>
   `$ sudo cp -rp cita-cli /usr/local/bin/`

# 提交矿工节点申请
# 运行节点
## 启动节点
1. 拷贝发送压缩包的litex-chain到当前目录下，然后解压<br>
`$ tar -zxvf litex-chain ./`

2. 以节点 0 为例，启动节点<br>
`$ bin/cita setup litex-chain/0`<br>
`$ bin/cita start litex-chain/0`

## 验证节点是否运行正常
检查7个服务是否都启动<br>
`$ bin/cita top litex-chain/0`
```bash
user      1953     0  0 17:10 ?        00:00:00 cita-forever
user      1968  1953  0 17:10 ?        00:00:00 cita-auth -c auth.toml
user      1963  1953  0 17:10 ?        00:00:00 cita-bft -c consensus.toml -p privkey
user      1966  1953  0 17:10 ?        00:00:00 cita-chain -c chain.toml
user      1969  1953  0 17:10 ?        00:00:00 cita-executor -c executor.toml
user      1967  1953  0 17:10 ?        00:00:00 cita-jsonrpc -c jsonrpc.toml
user      1965  1953  0 17:10 ?        00:00:00 cita-network -c network.toml
```
## 停止节点
`$ bin/cita stop litex-chain/0`

## 其它操作
更多节点命令可通过bin/cita help来获取，或者查看 [CITA 命令说明](https://docs.citahub.com/zh-CN/cita/commands) 

**注：**
- **请不要先进到 bin 目录，再执行以上的部署操作**<br>
**错误示范：**<br>
`$ cd bin`<br>
`$ cita setup test-chain/0`
- **请勿在一台服务器上运行多个容器，因为虽然 CITA 在 Docker 中运行，但是容器并没有做网络隔离。**

- **请不要同时在 host 系统里面运行 CITA 以及相关的 RabbitMQ 等软件，以免造成端口冲突**

# 基本操作.
## 查看区块高度
1. 执行命令
   `$ cita-cli rpc blockNumber --url http://121.196.200.225:1337`
2. 返回结果
```bash
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x283"
}
```
其中 *result*表示本次查询时的块高度为*0x283*。 CITA 默认*3s* 出一个块，重复执行查询命令，可以观察到 result 在持续的增长

## 发送交易
1. 执行命令
```bash
    $ cita-cli rpc sendRawTransaction \
    --private-key "0x352416e1c910e413768c51390dfd791b414212b7b4fe6b1a18f58007fa894214" \
    --code "0x606060405234156100105760006000fd5b610015565b60e0806100236000396000f30060606040526000357c0100000000000000000000000000000000000000000000000000000000900463ffffffff16806360fe47b114604b5780636d4ce63c14606c576045565b60006000fd5b341560565760006000fd5b606a60048080359060200190919050506093565b005b341560775760006000fd5b607d60a3565b6040518082815260200191505060405180910390f35b8060006000508190909055505b50565b6000600060005054905060b1565b905600a165627a7a72305820942223976c6dd48a3aa1d4749f45ad270915cfacd9c0bf3583c018d4c86f9da20029" \
    --height 111146 \
    --url http://121.196.200.225:1337
```
2. 返回结果
```bash
{
  "jsonrpc": "2.0",
  "result": {
    "status": "OK",
    "hash": "0x16251c374ee87eae41cbd9203eea481b861738a19c19df9d3c6603b9fbe84478"
  },
  "id": 2
}
```
## 获取交易回执
1. 执行命令
```bash
$ cita-cli rpc getTransactionReceipt \
    --hash "0x16251c374ee87eae41cbd9203eea481b861738a19c19df9d3c6603b9fbe84478" \
    --url http://121.196.200.225:1337
```
2. 返回结果
```bash
{
  "jsonrpc": "2.0",
  "result": {
    "transactionHash": "0x16251c374ee87eae41cbd9203eea481b861738a19c19df9d3c6603b9fbe84478",
    "logs": [],
    "blockNumber": "0x1b234",
    "transactionIndex": "0x0",
    "cumulativeGasUsed": "0xafc8",
    "gasUsed": "0xafc8",
    "blockHash": "0xca3733ac87fab23dc3c6c9b644631c98a937b369183c44f5743c5179587a3028",
    "root": null,
    "errorMessage": null,
    "logsBloom": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
    "contractAddress": "0xd9ae0a3b3e856bf5d01061d99721cc4b136d7e26"
  },
  "id": 1
}}
```
## 其他命令
更多其他请参照[cli 命令](https://github.com/cryptape/cita-cli/blob/master/README-CN.md)


