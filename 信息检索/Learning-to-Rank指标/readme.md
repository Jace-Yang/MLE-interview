### 1. Threshold-based metrics

**(1) Accuracy, Precision, Recall, F1-score**

​                                                   $Accuracy = \frac{TP+TN}{TP+TN+FP+FN} $

​                                                         $Precision = \frac{TP}{TP+FP}$   //模型给出的阳性，有多少是真阳？

​                                                       $Recall = \frac{TP}{TP+FN}$  //阳性有多少被找了出来？

​                                                    ![F1 = \frac{2}{\frac{1}{Precision}+\frac{1}{Recall}}](https://www.zhihu.com/equation?tex=F1%20%3D%20%5Cfrac%7B2%7D%7B%5Cfrac%7B1%7D%7BPrecision%7D%2B%5Cfrac%7B1%7D%7BRecall%7D%7D) //二者的调和平均数

TP, TN, FP, FN 构成了混淆矩阵。

Precision 和 Recall是”此消彼长“的关系，所以需要F1-score来综合二者。事实上，Precision-Recall构成了P-R曲线：

![img](https://camo.githubusercontent.com/8089392d2b45c88b4a9490831c315d258757802f6a2c44a1474214bcbd5ccd31/687474703a2f2f7777312e73696e61696d672e636e2f6c617267652f303036674f6569536c793167343067796f673065386a33306176303972676e392e6a7067)



**（2）ROC曲线和AUC**

TPR(True Positive Rate): ![\frac{TP}{TP+FN}](https://www.zhihu.com/equation?tex=%5Cfrac%7BTP%7D%7BTP%2BFN%7D), 就是recall

FPR(False Positive Rate): ![\frac{FP}{FP+TN}](https://www.zhihu.com/equation?tex=%5Cfrac%7BFP%7D%7BFP%2BTN%7D) 

![img](https://pic4.zhimg.com/v2-df646644ea2108996c36052f3b893833_b.png)



AUC是工业界评判CTR预估的指标。AUC（Area Under Curve）被定义为ROC曲线下的面积。又由于ROC曲线一般都处于y=x这条直线的上方，所以AUC的取值范围在0.5和1之间。使用AUC值作为评价标准是因为很多时候ROC曲线并不能清晰的说明哪个分类器的效果更好，而作为一个数值，对应AUC更大的分类器效果更好。

**【手撕AUC】**

首先要明白AUC的物理含义不仅是ROC曲线下的面积，AUC还有另外一个**物理含义**就是：给定正样本Ｍ个，负样本Ｎ个，以及他们的预测概率，那么AUC的含义就是所有**穷举所有的正负样本对**，如果正样本的预测概率大于负样本的预测概率，那么就+1；如果如果正样本的预测概率等于负样本的预测概率，那么就＋0.5,　如果正样本的预测概率小于负样本的预测概率，那么就+0；最后把统计处理的个数除以Ｍ×Ｎ就得到AUC.

```python
def AUC(label,pre):
    pos = []  ## 正样本index
    neg = []  ## 负样本index
    for i in range(len(label)):
        if(label[i] == 1):
            pos.append(i)
        else:
            neg.append(i)
    cnt = 0
    for i in pos:
        for j in neg:
            if(pre[i]>pre[j]):
                cnt += 1
            if(pre[i] == pre[j]):
                cnt += 0.5
            else:
                cnt += 0
    return cnt / (len(pos)*len(neg))
```



**（3）多分类问题**

**1) Macro F1**： 宏平均

Macro 算法在计算 Precision 与 Recall 时是先分别计算每个类别的Precision 与 Recall， 然后再进行平均。

​                                                     $$Macro_{F1-score}=\frac{1}{N}\sum_{i=0}^N F1-score_i$$

其中，N为类别数。

Macro F1 本质上是所有类别的统计指标的算术平均值来求得的，这样单纯的平均忽略了**样本之间分布可能存在极大不平衡的情况**。



**2) Micro F1** ：微平均

Micro 算法在计算 Precision 与 Recall 时会将所有类直接放到一起来计算。
$$
\text{Precision}_{micro} = \frac{\sum_{i=1}^L TP}{\sum_{i=1}^L TP + \sum_{i=1}^L FP} \\
\text{Recall}{micro} = \frac{\sum_{i=1}^L TP}{\sum_{i=1}^L TP + \sum_{i=1}^L FN} \\

\text{Micro F1} = \frac{2 \cdot \text{Precision}_{micro} \cdot \text{Recall}
_{micro}}{\text{Precision}_{micro} + \text{Recall}_{micro}}
$$


**Macro vs Micro** 

Macro 相对 Micro 而言，**小类别起到的作用更大**。考虑到实际的环境中，这种指标明显是有问题的，因为小类别起到的作用太大。 而对于 Micro 来说，其考虑到了这种样本不均衡的问题， 因此在这种情况下相对较佳。

总的来说， 如果你的类别比较均衡，则随便； 如果你认为大样本的类别应该占据更重要的位置， 使用Micro； 如果你认为小样本也应该占据重要的位置，则使用 Macro。

为了解决 Macro 无法衡量样本均衡问题，一个很好的方法是求加权的 Macro， 因此 Weighted F1 出现了。

**3). Weight F1**

Weighted 算法算术 Macro 算法的改良版，是为了解决Macro中没有考虑样本不均衡的原因， 在计算 Precision与Recall 时候，各个类别的 Precision 与 Recall要乘以**该类在总样本中的占比**来求和。



### 2. Ranking-based Metrics：加入位置信息

**(1) Hit Rate**

在top-K推荐中，HR是一种常用的衡量召回率的指标，计算公式为：



![img](https://pic2.zhimg.com/v2-4fddd0ead39481a35d57a285b9a11cc5_b.gif)

例如，三个用户购买的商品个数分别是10,12,8, 模型得到的top-10推荐列表中，分别有6个，5个，4个在测试集中，那么此时HR@10的值是 (6+5+4)/(10+12+8) = 0.5。

**（2）MRR(Mean Reciprocal Rank, 平均倒数排名)**

把标准答案在被评价系统给出结果中的排序取倒数作为它的准确度，再对所有的问题取平均。例如有3个query如下图所示：

![img](https://pic2.zhimg.com/v2-78a3751dd3b382ccc9d426d6b76e3451_b.png)

(黑体为返回结果中最匹配的一项)

这个系统的MRR值为：(1/3 + 1/2 + 1)/3 = 11/18=0.61。

**（3）MAP（Mean Average Precision）**

MAP（Mean Average Precision）是信息检索/推荐领域用以衡量搜索/推荐引擎的排序性能的评价指标。例如对于【命中，命中，未命中，未命中，未命中】和【未命中，未命中，未命中，命中，命中】这两个top-5的推荐列表，虽然他们的precision都是2/5，但是显然第一个推荐列表的性能要高于第二个推荐列表，因为其在第1、2位就已命中。

MAP可以由它的三个部分来理解：P, AP, MAP

**i)  P: precision**

正确率只是考虑了返回结果中相关文档的个数，没有考虑文档之间的序。对一个搜索引擎或推荐系统而言返回的结果必然是有序的，而且越相关的文档排的越靠前越好，于是有了AP的概念。

**ii). AP: average precision**

对一个有序的列表，计算AP的时候要先求出每个位置上的precision，然后对所有的位置的precision再做个average。如果该位置的文档是不相关的则该位置 precision=0.

对于推荐列表【命中，命中，未命中，未命中，未命中】(假设用户实际购买了3种商品，而我们的推荐系统在前5个只给出了2个):

| ID   | Correctness | Score |
| ---- | ----------- | ----- |
| 1    | 命中，1     | 1     |
| 2    | 命中，1     | 1     |
| 3    | 未命中，0   | 2/3   |
| 4    | 未命中，0   | 1/2   |
| 5    | 未命中，0   | 2/5   |

Score行代表到目前为止的准确率。

![img](https://pic4.zhimg.com/v2-c81598828fddf46ffcfcac4569d25ae3_b.jpeg)

**iii) MAP**

MAP(Mean Average Precision)，即为所有query的AP取均值.



**（4）Normalized Discounted Cummulative Gain (NDCG)**

归一化折损累计增益(NDCG) 通常用来评价搜索/推荐算法的好坏。

**i. 累计增益（CG）**

CG，cumulative gain，是DCG的前身，只考虑到了相关性的关联程度，没有考虑到位置的因素。它是一个搜素结果相关性分数的总和。指定位置p上的CG为：

![img](https://pic1.zhimg.com/v2-b2711663b0e08e961f8b2cbea3904614_b.png)

![rel_i](https://www.zhihu.com/equation?tex=rel_i)代表i这个位置上的相关度。

假设搜索一个query，最理想的结果是: doc1、doc2、 doc3。而出现的结果是 doc3、doc1、doc2的话，CG的值是没有变化的，因此需要下面的DCG。

**ii. 折损累计增益（DCG)**

DCG， Discounted 的CG，就是在每一个CG的结果上除以一个折损值，为什么要这么做呢？目的就是为了让排名越靠前的结果越能影响最后的结果。假设排序越往后，价值越低。到第i个位置的时候，它的价值是1/log2(i+1)，所以：

![img](https://pic3.zhimg.com/v2-35d9692a35fa7a5fb251bce5e8a3881a_b.png)

**iii) 归一化折损累计增益（NDCG）**

NDCG:  Normalized 的DCG，由于不同query搜索之后返回的数量是不一致的，而DCG是一个累加的值，没法针对两个不同的搜索结果进行比较（因为返回结果越多，DCG会越大），因此需要归一化处理，这里是除以IDCG。IDCG为理想情况下最大的DCG值。

举例

假设搜索回来的6个结果，其相关性分数分别是 3、2、3、0、1、2

![img](https://pic4.zhimg.com/v2-12ab0f3e05d2af6115d8140ba5edaf43_b.png)

所以 DCG  = 3+1.26+1.5+0+0.38+0.71 = 6.86

为了求NDCG，首先需要计算IDCG：假如我们实际召回了8个物品，除了上面的6个，还有两个结果，假设第7个相关性为3，第8个相关性为0。那么在理想情况下的相关性分数排序应该是：3、3、3、2、2、1、0、0。计算IDCG@6:

![img](https://pic1.zhimg.com/v2-e730919304f9220c4a76688d92c321f8_b.png)

所以IDCG = 3+1.89+1.5+0.86+0.77+0.35 = 8.37

因此，最终 NDCG@6 = DCG/IDCG = 6.86/8.37 = 81.96%