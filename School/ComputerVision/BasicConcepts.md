# CV_Concepts

## Basic Concept

`均值`：样本集合的中间点

`标准差`（散布度）：各个样本点到均值的距离和的平均值

`方差Variant`：标准差的平方

> n-1而不是n是为了以较小的样本集更好地逼近总体的标准差（无偏估计）
>
> 方差是标准差的平方，度量各个维度偏离其均值的程度

![均值-标准差-方差](http://images.cnitblog.com/blog/397158/201307/24151741-557883992e954590b30407d4ed4a2005.jpg)

`协方差 Covariance`：

<img src="https://img-blog.csdnimg.cn/20181229094537124.png" alt="协方差定义" style="zoom:80%;" />

 当X,Y是同一个随机变量时，X与其自身的协方差就是X的方差，可以说**方差是协方差的一个特例**。

<img src="https://img-blog.csdnimg.cn/20181229094740204.png" alt="方差和协方差" style="zoom:80%;" />

`协方差相关系数`：

由于两个协方差无法直接比较(如随机变量X、Y、Z，协方差Cov(X,Y)和Cov(X,Z)无法比较)，通过方差Var(X)和Var(Y)对协方差进行归一化(**Unit**),得到协方差相关系数的取值范围为[-1,1]。1表示完全线性相关，0表示完全线性无关，-1表示完全线性负相关。

> **对于二维随机变量**![[公式]](https://www.zhihu.com/equation?tex=%28X%2CY%29) **，各自的方差为：**![[公式]](https://www.zhihu.com/equation?tex=Var%28X%29%3D%5Csigma%5E2_X%2C%5Cquad+Var%28Y%29%3D%5Csigma%5E2_Y) **则：** ![[公式]](https://www.zhihu.com/equation?tex=%5Crho_%7BXY%7D%3D%5Cfrac%7BCov%28X%2CY%29%7D%7B%5Csigma_X%5Csigma_Y%7D%5C%5C) **称为随机变量**![[公式]](https://www.zhihu.com/equation?tex=X) **和**![[公式]](https://www.zhihu.com/equation?tex=Y) **的 相关系数 。**

![协方差系数计算1](https://www.zhihu.com/equation?tex=%5Crho_%7BXY%7D%3D%5Cfrac%7BCov%28X%2CY%29%28%E5%8E%98%E7%B1%B3%5Ccdot%E5%85%AC%E6%96%A4%29%7D%7B%5Csigma_X%28%E5%8E%98%E7%B1%B3%29%5Csigma_Y%28%E5%85%AC%E6%96%A4%29%7D%3D%5Cfrac%7BCov%28X%2CY%29%7D%7B%5Csigma_X%5Csigma_Y%7D%5C%5C)

![协方差系数计算2](https://www.zhihu.com/equation?tex=%5Crho_%7BYZ%7D%3D%5Cfrac%7BCov%28Z%2CY%29%28%E5%B2%81%5Ccdot%E5%85%AC%E6%96%A4%29%7D%7B%5Csigma_Z%28%E5%B2%81%29%5Csigma_Y%28%E5%85%AC%E6%96%A4%29%7D%3D%5Cfrac%7BCov%28Z%2CY%29%7D%7B%5Csigma_Z%5Csigma_Y%7D%5C%5C)

通过除以标准差，消去单位，得到相关系数，就能够对Cov(X,Y)和Cov(Z,Y)进行比较。若

![协方差相关系数计算结果](https://www.zhihu.com/equation?tex=%5Crho_%7BXY%7D%3D0.7%2C%5Cquad+%5Crho_%7BZY%7D%3D0.53%5C%5C)

则可以得出：相较于年龄(*Z*), 身高(*Y*)与体重(*X*)的正相关关系更强烈

[参考知乎（马同学）的回答](https://www.zhihu.com/question/20852004)

`协方差矩阵`：n个变量X1、X2、···Xn，两两之间的协方差，就可以组成协方差矩阵，n维的数据集就需要计算 n! / ((n-2)!*2) 个协方差，$$C^2_n$$

![协方差矩阵](https://img-blog.csdn.net/2018071817155229?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMxMDczODcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

`点积`：一个向量在另一向量上的投影（重合程度）

## Pro

### 卷积和池化

1. 卷积：特征提取，将图像中符合条件（如激活值越大越符合条件）的部分筛选出来。

```md
输入矩阵: W1 * H1 * D1
输出特征: W2 * H2 * D2
影响参数
	- `F` 卷积核尺寸
	- `S` 卷积步长
	- `P` 零填充数量
	- `K` 卷积核个数

W2 = (W1 - F + 2 * P) / S + 1
H2 = (H1 - F + 2 * P) / S + 1
D2 = K
```

![卷积](https://img-blog.csdnimg.cn/20210404145928812.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3BlYWNlZmFpcnk=,size_16,color_FFFFFF,t_70)

2. 激活：增加非线性的计算
3. 池化
   + 降低特征响应图尺寸，减少卷积计算量
   + 增大感受野，特征响应图变小之后，卷积核能观察的东西更广了，就能够提取到更加粗粒度的信息
   + 为了保留主要特征（类似Canny检测中的非极大值抑制），在纹理表示中，判断图像某处的表示也是根据响应值最大的点

![池化](https://img-blog.csdnimg.cn/20210404150630124.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3BlYWNlZmFpcnk=,size_16,color_FFFFFF,t_70)

![conv_poll](https://img-blog.csdn.net/20130918153655515?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2lsZW5jZTEyMTQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

### 上采样和下采样

1. Upsampling（放大图像）
   + 目的
     + 使图像符合显示区域大小（大），对图像的缩放操作不能带来更多关于该图像的信息，图像质量不可避免受到影响
   + 大多采用内插值的方式，有最近邻插值，双线性插值，均值插值，中值插值等方法
2. Subsampled（缩小图像）
   + 目的
     + 使图像符合显示区域大小（小）
     + 生成缩略图
   + 对于一幅图像I尺寸为`M * N`，对其进行`s`倍下采样，即得到`(M/s) * (N/s)`尺寸的得分辨率图像，s是M和N的公约数，如果考虑的是矩阵形式的图像，就是把原始图像`s*s`窗口内的图像变成一个像素，这个像素点的值就是窗口内所有像素的均值（或极值）

### 梯度弥散和梯度爆炸

[梯度消失与梯度爆炸问题产生的原因和解决方法](https://blog.csdn.net/u013069552/article/details/108307491?utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1.no_search_link&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1.no_search_link)

#### `梯度弥散（梯度消失)`

通常神经网络所用的激活函数是sigmoid函数，sigmod函数容易引起梯度弥散。这个函数能将负无穷到正无穷的数映射到0和1之间，并且对这个函数**求导的结果**是f′(x)=f(x)(1−f(x))f′(x)=**f(x)(1−f(x))**表示两个0到1之间的数相乘，得到的结果就会变得很小了。神经网络的反向传播是逐层对函数偏导相乘，因此当神经网络层数非常深的时候，最后一层产生的偏差就因为乘了很多的小于1的数而越来越小，最终就会变为0，从而导致**层数比较浅的权重没有更新**，这就是梯度消失。

#### `梯度爆炸`

初始化权值过大，前面层会比后面层变化得更快，就会导致权值越来越大，梯度爆炸的现象就发生了。

> 反向传播基于的是链式求导法则。如果导数小于1，那么随着层数的增多，梯度的更新量会以指数形式衰减，结果就是越靠近输出层的网络层参数更新比较正常，而靠近输入层的网络层参数可能基本就不更新。这就是梯度消失。而如果导数值大于1，那么由于链式法则的连乘，梯度更新量是会成指数级增长的。这就是梯度爆炸。

