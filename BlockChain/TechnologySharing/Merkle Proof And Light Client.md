# How MMR implements FlyClient

## Share Content

1. Merkle Tree Summary
2. Merkle Mountain Range
3. Light Client And FlyClient

![BirdLine](https://cdn.jsdelivr.net/gh/RJman-self/resources@master/assets/1618824406139-1618824406138.png)

## Merkle Tree Summary

### BTC 区块

BTC`区块` = `元数据` + `交易列表`

> BTC区块头占 80bytes，PrevBlock和MerkleRoot各占32bytes

![BTC Header](https://cdn.jsdelivr.net/gh/RJman-self/resources@master/assets/1618823510646-1618823510644.png)

> coinbase交易：奖励矿工（打包+手续费）
> 奖励包括打包交易的奖励、除coinbase交易外的其他交易手续费总和

### Merkle Tree

![Merkle Tree](https://cdn.jsdelivr.net/gh/rjman-self/resources/assets/20210409163441.png)

1. 典型应用: **足够精确地验证某笔交易在某个区块上**
2. 作用

   + 提供了一种证明数据的完整性和有效性的方法
      + => 精确地验证某笔交易在某个区块
         + => `验证交易的存在性`
   + 可以大大减少为了验证目的而必须维护的数据量
      + => 降低验证交易所需的硬件成本
         + => `降低验证所需的硬件成本`
   + 进行验证的消耗小
      + => 信息传输量小
         + => `提高验证效率`
   > 成本低、效率高、技术可行 => `轻节点`

![BirdLine](https://cdn.jsdelivr.net/gh/RJman-self/resources@master/assets/1618824406139-1618824406138.png)

## Merkle Mountain Range

1. Merkle Tree声明Merkle Root需要已知所有的叶子结点
   + 轻节点需要在本地存储所有的块头
2. 每次插入新的区块时，Merkle Tree需要重新计算Merkle Root
   + 区块不断产生，意味着不停地重新计算Merkle Root
   + 如果叶子结点数量很大的话，计算量会非常巨大
3. MMR支持动态插入叶子结点并计算Root，且不会对之前已有的数据产生影响

![MT](https://cdn.jsdelivr.net/gh/RJman-self/resources@master/assets/1619661865181-1619661865176.png)

### MMR

#### Basic Operation

![MMR](https://cdn.jsdelivr.net/gh/RJman-self/resources@master/assets/1619684039320-1619684039318.png)

### Bagging the peak

![MMR Root-0](https://cdn.jsdelivr.net/gh/RJman-self/resources@master/assets/1619614553832-1619614553827.png)

![MMR Root-1](https://cdn.jsdelivr.net/gh/RJman-self/resources@master/assets/1619684068877-1619684068871.png)

![MMR Root-2](https://cdn.jsdelivr.net/gh/RJman-self/resources@master/assets/1619614361214-1619614361208.png)

![MMR Root-3](https://cdn.jsdelivr.net/gh/RJman-self/resources@master/assets/1619612830987-1619612830979.png)

![MMR Root-4](https://cdn.jsdelivr.net/gh/RJman-self/resources@master/assets/1619613321397-1619613321388.png)

![MMR Root-5](https://cdn.jsdelivr.net/gh/RJman-self/resources@master/assets/1619683201067-1619683201064.png)

### MMR Proof

#### Construct merkle proof

1. 构造该叶子到山峰的proof
2. 将右侧的山峰加入proof
3. 将左侧的山峰从右到左加入proof

![MMR Proof](https://cdn.jsdelivr.net/gh/RJman-self/resources@master/assets/1619602107161-1619602107158.png)

![MMR Proof](https://cdn.jsdelivr.net/gh/RJman-self/resources@master/assets/1619682606313-1619682606310.png)

![MMR Proof](https://cdn.jsdelivr.net/gh/RJman-self/resources@master/assets/1619682582488-1619682582484.png)

![MMR Root](https://cdn.jsdelivr.net/gh/RJman-self/resources@master/assets/1619612770820-1619612770812.png)

![BirdLine](https://cdn.jsdelivr.net/gh/RJman-self/resources@master/assets/1618824406139-1618824406138.png)

## Light Client(Wallet) And FlyClient

### Light Client

#### 简单支付验证（`S`implified `P`ayment `V`erification）

1. 若Merkle Tree不作验证，区块头的信息只能用作共识，具体交易要从`区块体`中`检索`

   + B声称向A发送了一笔交易，A如果要对这笔交易进行验证
   + `交易验证`：B是否曾经有足够的金额，接下来遍历后续账本，B是否已经支出金额给别人（双花），B是否有账户的支配权，这一过程需要得到完整的区块链

2. 若使用Merkle Tree作验证，`区块头`的Merkle Root能作`Merkle Proof`，证明交易的存在性
   + `支付验证`：B的交易是否得到验证（被区块打包），得到多少确认数

![SPV](https://cdn.jsdelivr.net/gh/RJman-self/resources@master/assets/1619594054812-image-20210420104821825.png)

### FlyClient

#### NIPoPow

![Pow](https://cdn.jsdelivr.net/gh/RJman-self/resources@master/assets/1619599387130-1619599387127.png)

![PoPow](https://cdn.jsdelivr.net/gh/RJman-self/resources@master/assets/1619598466664-1619598466662.png)

#### FlyHeader

当轻节点需要验证一笔交易时，需要获取：

这笔交易所在区块及证明这个区块的 Merkle Proof；
这笔交易在区块中的信息及其 Merkle Proof；

![FlyHeader](https://cdn.jsdelivr.net/gh/RJman-self/resources@master/assets/1619667241298-1619667241289.png)

![BirdLine](https://cdn.jsdelivr.net/gh/RJman-self/resources@master/assets/1618824406139-1618824406138.png)

## Reference

https://github.com/mimblewimble/grin/blob/master/doc/translations/mmr_KR.md
https://nipopows.com/
https://eprint.iacr.org/2019/226.pdf
https://www.youtube.com/watch?v=uA1dw1qDytM
https://github.com/paritytech/substrate/tree/master/frame/merkle-mountain-range/src
https://crates.io/crates/merklemountainrange
https://www.hashwei.cn/t/topic/36
