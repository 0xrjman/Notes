# 搭建第一条以太坊私有链

> 搭建环境

## 安装geth客户端

### 从[go-ethereum](https://github.com/ethereum/go-ethereum)源码构建

```bash
git clone https://github.com/ethereum/go-ethereum.git
cd go-ethereum
make geth
```

完成构建后查看geth版本

`./build/bin/geth version`

出现相应的版本信息即构建geth成功

## 配置私有链信息

### step1.创建账户

#### MetaMask账户1：gethGenesis

> 地址
>
> 0xa0CD0e9A12FD2331e6716B292994071f934759C0
>
> 私钥
>
> da5ac2529bf11911865b8dce55549fa4afd0b00ffb21b6e7b701011e430a065b
>
> 密码
>
> chainbridge
>
> 私密备份密语
>
> upgrade must army glove humor wasp giant curve state core clock tomato

#### MetaMask账户2：gethAlice

>地址
>
>0xC1593919F406ea0b2e39a014BE28dcEb9BA1e775
>
>私钥
>
>a2e77846ce777ee833179b9053cd826592e694e4fda73db412b6ac3735e4e620
>
>密码
>
>chainbridge

### step2.配置genesis.json

```json
{
	"config": {
		"chainId": 111
	},
	"difficulty": "2000",
	"gasLimit": "2100000",
	"alloc": {
		"0xa0CD0e9A12FD2331e6716B292994071f934759C0": {
			"balance": "1000000000000000000000"
		},
        "0xC1593919F406ea0b2e39a014BE28dcEb9BA1e775": {
            "balance": "500000000000000000000"
        }
	}
}
```

### step3.初始化

cd 进入自定义data路径，并创建genesis.json 

`geth --datadir . init genesis.json`

```json
INFO [02-17|18:36:29.431] Maximum peer count                       ETH=50 LES=0 total=50
INFO [02-17|18:36:29.431] Smartcard socket not found, disabling    err="stat /run/pcscd/pcscd.comm: no such file or directory"
INFO [02-17|18:36:29.433] Set global gas cap                       cap=25000000
INFO [02-17|18:36:29.433] Allocated cache and file handles         database=/home/rjman/Project/BlockChain/Ethereum/gethData/data/geth/chaindata cache=16.00MiB handles=16
INFO [02-17|18:36:29.447] Writing custom genesis block 
INFO [02-17|18:36:29.447] Persisted trie from memory database      nodes=4 size=478.00B time="64.999µs" gcnodes=0 gcsize=0.00B gctime=0s livenodes=1 livesize=0.00B
INFO [02-17|18:36:29.448] Successfully wrote genesis state         database=chaindata hash="e9a468…1eb27e"
INFO [02-17|18:36:29.448] Allocated cache and file handles         database=/home/rjman/Project/BlockChain/Ethereum/gethData/data/geth/lightchaindata cache=16.00MiB handles=16
INFO [02-17|18:36:29.456] Writing custom genesis block 
INFO [02-17|18:36:29.457] Persisted trie from memory database      nodes=4 size=478.00B time="77.114µs" gcnodes=0 gcsize=0.00B gctime=0s livenodes=1 livesize=0.00B
INFO [02-17|18:36:29.457] Successfully wrote genesis state         database=lightchaindata hash="e9a468…1eb27e"
```

## 启动geth

本地启动私有链

`geth --datadir . --networkid 111`

```json
......
INFO [02-16|23:01:59.983] Started P2P networking                   self=enode://11fb58dc2f7f3e00007da20b9746ba58eead0739babda69bdc4fad0f59763d7fc7b7454fbf218ccc00d5385122da96c0d6c9e4ed2e50eaf17b304cff3f18077f@127.0.0.1:30303
......
```

Or 使用console进行交互、打印运行日志到json文件、开启8545端口（默认）给rpc

`geth --datadir . --networkid 111 --rpc console 2>output.json`

Or 允许

`geth --datadir . --networkid 111 --rpc --rpcapi eth,web3,personal --allow-insecure-unlock console 2>output.json `

### console使用web3js与以太坊交互

​	console输入`web3`查询可交互对象的命令（exit退出console）

1. 查询余额
    + eth.getBalance("0xa0CD0e9A12FD2331e6716B292994071f934759C0")
    + eth.getBalance("0xC1593919F406ea0b2e39a014BE28dcEb9BA1e775")
    + web3.fromWei(eth.getBalance("0xa0CD0e9A12FD2331e6716B292994071f934759C0"),'ether')
    + web3.fromWei(eth.getBalance("0xC1593919F406ea0b2e39a014BE28dcEb9BA1e775"),'ether')

2. 查询账户

    + eth.accounts

3. 新建账户

    + personal.newAccount()

        + 密码: 111
        + 地址: 0xf106b7c8eaddfbff34ce5983ce55ffa31089ead3
        + 账户2
        + 密码: 111
        + 地址: 0x03a68491408465428a4464d1ca3ad7b878becbc9

        > 本地keystore文件夹生成相应json文件

    + eth.getBalance(eth.accounts[0])

4. 尝试转账

    + 解锁账户

        + personal.unlockAccount(eth.accounts[0])

    + eth.sendTransaction(from: eth.accounts[0], to: "", value: 1000)

        > 当前账户没有余额
        >
        > 1. （可选）personal.imporRawkey导入metamask账户的原始私钥到当前钱包
        > 2. mint产出ether

5. 挖矿产出ether

    + miner.start(1)

    + miner.stop
    + eth.blockNumber

6. 查询余额
   
    + web3.fromWei(eth.getBalance(eth.accounts[0]), 'ether')
7. 转账
    + eth.sendTransaction({from: eth.accounts[0], to: "0xC1593919F406ea0b2e39a014BE28dcEb9BA1e775", value: web3.toWei(50, 'ether')})
    + eth.sendTransaction({from: eth.accounts[0], to:  eth.accounts[1], value: web3.toWei(15, 'ether')})
    + 
    + web3.fromWei(eth.getBalance("0xC1593919F406ea0b2e39a014BE28dcEb9BA1e775"),'ether')
    + web3.fromWei(eth.getBalance(eth.accounts[0]), 'ether')
    + web3.fromWei(eth.getBalance(eth.accounts[1]), 'ether')

> account unlock with HTTP access is forbidden
>
> 问题原因：新版本geth，出于安全考虑，默认禁止了HTTP通道解锁账户
>
> 解决方法：允许eth,web3,personal不安全的解锁
>
> geth --rpc --rpcapi eth,web3,personal --allow-insecure-unlock

