# Wide & Deep [2016]:

出自论文：Wide & Deep Learning for Recommendation Systems 。

Wide x& Deep是 2016年 Google提出的用于Google Play app推荐业务的一种算法。其核心思想是通过结合**Wide线性模型的记忆性（memorization）**和**Deep深度模型的泛化性（generalization）**来对用户行为信息进行学习建模。

如果推荐的内容都是精准内容（记忆性），用户兴趣收敛，无新鲜感，不利于长久的用户留存；推荐内容过于泛化，用户的精准兴趣无法得到满足，用户流失风险很大。所以，要结合记忆性和泛化性才好。

![img](https://pic1.zhimg.com/v2-8e4235b518f9ea6589e60824df41be1c_b.png)

![img](https://pic1.zhimg.com/v2-0bcf5db620faf08798e863bf4fa259dc_b.png)



- Wide侧：线性模型通常输入二进制的one-hot稀疏表示特征进行训练。比如特征“user_installed_app=netflix”为1，表示用户已安装netflix。交叉特征AND（user_installed_app=netflix，impresion_app=Pandora）表示既安装了netflix app同时又浏览过Pandora的用户特征为1，否则为0。wide模型可以通过利用**交叉特征**高效的实现**记忆能力**，达到准确推荐的目的。wide模型通过加入一些宽泛类特征实现一定的泛化能力。但是受限与训练数据，wide模型无法实现训练数据中未曾出现过的泛化。
- Deep侧：像FM和DNN这种Embedding类的模型，可以通过学习到的**稠密向量**实现模型的**泛化能力**，包括可以实现对未见过的内容进行泛化推荐。

总体的公式：

![img](https://pic1.zhimg.com/v2-e17517e10c5c92f67aac9c26889f6d58_b.png)

其中，$x$是原始的稀疏特征，$\phi(x)$是交叉特征。