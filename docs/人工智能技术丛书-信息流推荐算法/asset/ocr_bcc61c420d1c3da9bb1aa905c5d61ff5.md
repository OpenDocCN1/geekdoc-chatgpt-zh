$$e_u(A)=f(e_A,e_1,e_2,\cdots,e_H)=\sum_{j=1}^H g(e_A,e_j)e_j=\sum_{j=1}^H w_j e_j \tag{5-28}$$

式中， $e_1, e_2, \dots, e_H$ 是用户行为列表中的Embedding；H 为用户行为列表的长度；$e_A$ 是候选广告的Embedding；g 是一个前馈神经网络，如图5-13右上方所示，输入的两个Embedding会做进一步处理（即计算Embedding之间的外积），之后拼接起来，送入全连接层。DIN的这种加权求和的方式和神经网络语言翻译中的注意力机制类似，不同的是其他方法中通常满足\(\sum w_j=1\)，DIN中移除了这个限制，去掉了Softmax归一化的操作来保留兴趣表示的强度。

除了Embedding部分的创新，DIN还有一些训练技术上的贡献。

# (1) Data Adaptive Activation Function

PReLU是一种常用的激活函数：

$$f(s)=\begin{cases} s, & \text{如果 } s>0 \\ as, & \text{如果 } s \leqslant 0 \end{cases}=p(s)s+(1-p(s))as \tag{5-29}$$

式中， s 为激活函数的输入；p (s) = I ( s > 0 ) 可指示函数来处理正、负两种情况。PReLU函数在0点有一个突变，这种方式可能不适合不同的数据分布。考虑到这个问题，DIN中提出了适配数据分布的激活函数Dice：

$$f(s)=p(s)s+(1-p(s))as, \quad p(s)=\frac{1}{1+e^{-\frac{s-E(s)}{\sqrt{Var(s)+\epsilon}}}} \tag{5-30}$$

两个函数的曲线如图5-14所示。训练阶段为当前Batch的均值和方差，预测阶段为已训练数据的滑动平均，这一点类似于Batch Normalization。 \(\epsilon\) 为一个极小的常数。当均值和方差等于0时，Dice退化为PReLU。相比PReLU，Dice可以看作更具一般性的激活函数。

# (2) Mini-batch Aware Regularization

DIN的实验中观察到了明显的过拟合现象，如图5-15所示。观察图中的绿线可以发现，第一轮迭代后训练损失有明显的下降，但是测试损失有明显的上升。为了解决这个问题，可以采用常用 Drop Out和正则化方式来防止过拟合。如果采用标准的L2正则，则需要在每一个Batch更新的时候对所有的参数做权重衰减，这对于动辄亿级参数的大规模稀疏推荐场景来说是非常耗时的，在实践中不可行。比较可行的做法是只对当前Batch出现的参数做L2正则。这里会牵扯到一个问题，参数的覆盖率或者频度是有高低之分的，如果对当前Batch出现的参数按照标准L2正则的梯度衰减，那么对于更新频率更高的参数是不利的，为此DIN中给出了Batch内做近似L2正则的方式。

模型的参数集中在Embedding部分，定义 $W \in R^{D \times K}$ 来表示Embedding字典， D 为Embedding的维度， K 为字典的大小。将参数 W 上的L2正则按照样本展开：