# Polkadot Bridges

## What are bridges

![](https://cdn.jsdelivr.net/gh/RJman-self/resources@master/assets/1618995093149-1618995093145.png)

### 充值和赎回

1. 用户A先抵押（Chain A）
2. 验证已抵押  （Relayer），保证交易安全
3. 放款给用户B（Chain B）

Relayer：在A网络上受到抵押款，在B网络上放款

#### 充值的资金流向

| 交易名称      | 发送方             | 接收方             |
| ------------- | ------------------ | ------------------ |
| 用户A`抵押`   | Chain A上的 用户A  | Chain A上的Relayer |
| Relayer`发行` | Chain B上的Relayer | Chain B上的用户B   |

> 验证交易的存在性 => 验证用户A成功进行抵押 => Relayer发行 => 完成`跨链` 

#### 交易的存在方式

+ 区块头 Block `Body`：在区块上存在对应交易的数据
  + 遍历区块成功打包的交易，若遍历成功则存在

+ 区块体 Block `Header`：在轻节点存在对应交易的hash
  + 

### Relayer

转账时机：`验证`在`原来的Chain A（验证对象）`上存在`交易`，在Chain B上发送交易

#### Substrate  to Substrate

交易安全：

#### Platdot









## Polkadot Bridge

### Header Sync

### Header Relayer

### Message Delivery

### Message Dispatch

### 