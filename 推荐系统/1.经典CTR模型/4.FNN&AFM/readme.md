# FNN

基于**FM预训练**获取特征embedding表示，然后输入MLP来进行CTR的预估。使用DNN来对FM显式表达的二阶交叉特征进行再交叉，从而产生更高阶的特征组合（隐式），加强模型对数据模式的学习能力。

![img](https://img-blog.csdnimg.cn/20210129145234630.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTMzMjAwOQ==,size_16,color_FFFFFF,t_70)

使用FM预训练的embedding，在FNN中之后并没有通过fine tuning来调整参数，所以FNN不是end-to-end训练的，而是一种“贪心”的训练方法。FNN有如下几个问题：

1）FM中进行特征组合，使用的是隐向量**点积**。将FM得到的隐向量移植到DNN中接入全连接层，全连接本质是将输入向量的所有元素进行加权**求和**，且不会对特征Field进行区分（是bit-wise的），也就是说FNN中高阶特征组合使用的是全部隐向量元素相加的方式。说到底，在理解特征组合的层面上FNN与FM是不同的(FM: 点积，FNN：加权平均)，而这一点也正是PNN对其进行改进的动力。

2）在神经网络的调参过程中，参数学习率是很重要的。况且FNN中底层参数是通过FM预训练而来，如果在进行反向传播更新参数的时候学习率过大，很容易将FM得到的信息抹去。个人理解，FNN至少应该采用Layer-wise learning rate(**不同层的学习率不同**)，底层的学习率小一点，上层可以稍微大一点，在保留FM的二阶交叉信息的同时，在DNN上层进行更高阶的组合。



# AFM (Attentional Factorization Machines)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190205180743725.png?,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2J1d2VpMDIzOQ==,size_16,color_FFFFFF,t_70)



这里的**Pair-wise Interaction layer**算的是哈达玛积，每个交叉特征都是一个向量。为了解决FM不能区分交叉特征重要性的问题，论文中在二阶交互特征计算完成后加入了注意力网络（Attention Net），使用多层感知器（MLP）得到注意力权重。$a_{ij}$表示第i个特征和第j个特征的组合特征对于结果的重要程度。

**Attention-based Pooling**: 就是将Pair-wise Interaction Layer中所有的特征用attention net中算出来的特征重要性$a_{ij}$进行加权求和。得到$\sum_{ij} a_{ij}(v_i \odot v_j)x_ix_j$. 这是一个k维向量。

**prediction score:** 使用一阶特征和二阶特征进行得分的预测：

![img](https://img-blog.csdnimg.cn/20210207152537842.png)



