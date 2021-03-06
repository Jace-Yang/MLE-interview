# 无监督学习 - 聚类

无监督学习主要分为三类：**聚类**、**降维** (Dimensionality reduction)、**异常检测** (Outlier detection)。之前已经介绍了PCA降维，这篇文章主要来介绍聚类。聚类方法分为硬聚类（一个样本只属于一类）和软聚类（一个样本可以以不同概率属于多类，类似softmax），本文中我们只考虑硬聚类。

一个好的聚类算法，应当具有**大的类内相似度、小的类间相似度**。

### 0x01. K-Means聚类 – partitioning method

K-Means是知名度最高的一种聚类算法，代码非常容易理解和实现。

```python
[Algorithm 1] K-means:

随机初始化k个聚类质心点
迭代 t 步(或迭代到质心位置不再发生太大变化)
2.1 计算每个数据点到质心的距离来进行分类，它跟哪个聚类的质心更近，它就被分类到该聚类。
2.2 再重新计算每一聚类中所有向量的平均值，并确定出新的质心。
```

K-Means在本质上是一种EM算法(Expectation Maximization), 有关EM算法以后会详细介绍。

**K-means的优点：**

-  当数据的分布有明显的簇的划分时，K-means表现很好。(works well when the clusters are compact clouds that are well-separated from each other)

**K-means的缺点：** 

-  **k值**的选取不太确定。
-  一开始质心点的选取是**随机**的，不同的初始质心选择，会导致不同的结果。所以，K-means 一定能收敛，但不一定是最优解
-  对噪声和outlier非常敏感(因为直接以距离来度量)
- 只适用于**凸形状**聚类，不能用于非凸形状（凸形状是指当选择形状内任意两点，这两点的连线上所有点，也都在形状内）

K-Medians是与K-Means相关的另一种聚类算法，不同之处在于它使用簇的中值向量来重新计算质心点。和K-means不同，K-中值算法的聚类中心点一定是一个真实存在的点。该方法**对异常值不敏感**（因为使用中值），但在较大数据集上运行时速度会慢很多，因为每次计算中值向量，我们都要重新排序。

**【面试题：怎么确定好的初值？】**

- 第一种方法：随机选择，选择距离尽可能远的K个点
  - 随机选一个点作为一个类簇的初始中心点
  - 然后选取距离这个点**最远**的点作为第二个点
  - 之后选与前两个点距离和最远的点作为第三个点，以此类推。
- 第二种方法：用层次聚类预处理
  - 使用层次聚类的方法，得到若干个"Canopy".可以认为每个Canopy都是一个Cluster。但是与KMeans等硬划分算法不同，每个点有可能属于多个Canopy。我们可以选择每个Canopy的中心点作为KMeans的初始K个类簇中心点。

**【面试题：怎么确定好的K值？】**

常见的一种方法是[elbow method](https://www.zhihu.com/search?q=elbow+method&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A667861076%7D)。x轴为K值，y轴为WSS（within cluster sum of squares）也就是**各个点到聚类簇中心的距离的平方的和**。

先假设K=1，然后我们找到这一范围的中心点，用⭐️表示，蓝色的线表示黑点到⭐️的距离，WSS就是所有的蓝色的线的平方加起来：

![img](https://pica.zhimg.com/80/v2-cb029eaec2a3c77b34f28ee7e5f2aad2_1440w.jpg?source=1940ef5c)

很明显这样的WSS是很大的，于是我们可以进一步再看K=2的时候：

![img](https://pic3.zhimg.com/80/v2-fdd906ff2f595aa80de74601ee05d8ea_1440w.jpg?source=1940ef5c)

以此类推，算出K = 1,2,3...

![img](https://pic3.zhimg.com/80/v2-916dd08810a3e3ca23ecab29378cbfdc_1440w.jpg?source=1940ef5c)

看到那个拐弯处最厉害的地方，就像我们的肘关节一样，那个点对应的值，就是比较适合的K值。这是因为在这个”肘子值“后面WSS指标下降的就不再那么明显了。



### 0x02. 层次聚类 -- hierarchical method

层次聚类实际上可以被分为两类：自上而下和自下而上。其中自下而上算法（Bottom-up algorithms）首先会将每个数据点视为单个聚类，然后连续合并（或聚合）成对的聚类，直到所有聚类合并成包含所有数据点的单个聚类。它也因此会被称为hierarchical agglomerative clustering。该算法的聚类可以被表示为一幅树状图，树根是最后收纳所有数据点的单个聚类，而树叶则是只包含一个样本的聚类。

```python
[Algorithm 2] 凝聚型层次聚类:

把每个数据点看作是一个聚类。
迭代，直到只有一个聚类簇：
2.1 找到距离最小的两个聚类簇c_i,c_j
2.2 将c_i,c_j合并为一个簇
```

三种衡量聚类簇相似性的标准：

- single linkage: 两个聚类簇中最相似的两个点的相似度
- complete linkage: 两个聚类簇中最不相似的两个点的相似度
- average linkage: 两个聚类簇中平均两两相似度

![img](https://pic1.zhimg.com/v2-3feeb1dc67417b5d71a9839093f38268_b.png)

层次聚类不要求我们指定聚类的数量，由于这是一个构建树的过程，我们甚至可以选择那种聚类看起来更合适。它具有 ![O(n^3)](https://www.zhihu.com/equation?tex=O(n%5E3))的时间复杂度。

### 0x03. DBSCAN – density-based method

#### 1. 简介

DBSCAN（Density-Based Spatial Clustering of Applications with Noise，具**有噪声的基于密度的聚类方**法）是一种基于密度的空间聚类算法。 该算法将具有足够密度的区域划分为簇，并在具有噪声的空间数据库中发现任意形状的簇。

它将簇定义为密度相连的点的最大集合。通过将紧密相连的样本划为一类，这样就得到了一个聚类类别。通过将所有各组紧密相连的样本划为各个不同的类别，则我们就得到了最终的所有聚类类别结果。

我们知道k-means聚类算法只能处理凸(球形)的簇，也就是一个聚成实心的团 (cloud), 这是因为算法本身计算平均距离的局限。但往往现实中还会有各种形状，比如下面这张图，传统的聚类算法显然无法解决。

![img](https://pic2.zhimg.com/v2-5021447bcd66976bee25c16d3d78c679_b.png)



#### 2. 重要概念

假设样本集 ![D = ( x_1 , x_2 , . . . , x_m )](https://www.zhihu.com/equation?tex=D%20%3D%20(%20x_1%20%2C%20x_2%20%2C%20.%20.%20.%20%2C%20x_m%20)), 则DBSCAN具体的密度描述定义如下:

(1) ![\epsilon](https://www.zhihu.com/equation?tex=%5Cepsilon)**邻域：**对于一个样本 ![x_j](https://www.zhihu.com/equation?tex=x_j), 其![\epsilon](https://www.zhihu.com/equation?tex=%5Cepsilon) 邻域包含样本集D中所有与 ![x_j](https://www.zhihu.com/equation?tex=x_j) 距离不大于 ![\epsilon](https://www.zhihu.com/equation?tex=%5Cepsilon) 的样本点集合，这个集合的元素个数记作 ![|N\epsilon(x_j)|](https://www.zhihu.com/equation?tex=%7CN%5Cepsilon(x_j)%7C) .

(2) **核心对象**：对于任一样本 ![x_j](https://www.zhihu.com/equation?tex=x_j) 如果其ϵ-邻域对应的ϵ邻域至少包含MinPts个样本，则 ![x_j](https://www.zhihu.com/equation?tex=x_j) 是核心对象。

(3) **直接密度可达**：如果 ![x_i](https://www.zhihu.com/equation?tex=x_i) 位于 ![x_j](https://www.zhihu.com/equation?tex=x_j) 的ϵ-邻域中，且 ![x_j](https://www.zhihu.com/equation?tex=x_j) 是核心对象，则称 ![x_i](https://www.zhihu.com/equation?tex=x_i) 由 ![x_j](https://www.zhihu.com/equation?tex=x_j) 直接密度可达。注意反之不一定成立，即此时不能说 ![x_j](https://www.zhihu.com/equation?tex=x_j) 由 ![x_i](https://www.zhihu.com/equation?tex=x_i) 密度直达, 除非 ![x_i](https://www.zhihu.com/equation?tex=x_i) 也是核心对象。

(4) **密度可达：**对于 ![x_i](https://www.zhihu.com/equation?tex=x_i) 和 ![x_j](https://www.zhihu.com/equation?tex=x_j) ,如果存在样本序列 ![\{p_1,p_2,...,p_T\}](https://www.zhihu.com/equation?tex=%5C%7Bp_1%2Cp_2%2C...%2Cp_T%5C%7D), 满足![p_1 = x_i ,p_T = x_j](https://www.zhihu.com/equation?tex=p_1%20%3D%20x_i%20%2Cp_T%20%3D%20x_j), 其中对于所有的 ![p_{t+1}](https://www.zhihu.com/equation?tex=p_%7Bt%2B1%7D) ,它都可以由 $p_{t}$ 直接密度可达。也就是说，所有的 $$\{p_1,p_2,...,p_{T-1}\}$$ 都是核心对象。称 ![x_j](https://www.zhihu.com/equation?tex=x_j) 由 ![x_i](https://www.zhihu.com/equation?tex=x_i) 密度可达。也就是说，密度可达就是直接密度可达的传递。注意密度可达也不满足对称性，这个可以由直接密度可达的不对称性得出。

(5) **密度相连：**对于 ![x_i](https://www.zhihu.com/equation?tex=x_i) 和 ![x_j](https://www.zhihu.com/equation?tex=x_j) ,如果存在核心对象 ![x_k](https://www.zhihu.com/equation?tex=x_k) ,使 ![x_i](https://www.zhihu.com/equation?tex=x_i) 和 ![x_j](https://www.zhihu.com/equation?tex=x_j) 均由 ![x_k](https://www.zhihu.com/equation?tex=x_k) 密度可达，则称![x_i](https://www.zhihu.com/equation?tex=x_i) 和 ![x_j](https://www.zhihu.com/equation?tex=x_j)密度相连。注意密度相连关系是满足对称性的。



从下图可以很容易看出理解上述定义，图中MinPts=5，红色的点都是核心对象，因为其ϵ-邻域至少有5个样本。黑色的样本是非核心对象。所有核心对象**直接密度可达**的样本在以红色核心对象为中心的超球体内，如果不在超球体内，则不能直接密度可达。图中用绿色箭头连起来的核心对象组成了**密度可达**的样本序列。在这些密度可达的样本序列的ϵ-邻域内所有的样本相互都是**密度相连**的。

所以，下图中共有两个密度相连的簇（左边和右边），簇中的每个样本都是密度相连的。

![img](https://pic1.zhimg.com/v2-bf304fca90a313772765ec4abd9d5adc_b.png)

定义：

- 边界点(border point)在ϵ邻域内的点小于MinPts个，但它落在一个核心点的ϵ邻域内。

- 噪声点(noise point)不在任何一个核心对象在周围. 所以，DBSCAN是可以解决噪声点的。

![img](https://pic4.zhimg.com/v2-a0b49d6fbf78a1d08c9ff25005d66b8f_b.png)

​                                       右图中，绿色的点是核心对象，蓝色的点是边界点，红色的点是噪声。

#### 3. DBSCAN算法

```
[Algorithm 3]: DBSCAN

随机选一点p
根据参数Minpts和ϵ找到p点密度可达的一些点
2.1 如果p是核心对象，则一个聚类簇已经形成了；
2.2 如果p不是核心对象，没有点是从p开始密度可达的，于是跳过它访问下一个对象。
持续迭代，直到所有的点都被访问过。
```

时间复杂度 ![ O(N^2)](https://www.zhihu.com/equation?tex=%20O(N%5E2))  , 使用kd树优化可以达到 ![O(NlogN)](https://www.zhihu.com/equation?tex=O(NlogN)) .

#### 4. 小结

我们什么时候需要用DBSCAN来聚类呢？一般来说，如果数据集是**稠密**的，并且数据集是**非凸**的，那么用DBSCAN会比K-Means聚类效果好很多。如果数据集不是稠密的，则不推荐用DBSCAN来聚类。

DBSCAN的优点有：

- 可以对**任意形状的稠密数据集**进行聚类。相对的，K-Means之类的聚类算法一般只适用于凸数据集。
- 可以在聚类的同时发现异常点，对数据集中的异常点不敏感。("With **noise**")
- 聚类结果不受初始值的影响。相对的，K-Means之类的聚类算法**初始值**对聚类结果有很大影响。

DBSCAN的缺点有：

- 如果样本集的密度不均匀、聚类间距差相差很大时，聚类质量较差，这时用DBSCAN聚类一般不适合。
- 如果样本集较大时，聚类收敛时间较长，此时可以对搜索最近邻时建立的KD树或者球树进行规模限制来改进。
- **调参**相对于传统的K-Means之类的聚类算法稍复杂，主要需要对距离阈值ϵ，邻域样本数阈值MinPts联合调参，不同的参数组合对最后的聚类效果有很大影响。



### 4. 混合高斯模型 GMM


高斯混合模型（GMM）是多个高斯分布函数的线性组合，**理论上可以拟合出任意类型的分布**，通常用于解决同一集合下的数据包含多个不同的分布的情况。对于GMM，我们假设数据点满足**不同参数下的高斯分布**。我们用$\mu$和$\sigma$来描述聚类的形状。以二维分布为例，标准差的存在允许聚类的形状可以是任何种类的**椭圆形**。因此这个算法的思想是：如果数据点符合某个高斯分布，那它就会被归类为那个聚类。

为了找到每个聚类的高斯参数，我们要用到EM算法。下图是高斯混合模型的聚类过程。

初始化

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210219152855893.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTMzMjAwOQ==,size_16,color_FFFFFF,t_70)

迭代数次之后：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210219153208381.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTMzMjAwOQ==,size_16,color_FFFFFF,t_70)

E步：随机初始化每个聚类的高斯分布参数。根据每个聚类的高斯分布，**计算数据点属于特定聚类的概率**。如果数据点越接近高斯质心，那它属于该聚类的概率就越高。
M步：为每个聚类的高斯分布计算一组新的参数，使聚类内数据点的概率最大化。我们用数据点位置的加权和来计算这些新参数，其中权重就是数据点属于聚类的概率。
迭代步骤2和步骤3，直至收敛。

GMM有两个关键优势。首先它比K-Means更灵活，由于标准差的引入，**最后聚类的形状不再局限于圆形，它还可以是大小形状不一的椭圆形**（K-means由于计算的是欧式距离，所以更适合圆形形状聚类；而GMM由于有高斯分布假设，所以更适合椭圆形聚类）。其次，权重的引入为同一点属于多个聚类（软聚类）找到了解决方案。我们就可以计算一个点属于X聚类的百分比是多少，属于Y聚类的百分比是多少。
