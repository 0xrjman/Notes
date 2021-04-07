# EVM智能合约

## HelloWorld

### 编译HelloWorld合约

#### step1. 创建目录

`mkdir HelloWorld && cd HelloWorld`

#### step2.初始化一个工程

`alaya-truffle init`

在操作完成之后，就有如下项目结构：

- contracts/: Solidity合约目录
- migrations/: 部署脚本文件目录
- test/: 测试脚本目录
- truffle-config.js: alaya-truffle 配置文件

#### step3.编写HelloWorld合约

`cd contracts`

`touch HelloWorld.sol`，编写如下内容

```go
pragma solidity ^0.5.17;
contract HelloWorld {
    string name;
    
    function setName(string memory _name) public returns(string memory){
        name = _name;
        return name;
    }
    
    function getName() public view returns(string memory){
        return name;
    }
}
```

#### step4.修改配置文件

`cd ../ && vim truffle-config.js`，添加（修改）下的内容

```go
compilers: {
      solc: {
            version: "^0.5.17",    // 此版本号与HelloWorld.sol中声明的版本号保持一致
      }
}
```

#### step5. 编译合约

`alaya-truffle compile`

在操作完成之后，生成如下目录结构：

- build/: Solidity合约编译后的目录
- build/contracts/HelloWorld.json HelloWorld.sol对应的编译文件

### 部署HelloWorld合约

#### step1.新增合约部署脚本文件

`cd migrations/ && touch 2_initial_helloworld.js`，编写内容如下

```go
const helloWorld = artifacts.require("HelloWorld"); //artifacts.require告诉alaya-truffle需要部署哪个合约，HelloWorld即之前写的合约类名
    module.exports = function(deployer) {
       deployer.deploy(helloWorld); //helloWorld即之前定义的合约抽象（部署带参数的合约失败，请参考FAQ部署带参数合约失败说明）
};
```

#### step2.修改truffle-config.js中链的配置信息

`vim truffle-config.js`

```go
networks: {
    development: {
       host: "127.0.0.1",     // 区块链所在服务器主机
        port: 6789,            // 链端口号(rpc)
        network_id: "*",       // Any network (default: none | 201030)
       from: "atx1u9rxrpqxe08zrvx0z6eaqwnn74y3m9032fas55", //部署合约账号的钱包地址
       //gas: 999999,
       //gasPrice: 50000000004,
    },
}
```

#### step3.解锁钱包账户

进入alaya-truffle控制台

`alaya-truffle console`

导入私钥（如果之前已导入可以跳过此步骤）

`web3.platon.personal.importRawKey("19671a7d7a1db312d53300c55b9457882425f499ae79f6dd2df4acd563e7a2e0","chainbridge");`

导入成功将看到私钥对应的地址：

`atx1u9rxrpqxe08zrvx0z6eaqwnn74y3m9032fas55`

解锁钱包账户

`web3.platon.personal.unlockAccount('atx1u9rxrpqxe08zrvx0z6eaqwnn74y3m9032fas55','chainbridge',999999);`

解锁成功后返回

`true`

#### step4.部署合约

`alaya-truffle migrate`

```go

2_initial_helloworld.js
=======================

   Deploying 'HelloWorld'
   ----------------------
   > transaction hash:    0xd780646b096f8d38375a39926a69b6c301c77788b574597654ba3efc8df43cc0
   > Blocks: 0            Seconds: 0
   > contract address:    atx1pczuraprfatag2xxcx46sgqlufy6hh8g7fca66
   > block number:        58848
   > block timestamp:     1612764958359
   > account:             atx1u9rxrpqxe08zrvx0z6eaqwnn74y3m9032fas55
   > balance:             999999.985720876
   > gas used:            242072
   > gas price:           20 gvon
   > value sent:          0 ATP
   > total cost:          0.00484144 ATP


   > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:          0.00484144 ATP


Summary
=======
> Total deployments:   2
> Final cost:          0.00789924 ATP
```

### 调用HelloWorld合约

#### step1. 进入alaya-truffle控制台

`alaya-truffle console`

#### step2.与合约交互

```bash
let instance = await HelloWorld.deployed()
instance	# output
# 调用合约方法
instance.getName()
instance.setName("RJman's first Contract")
instance.getName()
```

#### step4. 构建合约对象

```go
//在console中定义`abi`(可以从HelloWorld/build/contracts/HelloWorld.json文件中获取到)
var abi = [{"constant":false,"inputs": [{"internalType": "string","name": "_name","type": "string"}],"name": "setName","outputs": [{"internalType": "string","name": "","type": "string"}],"payable": false,"stateMutability": "nonpayable","type": "function"},{"constant": true,"inputs": [],"name": "getName","outputs": [{"internalType": "string","name": "","type": "string"}],"payable": false,"stateMutability": "view","type": "function"}];`
//定义部署合约时获取的地址
var contractAddr = 'atx1pczuraprfatag2xxcx46sgqlufy6hh8g7fca66';
//创建合约对象
var helloWorld = new web3.platon.Contract(abi,contractAddr);
```

- `abi` 是合约提供给外部调用时的接口，每个合约对应的abi在编译后的文件中，如：`HelloWorld/build/contracts/HelloWorld.json` 中可以找到

- `contractAddr` 在部署合约成功后有一个contract address

- `helloWorld` 就是构建出来与链上合约交互的合约对象抽象

调用合约
`helloWorld.methods.setName("hello world").send({from: 'atx1u9rxrpqxe08zrvx0z6eaqwnn74y3m9032fas55'}).on('receipt', function(receipt) {console.log(receipt);}).on('error', console.error);`

- `helloWorld` 是之前构建的合约对象
- `methods` 固定语法,指量后面紧跟合约的方法名
- `setName` 是我们HelloWorld合约中的一个方法，有一个String类型的入参，此处入参为`hello world`
- `from` 调用者的钱包地址
- `on` 是监听合约处理结果事件，此处如果成功将打印回执，失败输出错误日志

```go
//成功返回以下数据
{
  blockHash: '0xeafa0d336f46098120abeabfc6eb24de1d6405370722b912c2f6ea8324f5a57e',
  blockNumber: 64144,
  contractAddress: null,
  cumulativeGasUsed: 25346,
  from: 'atx1u9rxrpqxe08zrvx0z6eaqwnn74y3m9032fas55',
  gasUsed: 25346,
  logsBloom: '0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000',
  status: true,
  to: 'atx1pczuraprfatag2xxcx46sgqlufy6hh8g7fca66',
  transactionHash: '0xac5370e52cd9b904390941427dccbff989212535645316f8f6d4b1b94aa3ff61',
  transactionIndex: 0,
  events: {}
}
```

`helloWorld.methods.getName().call(null,**function**(error,result){console.log("name is:" + result);})`

```go
//成功返回以下数据
'hello world'
```

## 众筹合约

### 合约代码

```go
pragma solidity ^0.5.17;

contract CrowdFunding {
    address payable public beneficiaryAddress = address("atx1u9rxrpqxe08zrvx0z6eaqwnn74y3m9032fas55"); //受益人地址，设置为合约创建者
    uint256 public fundingGoal = 100 atp;  //众筹目标，单位是ATP
    uint256 public amountRaised = 0; //已筹集金额数量， 单位是VON
    uint256 public deadline; //截止时间
    uint256 public price;  //代币价格
    bool public fundingGoalReached = false;  //达成众筹目标
    bool public crowdsaleClosed = false; //众筹关闭

    mapping(address => uint256) public balance; //保存众筹者对捐赈的金额
    mapping(address => uint256) public tokenMap; //保存众筹者所拥有的代币数量
    //记录已接收的ATP通知
    event GoalReached(address _beneficiaryAddress, uint _amountRaised);
    //转帐通知
    event FundTransfer(address _backer, uint _amount, bool _isContribution);
    //校验地址是否为空
    modifier validAddress(address _address) {
        require(_address != address("atx1qqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqq89qwkc") || _address != address("atp1qqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqdruy9j"));
        _;
    }

    /**
     * 初始化构造函数
     *
     * @param _fundingGoalInlats 众筹ATP币总量
     * @param _durationInMinutes 众筹截止,单位是分钟
     */
    constructor (
        uint _fundingGoalInlats,
        uint _durationInMinutes
    )public {
        beneficiaryAddress = msg.sender;
        fundingGoal = _fundingGoalInlats * 1 atp;
        deadline = now + _durationInMinutes * 1 minutes;
        price = 500 finney; //1个ATP币可以买 2 个代币
    }

    /**
     * 默认函数
     *
     * 默认函数，可以向合约直接打款
     */
    function () payable external {
        //判断是否关闭众筹
        require(!crowdsaleClosed);
        uint amount = msg.value;
        //捐款人的金额累加
        balance[msg.sender] += amount;
        //捐款总额累加
        amountRaised += amount;
        //转帐操作，转多少代币给捐款人
        tokenMap[msg.sender]  += amount / price;
        emit FundTransfer(msg.sender, amount, true);
    }

    /**
     * 判断是否已经过了众筹截止限期
     */
    modifier afterDeadline() { if (now >= deadline) _; }

    /**
     * 检测众筹目标是否已经达到
     */
    function checkGoalReached() public afterDeadline payable{
        if (amountRaised >= fundingGoal){
            //达成众筹目标
            fundingGoalReached = true;
            emit GoalReached(beneficiaryAddress, amountRaised);
        }
        //关闭众筹
        crowdsaleClosed = true;
    }
    /**
     * 收回资金
     *
     * 检查是否达到了目标或时间限制，如果有，并且达到了资金目标，
     * 将全部金额发送给受益人。如果没有达到目标，每个贡献者都可以退回
     * 他们贡献的金额
     */
    function safeWithdrawal() public afterDeadline {
        //如果没有达成众筹目标
        if (!fundingGoalReached) {
            //获取合约调用者已捐款余额
            uint amount = balance[msg.sender];
            if (amount > 0) {
                //返回合约发起者所有余额
                msg.sender.transfer(amount);
                emit FundTransfer(msg.sender, amount, false);
                balance[msg.sender] = 0;
            }
        }
        //如果达成众筹目标，并且合约调用者是受益人
        if (fundingGoalReached && beneficiaryAddress == msg.sender) {
            //将所有捐款从合约中给受益人
            beneficiaryAddress.transfer(amountRaised);
            emit FundTransfer(beneficiaryAddress, amountRaised, false);
        }
    }
}
```

### Alaya-truffle migrate

```bash
2_initial_CrowdFunding.js
=========================

   Deploying 'CrowdFunding'
   ------------------------
   > transaction hash:    0x0eeb3e7e18e30bb336d0b695fbb116db4e80c56de5c501471505badebdea8f64
   > Blocks: 0            Seconds: 0
   > contract address:    atx13lgc4trl0v3askm0vhpwnznyrjxlz9njk3y3ff
   > block number:        63925
   > block timestamp:     1612770548548
   > account:             atx1u9rxrpqxe08zrvx0z6eaqwnn74y3m9032fas55
   > balance:             999999.970834733
   > gas used:            417795
   > gas price:           20 gvon
   > value sent:          0 ATP
   > total cost:          0.0083559 ATP


   > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:           0.0083559 ATP


Summary
=======
> Total deployments:   2
> Final cost:          0.01141346 ATP
```

### 与众筹合约交互

#### step1.进入控制台

`alaya-truffle console`

#### step2.构建众筹合约对象

[压缩json为一行](https://www.sojson.com/yasuoyihang.html)

```go
//众筹合约的ABI，从编译后的文件获取
var abi = [{"inputs":[{"internalType":"uint256","name":"_fundingGoalInlats","type":"uint256"},{"internalType":"uint256","name":"_durationInMinutes","type":"uint256"}],"payable":false,"stateMutability":"nonpayable","type":"constructor"},{"anonymous":false,"inputs":[{"indexed":false,"internalType":"address","name":"_backer","type":"address"},{"indexed":false,"internalType":"uint256","name":"_amount","type":"uint256"},{"indexed":false,"internalType":"bool","name":"_isContribution","type":"bool"}],"name":"FundTransfer","type":"event"},{"anonymous":false,"inputs":[{"indexed":false,"internalType":"address","name":"_beneficiaryAddress","type":"address"},{"indexed":false,"internalType":"uint256","name":"_amountRaised","type":"uint256"}],"name":"GoalReached","type":"event"},{"payable":true,"stateMutability":"payable","type":"fallback"},{"constant":true,"inputs":[],"name":"amountRaised","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[{"internalType":"address","name":"","type":"address"}],"name":"balance","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"beneficiaryAddress","outputs":[{"internalType":"address payable","name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"crowdsaleClosed","outputs":[{"internalType":"bool","name":"","type":"bool"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"deadline","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"fundingGoal","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"fundingGoalReached","outputs":[{"internalType":"bool","name":"","type":"bool"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"price","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[{"internalType":"address","name":"","type":"address"}],"name":"tokenMap","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[],"name":"checkGoalReached","outputs":[],"payable":true,"stateMutability":"payable","type":"function"},{"constant":false,"inputs":[],"name":"safeWithdrawal","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"}];
//migrate的众筹合约地址
var contractAddr = 'atx13lgc4trl0v3askm0vhpwnznyrjxlz9njk3y3ff';
var crowdFunding = new web3.platon.Contract(abi,contractAddr);  
```

#### step4.查询众筹金额

```go
crowdFunding.methods.amountRaised().call(null,function(error,result){console.log("result:" + result);}); //查询已筹集金额
```

#### step5.众筹者判断众筹是否成功，然后获取收益

```go
crowdFunding.methods.safeWithdrawal().send({from:'atx1u9rxrpqxe08zrvx0z6eaqwnn74y3m9032fas55'}).on('data', function(event){ console.log(event);}).on('error', console.error); 
//成功返回如下信息
{
  blockHash: '0x6aa5feb50a1db5462c576a5b575dd36d4f9ed50dc374f8f69018d4d16c72c2c3',
  blockNumber: 65425,
  contractAddress: null,
  cumulativeGasUsed: 22301,
  from: 'atx1u9rxrpqxe08zrvx0z6eaqwnn74y3m9032fas55',
  gasUsed: 22301,
  logsBloom: '0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000',
  status: true,
  to: 'atx13lgc4trl0v3askm0vhpwnznyrjxlz9njk3y3ff',
  transactionHash: '0x05dd61fc8afea39b6ff5508d6f1e583d0db9e8cb0bcbd95bc18b6339ff7538c1',
  transactionIndex: 0,
  events: {}
}
```



