# 部署Alaya测试网

## Alaya测试网

### 部署PlatON私有测试网

#### 安装PlatON源码

```bash
git clone https://hub.fastgit.org/PlatONnetwork/PlatON-Go.git
cd PlatON-GO
make all	# go env -w GO111MODULE=auto
```

#### 创建单节点

1. 生成节点公、私钥（nodeid、nodekey）和用于共识的公私钥（blspub、blskey）

   ```bash
   # 设定节点数据目录 ~/Projects/BlockChain/PlatON/platon-node/data
   mkdir -p ~/Projects/BlockChain/PlatON/platon-node/data && keytool genkeypair | tee >(grep "PrivateKey" | awk '{print $2}' > ~/Projects/BlockChain/PlatON/platon-node/data/nodekey) >(grep "PublicKey" | awk '{print $3}' > ~/Projects/BlockChain/PlatON/platon-node/data/nodeid) && keytool genblskeypair | tee >(grep "PrivateKey" | awk '{print $2}' > ~/Projects/BlockChain/PlatON/platon-node/data/blskey) >(grep "PublicKey" | awk '{print $3}' > ~/Projects/BlockChain/PlatON/platon-node/data/blspub)
   ```

   

2. 生成结果

   ```bash
   Address   :  0x2B4d5a61fF7e5417D872135163331320F01B9e3f
   PrivateKey:  29b540bcf7e0e95d32489cfd3c8d2436b89c1c70d1f6b632cfaa550ff226ca46
   PublicKey :  104f9e878bb895b7c2e0f75204dadc6243da2c8e1ba5db216a70e59e659a778c50b698793d95740707b12225af8c01586d884295c3d16dbc226ee588ec267066
   PrivateKey:  8c96e2ef6a83ab57993922e37c627e3066df3068f4aeb291cf0457f381060c57
   PublicKey :  a358ad3010758407dcbbd44443b0e42973fad7bfede2c310aaca46a9050340e11f2c6a68de2bd188e519564b9c66e312ddd6ad49a272632aca1506cbbad255df59f4625336c6fdcdef1b0a05efaaa1261a114992d9b2a30286e8301536a8ec95
   ```

> 说明：
>
> - nodeid：节点公钥(ID）文件，保存节点的公钥，用于标识节点身份
> - nodekey：节点私钥文件，保存节点的私钥，不能公开并且需要做好备份。
> - blspub：节点BLS公钥文件，保存节点的BLS公钥，用于共识协议中快速验证签名。
> - blskey：节点BLS私钥文件，保存节点的BLS私钥，不能公开并且需要做好备份。

3. 创建钱包文件（用MTool-Client替代）

   ```bash
   mkdir -p ~/Projects/BlockChain/PlatON/platon-node/data && platon --datadir ~/Projects/BlockChain/PlatON/platon-node/data account new
   # 密码 chainbridge
   main net Address: atp1akrtyuu84f0jsu4humxlqwfay8643n0zdggvaj
   other net Address: atx1akrtyuu84f0jsu4humxlqwfay8643n0z8w5xwc
   ```

4. 在platon-node文件夹下生成创世区块配置文件platon.json（修改nodeid、blspub、address为上述生成的信息）

   ```json
   {
       "config": {
           "chainId": 201030,
           "eip155Block": 3,
           "cbft": {
               "initialNodes": [
                 {
                       "node":"enode://104f9e878bb895b7c2e0f75204dadc6243da2c8e1ba5db216a70e59e659a778c50b698793d95740707b12225af8c01586d884295c3d16dbc226ee588ec267066@0.0.0.0:16789",
                       "blsPubKey":"a358ad3010758407dcbbd44443b0e42973fad7bfede2c310aaca46a9050340e11f2c6a68de2bd188e519564b9c66e312ddd6ad49a272632aca1506cbbad255df59f4625336c6fdcdef1b0a05efaaa1261a114992d9b2a30286e8301536a8ec95"
                 }
               ],
               "amount": 10,
               "period": 20000,
               "validatorMode": "ppos"
           },
           "genesisVersion": 3328
       },
       "economicModel":{
           "common":{
               "maxEpochMinutes":360,
               "maxConsensusVals":25,
               "additionalCycleTime":525960
           },
           "staking":{
               "stakeThreshold": 10000000000000000000000,
               "operatingThreshold": 1000000000000000000,
               "maxValidators": 101,
               "unStakeFreezeDuration": 8,
               "rewardPerMaxChangeRange": 500,
               "rewardPerChangeInterval": 10
           },
           "slashing":{
              "slashFractionDuplicateSign": 10,
              "duplicateSignReportReward": 50,
              "maxEvidenceAge":7,
              "slashBlocksReward":250,
              "zeroProduceCumulativeTime":30,
              "zeroProduceNumberThreshold":1,
              "zeroProduceFreezeDuration":7
           },
            "gov": {
               "versionProposalVoteDurationSeconds": 1209600,
               "versionProposalSupportRate": 6670,
               "textProposalVoteDurationSeconds": 1209600,
               "textProposalVoteRate": 5000,
               "textProposalSupportRate": 6670,          
               "cancelProposalVoteRate": 5000,
               "cancelProposalSupportRate": 6670,
               "paramProposalVoteDurationSeconds": 1209600,
               "paramProposalVoteRate": 5000,
               "paramProposalSupportRate": 6670      
           },
           "reward":{
               "newBlockRate": 50,
               "platonFoundationYear": 2,
               "increaseIssuanceRatio": 500
           },
           "innerAcc":{
               "platonFundAccount": "atx10spacq8cz76y2n60pl7sg5yazncmjuus7n6hw2",
               "platonFundBalance": 0,
               "cdfAccount": "atx17tfkaghs4vded6mz6k53xyv5cvqsl63h5gq7cw",
               "cdfBalance": 4000000000000000000000000
           }
       },
       "nonce": "0x0376e56dffd12ab53bb149bda4e0cbce2b6aabe4cccc0df0b5a39e12977a2fcd23",
       "timestamp": "0x5bc94a8a",
       "extraData": "0xd782070186706c61746f6e86676f312e3131856c696e757800000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
       "gasLimit": "4712388",
       "alloc": {
           "atx1akrtyuu84f0jsu4humxlqwfay8643n0z8w5xwc": {
               "balance": "1000000000000000000000000"
           },
       },
       "number": "0x0",
       "gasUsed": "0x0",
       "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000"
   }
   ```
   
   
   
5. 初始化创世区块

   `cd ~/Projects/BlockChain/PlatON/platon-node && platon --datadir ./data init platon.json`

   ```json
   INFO [02-07|17:52:46.730] addressPrefix  has set                   prefix=atx
   WARN [02-07|17:52:46.730] the address not compare current net      want=atx input=atp147txew2paj3y8kqthzelslxyyjmkzt0gwe99cr
   WARN [02-07|17:52:46.730] the address not compare current net      want=atx input=atp14cl7nrys9xlfcx6clpy4fs4rsasc2htdjz9unu
   INFO [02-07|17:52:46.730] Call CheckEconomicModel: check epoch and consensus round, config epoch duration="21600 s" perblock duration="2 s" round duration="500 s" real epoch duration="21500 s" consensus count of epoch=43
   INFO [02-07|17:52:46.730] Call CheckEconomicModel: additional cycle and epoch, config additional cycle duration="525960 min" real additional cycle duration="525675 min" epoch count of additional cycle=1467
   INFO [02-07|17:52:46.731] Maximum peer count                       ETH=50 LES=0 total=50
   WARN [02-07|17:52:46.731] the address not compare current net      want=atx input=atp1akrtyuu84f0jsu4humxlqwfay8643n0zdggvaj
   INFO [02-07|17:52:46.731] set path                                 package=snapshotdb path=/home/rjman/Projects/BlockChain/PlatON/platon-node/data/platon/snapshotdb
   INFO [02-07|17:52:46.731] Allocated cache and file handles         database=/home/rjman/Projects/BlockChain/PlatON/platon-node/data/platon/chaindata cache=16.78mB handles=16
   INFO [02-07|17:52:46.748] open snapshot db Allocated cache and file handles package=snapshotdb cache=0       handles=0  baseDB=true
   INFO [02-07|17:52:46.761] Writing custom genesis block             chainID=201030
   INFO [02-07|17:52:46.761] addressPrefix  has set                   prefix=atx
   INFO [02-07|17:52:46.761] Call CheckEconomicModel: check epoch and consensus round, config epoch duration="21600 s" perblock duration="2 s" round duration="500 s" real epoch duration="21500 s" consensus count of epoch=43
   INFO [02-07|17:52:46.761] Call CheckEconomicModel: additional cycle and epoch, config additional cycle duration="525960 min" real additional cycle duration="525675 min" epoch count of additional cycle=1467
   INFO [02-07|17:52:46.761] Init Govern parameters ... 
   WARN [02-07|17:52:46.761] cannot find current active version, The ActiveVersion List is nil 
   INFO [02-07|17:52:46.761] Write genesis version into genesis block genesis version=3328/0.13.0
   WARN [02-07|17:52:46.761] cannot find current active version, The ActiveVersion List is nil 
   INFO [02-07|17:52:46.761] Set SetYearEndBalance                    genesisReward=0
   INFO [02-07|17:52:46.761] Call genesisStakingData, Store genesis pposHash by stake data pposHash=0x7a33073805ae2e5b005d44ebc6eeb6d3641d60194c624cb44f4b5213a666f41d
   INFO [02-07|17:52:46.762] Persisted trie from memory database      nodes=11 size=1.96kB time=41.277µs gcnodes=0 gcsize=0.00B gctime=0s livenodes=1 livesize=-246.00B
   INFO [02-07|17:52:46.762] Successfully wrote genesis state         database=chaindata                                                                hash=0x5ba28e7ad4a6a80924b4ce4c4e665a4aa24a23493017b07b92f8b79e3ac4089e
   INFO [02-07|17:52:46.762] begin close snapshotdb                   package=snapshotdb path=/home/rjman/Projects/BlockChain/PlatON/platon-node/data/platon/snapshotdb
   INFO [02-07|17:52:46.762] snapshotdb closed                        package=snapshotdb
   INFO [02-07|17:52:46.762] Allocated cache and file handles         database=/home/rjman/Projects/BlockChain/PlatON/platon-node/data/platon/lightchaindata cache=16.78mB handles=16
   INFO [02-07|17:52:46.775] Writing custom genesis block             chainID=201030
   INFO [02-07|17:52:46.775] addressPrefix  has set                   prefix=atx
   INFO [02-07|17:52:46.775] Call CheckEconomicModel: check epoch and consensus round, config epoch duration="21600 s" perblock duration="2 s" round duration="500 s" real epoch duration="21500 s" consensus count of epoch=43
   INFO [02-07|17:52:46.775] Call CheckEconomicModel: additional cycle and epoch, config additional cycle duration="525960 min" real additional cycle duration="525675 min" epoch count of additional cycle=1467
   WARN [02-07|17:52:46.776] cannot find current active version, The ActiveVersion List is nil 
   INFO [02-07|17:52:46.776] Write genesis version into genesis block genesis version=3328/0.13.0
   WARN [02-07|17:52:46.776] cannot find current active version, The ActiveVersion List is nil 
   INFO [02-07|17:52:46.776] Set SetYearEndBalance                    genesisReward=0
   INFO [02-07|17:52:46.776] Call genesisStakingData, Store genesis pposHash by stake data pposHash=0x7a33073805ae2e5b005d44ebc6eeb6d3641d60194c624cb44f4b5213a666f41d
   INFO [02-07|17:52:46.776] Persisted trie from memory database      nodes=11 size=1.96kB time=47.318µs gcnodes=0 gcsize=0.00B gctime=0s livenodes=1 livesize=-246.00B
   INFO [02-07|17:52:46.776] Successfully wrote genesis state         database=lightchaindata                                                                hash=0x5ba28e7ad4a6a80924b4ce4c4e665a4aa24a23493017b07b92f8b79e3ac4089e
   ```

   

6. 启动节点

   `cd ~/Projects/BlockChain/PlatON/platon-node && platon --identity "platon" --datadir ./data --port 16789 --rpcaddr 127.0.0.1 --rpcport 6789 --rpcapi "db,platon,net,web3,admin,personal" --rpc --nodiscover --nodekey ./data/nodekey --cbft.blskey ./data/blskey`

## Alaya钱包 MTool-client

### 安装MTool-client

1. 下载MTool工具包 `wget http://download.alaya.network/alaya/mtool/linux/0.15.0/mtool-client.zip`

2. 解压工具包

3. `cd mtool-client`

4. 下载脚本 `wget http://download.alaya.network/opensource/scripts/mtool_install.sh`

5. 执行安装命令 `chmod +x mtool_install.sh && ./mtool_install.sh`

6. 重新启用会话窗口

   > 如果连接测试网，修改mtool-client下的config.properties中的chainId为测试网chainId（201030）
   >
   > （可选）设定环境变量：$MTOOLDIR为mtool-client目录路径

   ### 创建钱包

   1. `mtool-client account new staking`

   2. `$MTOOLDIR/keystore`下生成钱包文件`staking.json`，并打印如下信息

      ```go
      -name: staking
      -type: NORMAL
      -address: 
       mainnet: atp1u9rxrpqxe08zrvx0z6eaqwnn74y3m903q0p687
       testnet: atx1u9rxrpqxe08zrvx0z6eaqwnn74y3m9032fas55
      -public key: 0x192c3a81846dea566f162564431a6abc0e3c5035adb4ff2301f34bed6bdddacb875f916c63fb8cb4f97266b7fc0550e47912d89babe073eaf02feb871eae13a7
      **Important** write this Private Key in a safe place.
      It is the important way to recover your account if you ever forget your password.
      19671a7d7a1db312d53300c55b9457882425f499ae79f6dd2df4acd563e7a2e0
      **Important** write this mnemonic phrase in a safe place.
      It is the important way to recover your account if you ever forget your password.
      word banana relax liar slush mobile earth step oil room birth lake
      ```

      > 钱包地址格式调整为Bech32，其中：
      >
      > `atp1u9rxrpqxe08zrvx0z6eaqwnn74y3m903q0p687`：为主网账户地址，以atp开头；
      >
      > `atx1u9rxrpqxe08zrvx0z6eaqwnn74y3m9032fas55`：为测试网账户地址，以atx开头；
      >
      > `19671a7d7a1db312d53300c55b9457882425f499ae79f6dd2df4acd563e7a2e0` 为钱包私钥；
      >
      > `word banana relax liar slush mobile earth step oil room birth lake` 为助记词。
      >
      > 出于安全考虑，用户需对钱包私钥或助记词进行备份（可都进行备份，也可备份其中一个），钱包丢失时，可用于恢复。建议用户将助记词或者私钥备份到安全的存储介质上，如离线机器等。

   3. 恢复钱包

      + 私钥恢复 `mtool-client account recover -k staking`
      + 助记词恢复 `mtool-client account recover -m staking`

      > 恢复成功后会在目录`$MTOOLDIR/keystore`下生成钱包文件`staking.json`

   ### 钱包命令

   1. 查看钱包列表 `mtool-client account list`

   2. 查询余额 `mtool-client account balance $keystorename --config $MTOOLDIR/validator/validator_config.json`

      > $keystorename：钱包文件名称，如staking.json
      >
      > config：验证节点信息文件路径

      ```go
      mtool-client account balance staking.json --config $MTOOLDIR/validator/validator_config.json
      # 输出
      ➜ 
      Balanceof: atx1u9rxrpqxe08zrvx0z6eaqwnn74y3m9032fas55
      ATP:1000000
      ```

   3. 普通转帐

      `mtool-client tx transfer --keystore $MTOOLDIR/keystore/staking.json --amount "1000" --recipient atx18aut6tpuf632z40ggm7v4enypl9pw6mr9fcwkk --config $MTOOLDIR/validator/validator_config.json`

   ### 第二个钱包（用于交互）

   ```go
   -name: wallet	//$keystorename：钱包文件名称，wallet.json
   -type: NORMAL
   -address: 
    mainnet: atp18aut6tpuf632z40ggm7v4enypl9pw6mr00yy9u
    testnet: atx18aut6tpuf632z40ggm7v4enypl9pw6mr9fcwkk
   -public key: 0x30411534e15323ac9eab6aa6d889e38b8f39861aa6fd20cea78fe02355fcbd9d2adf2d86c02dd0a53f33d3e0aa090143b72363c3c2a1093cd8750a7772e7439c
   
   **Important** write this Private Key in a safe place.
   It is the important way to recover your account if you ever forget your password.
   c66f2028e08f0b359c0370afe20e9cbee6f58c3369f246ade7c43ebe7152eafd
   **Important** write this mnemonic phrase in a safe place.
   It is the important way to recover your account if you ever forget your password.
   box nurse coin genre equal check language actual better girl sunny state
   ```

   

