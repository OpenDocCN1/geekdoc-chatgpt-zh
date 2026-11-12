
根据公式还可以推导出：

\(\det(\boldsymbol{L}_{Y_g \cup |j|}) = \det(\boldsymbol{V}\boldsymbol{V}^T) \cdot d_i^2 = \det(\boldsymbol{L}_{Y_g}) \cdot d_i^2 \quad (7-14)\)

则求解目标可以等价于：

\(j = \arg \max_{i \in Z \ Y_g} \log(d_i^2) \quad (7-15)\)

如果求解出公式中的 j，则 Cholesky 分解表示为如下形式：

\(\boldsymbol{L}_{Y_g \cup |j|} = \begin{bmatrix} \boldsymbol{V} & \boldsymbol{0} \\ \boldsymbol{c}_j & d_j \end{bmatrix} \begin{bmatrix} \boldsymbol{V} & \boldsymbol{0} \\ \boldsymbol{c}_j & d_j \end{bmatrix}^T \quad (7-16)\)

式中，c<sub>j</sub> 和 d<sub>j</sub> 在求解出 j 以前已经得到。对于任意一个物品 i，在被选择以前，它对应的行向量 c<sub>i</sub> 和标量 d<sub>i</sub> 都是可以增量求解的。定义 c'<sub>i</sub> 和 d'<sub>i</sub> 分别表示新的结果，则根据式 (7-12) 和式 (7-16) 可以得到：

\(\begin{bmatrix} \boldsymbol{V} & \boldsymbol{0} \\ \boldsymbol{c}_j & d_j \end{bmatrix}^T = \boldsymbol{L}_{Y_g \cup |i|, i} = \begin{bmatrix} \boldsymbol{L}_{Y_g, i} \\ L_{ji} \end{bmatrix} \quad (7-17)\)

对上式求解可以得到：

\(\boldsymbol{c}'_i = \left[ \boldsymbol{c}_i \frac{(L_{ji} - \langle \boldsymbol{c}_j, \boldsymbol{c}_i \rangle)}{d_j} \right] = [\boldsymbol{c}_i \ e_i] \quad (7-18)\)

\(d_i^{'2} = L_{ii} - \| \boldsymbol{c}'_i \|_2^2 = L_{ii} - \| \boldsymbol{c}_i \|_2^2 - e_i^2 = d_i^2 - e_i^2 \quad (7-19)\)

至此完成了一个DPP优化后的推荐重排，完整的计算过程如算法7-1所示。

- 算法 7-1 快速贪心最大后验概率推理
- 0: 输入: Kernel L, 终止条件
- 1: 初始化: c<sub>i</sub> = [], d<sub>i</sub><sup>2</sup> = L<sub>ii</sub>, j = arg max <sub>i \in Z</sub> log(d<sub>i</sub><sup>2</sup>), Y<sub>g</sub> = {j}
- 2: while 未满足终止条件下
- 3: for i \in Z \ Y<sub>g</sub> do
- 4: e<sub>i</sub> = (L<sub>yi</sub> - <c<sub>j</sub>, c<sub>i</sub>>) / d<sub>j</sub>
- 5: c<sub>i</sub> = [c<sub>i</sub> e<sub>i</sub>], d<sub>i</sub><sup>2</sup> = d<sub>i</sub><sup>2</sup> - e<sub>i</sub><sup>2</sup>
- 6: j = arg max <sub>i \in Z \ Y<sub>g</sub></sub> log (d<sub>i</sub><sup>2</sup>), Y<sub>g</sub> = Y<sub>g</sub> U {j}
- 7: 返回 Y<sub>g</sub>

可以注意到，式 (7-8) 中相关性和多样性的权衡是没有超参可以调节的，为此将其修改为如下形式：

log\(\mathcal{P}(R_u) \propto \theta \cdot \sum_{i \in R_u} \boldsymbol{r}_{u,i} + (1-\theta) \cdot \log \det(\boldsymbol{S}_{R_u}) \qquad (7-20)\)

式中， \(\theta \in [0,1]\) 。修改后对应的矩阵如下：

\(\boldsymbol{L}' = \text{Diag}( \exp( \alpha \boldsymbol{r}_u ) ) \cdot \boldsymbol{S} \cdot \text{Diag}( \exp( \alpha \boldsymbol{r}_u ) ) \qquad (7-21)\)

式中， \(\alpha = \theta / \left( 2 ( 1 - \theta ) \right)\) ，相应的边际收益更新如下：

\(\theta \cdot \boldsymbol{r}_{u,i} + (1-\theta) \cdot \left( \log \det( \boldsymbol{S}_{R_u \cup \{i\}} ) - \log \det( \boldsymbol{S}_{R_u} ) \right) \qquad (7-22)\)

关于DPP算法还有一点需要注意的是 \(\mathrm{S}_{ij} \in [0,1]\) ，其中，0表示物品间完全没有相似性，1表示非常相似。但是 \(\mathrm{S}_{ij}\) 如果为 \(\mathrm{f}_i\) 和 \(\mathrm{f}_j\) 的内积 \(\langle \mathrm{f}_i, \mathrm{f}_j \rangle\) ，则是有可能引入负值的。为了保证非负性，可以采取如下的线性映射进行转换：

\(\mathrm{S}_{ij} = \frac{1 + \langle \mathrm{f}_i, \mathrm{f}_j \rangle}{2} = \left\langle \frac{1}{\sqrt{2}} \begin{bmatrix} 1 \\ \mathrm{f}_i \end{bmatrix}, \frac{1}{\sqrt{2}} \begin{bmatrix} 1 \\ \mathrm{f}_j \end{bmatrix} \right\rangle \in [0,1] \qquad (7-23)\)

## 7.3 多样性算法之list-wise建模

本节内容主要介绍几种典型的list-wise多样性建模方法，它们大致可以分为两类。

- 1) 相对简单的上下文信息重排模型：DLCM [4] （Deep Listwise Context Model）和PRM [5] （Personalized Re-Ranking Model）。
- 2) 序列生成与评估结合的重排模型：Seq2Slate [6] 、GRN [7] （Generative Rerank Network）和PRS [8] （Permutation Recommender System）。

### 7.3.1 DLCM

DLCM着眼于对初始排序列表做进一步的修正，利用深度神经网络将局部排序上下文信息引入排序学习（Learning To Rank, LTR）框架中。它的整体思想是利用循环神经网络从初始序列中学习局部排序的上下文信息，并基于局部信息对初始序列中的结果再次打分排序。

#### 1.问题定义

DLCM基于搜索场景的业务问题构建问题描述。给定一个具体的查询 q，提取查询 q 下文档 d 的特征向量 x(q, d)。传统的LTR算法假设存在一个最优的全局排序函数 f，该函数可以准确地预测查询 q 和文档 d 的相关性得分，其损失函数的定义形式如下：

L = \sum_{q \in Q} l( \{ y_{(q, d)}, f(x_{(q, d)}) \mid d \in D \} ) 
(7-24)

式中，Q 是所有查询的集合； D 为所有候选文档的集合。然而这样训练得到的全局排序函数 f 通常没有考虑不同查询的局部特点。DLCM在全局排序之后增加了局部的校准修正，新建上下文模型 I （ Rq， Xq ）来获取查询 q 的局部排序上下文信息，其中，查询 q 的初始排序结果 Rq = { d sorted by f ( x(q, d) ) }，即根据 f ( x(q, d) ) 对 d 进行排序， Xq = { x(q, d) | d \in Rq } 。引入局部上下文信息后，相应的损失函数定义形式如下：

L = \sum_{q \in Q} l( \{ y_{(q, d)}, \phi(x_{(q, d)}, I(R_q, X_q)) \mid d \in D \} ) 
(7-25)

式中， \(\phi\) 为打分函数，其输入包含特征向量 x(q, d) 以及局部上下文模型的输出 I （ Rq， Xq ）。

为了更充分地利用局部排序上下文，模型 I 需要满足两个需求。首先，模型 I 需要能够直接处理标量。现有的很多LTR系统在这方面都是不足的，如果可以省略数据加工而直接处理标量，那么对于效率的提升是显著的。其次，上下文模型 I 需要充分利用初始排序列表中的位置信息。初始列表中的顺序是结果相关性的一个重要参考，忽略这部分信息会严重影响整个排序的效果。DLCM算法的模型结构如图7-2所示。

### 图7-2 DLCM算法的模型结构

#### 2.输入表表示

DLCM中使用的特征和全局排序阶段的特征基本一致，同样包括查询和文档的相关特征，不过，这些特征在送入局部上下文模型之前做了进一步的处理：

$z_i^{(0)}=x(q, d_i)$ 
(7-26)

$z_i^{(l)} = elu( W_z^{(l-1)} z_i^{(l-1)} + b_z^{(l-1)} ), \quad l=1,2$ 
(7-27)

式中，  和  为参数；elu是一个非线性激活函数：

$elu(x)=\begin{cases} x & \text{如果 } x>0 \\ a( \exp(x) - 1 ) & \text{如果 } x<0 \end{cases}$ 
(7-28)

DLCM首先对原始输入做了非线性转换，连接了两层全连接层，得到 。接下来将  和原始输入 x ( q, d_i ) 拼接起来，得到新的特征向量 x' ( q, d_i ) 。这种做法类似于特征工程中对实数开根号的操作，可以调整输入特征的尺度，以便更有利于后续的特征表示学习。下文为了简便不再区分 x' ( q, d_i ) 和 x ( q, d_i )，均用 x ( q, d_i ) 来表示。

#### 3.局部上下文排序模型

给定全局排序函数 f 返回的前 n 个文档及其对应的特征向量

$X_q^s = \{ x_{(q, d)} \mid d \in R_q \}$ , DLCM中利用循环神经网络GRU提取

局部编码信息。循环神经网络是处理序列数据的典型深度模型，一般包含输入序列、输出序列和隐状态3个部分。循环神经网络中，每次输入序列中的一个数据，更新隐状态并输出一个特征表示 o_i ，最终的隐状态 s_n 可以认为包含了整个序列的编码信息。之所以选择循环神经网络，是因为除了能够处理序列数据之外，它也正好可以满足前面问题的描述部分对于局部上下文模型的要求。处理标量方面不必多说，循环神经网络天然地对位置或者顺序信息敏感。由于数据是一个可以看一个输入网络的，因此当前输入相比之前的输入对于网络状态的影响更大。而且从图7-2中可以看到，文档是按照全局排序从低到高送入循环神经网络的，这个顺序使得全局排序高的文档对隐状态的影响更大，隐含了局部上下文模型和全局排序模型的一致性。

DLCM中的循环神经网络是单向的，然而在很多其他自然语言任务中，双向的循环神经网络往往效果更好。DLCM中也尝试了双向的方式，但并没有取得效果上的实际收益，而且如果仅使用与全局排序不一致的反向方式，那么还会带来非常大的负向效果。因此，基于过去的实践经验，使用单向神经网络的DLCM更符合预期。

#### 4.局部打分函数设计

从图7-2的右侧可以看到，DLCM得到GRU的输出之后，需要计算序列中每一个文档的得分。局部打分函数定义如下：

\(\phi(o_{n+1-i}, s_n) = V_{\phi}(o_{n+1-i} \cdot \tanh(W_{\phi} \cdot s_n + b_{\phi}))\) (7-29)

式中， \(W_{\phi} \in \mathbb{R}^{a \times k \times a}\)； \(b_{\phi} \in \mathbb{R}^{a \times k}\)； \(V_{\phi} \in \mathbb{R}^{k}\)。局部打分函数的形式有点类似于注意力机制。DLCM的作者还尝试了一些更复杂的建模方式，比如多层的全连接等，不过都没有取得明显的收益。

#### 5.损失函数设计

DLCM中尝试了两种现有的损失函数ListMLE和SoftRank，并提出了一种新的损失函数Attention Rank。

##### (1) ListMLE

ListMLE将排序看作一个序列化的选择问题，在序列 $\pi_m^n$ 中选择文档 d_i 的概率定义如下：

\(P(d_i | \pi_m^n) = \frac{e^{S_i}}{\sum_{j=m}^{n} e^{S_j}}\) (7-30)

式中， S_i 和 S_j 分别表示文档 d_i 和 d_j 的局部排序得分。如果从初始序列开始逐次无放回地选择，则在给定局部得分的情况下观察到序列 $R_q^n$ 的概率如下：

\(P(R_q^n | S) = \prod_{i=1}^{n} P(d_i | \pi_m^n)\) (7-31)

接下来计算损失的时候就很直接了，优化目标就是最大化观察到真实标签序列的概率，也就是最小化下面的负对数：

\(l = -\log(P(R_q^* | S))\) (7-32)

##### (2) SoftRank

和ListMLE相比， SoftRank可以直接优化评价指标，比如NDCG等。令 $S_i$ 和 $S_j$ 表示文档 $d_i$ 和 $d_j$ 的局部排序得分， SoftRank假定文档 $d_i$ 的真实打分服从正态分布 $N(S_i, \sigma^2)$, 其中，方差 $\sigma^2$ 为文档间共享的超参。基于这个假设，文档 $d_i$ 得分大于文档 $d_j$ 得分的概率如下：

\(\pi_{ij} = Pr(S'_i - S'_j > 0) = \int_0^\infty N(S | S_i - S_j, 2\sigma^2) dS\) (7-33)

这里假定2个文档的正态分布是独立的，因此其线性组合仍然符合正态分布。得到 $\pi_{ij}$ 后，可以按照文档插入的思路生成结果序列，比如说当前的序列中包含文档 $d_j$，接下来需要插入文档 $d_i$，则可以根据 $\pi_{ij}$ 调整文档 $d_j$ 的位置：

\(P_j^{(i)}(r) = P_j^{(i-1)}(r-1) \pi_{ij} + P_j^{(i-1)}(r) (1 - \pi_{ij})\) (7-34)

式中，\(P_j^{(i)}(r)\) 表示插入文档 $d_i$ 后，文档 $d_j$ 排在第 $r$ 个位置的概率，\(P_j^{(i-1)}(r-1)\) 和 \(P_j^{(i-1)}(r)\) 的意义类似，因此可以得到文档排在位置 $r$ 的概率 $P_j(r)$，接下来可以基于 $P_j(r)$ 定义损失函数：

\(l = 1 - \frac{1}{z} \sum_{j=1}^n (2^{y_{(q, d_i)}} - 1) \sum_{r=1}^n \eta(r) P_j(r)\) (7-35)

式中， \(\eta(r)\) 为位置权重，位置越靠前权重越大。该损失函数形式和NDCG类似，也同样要求相关性越高，预测得分越高。

##### (3) Attention Rank

受到注意力机制的启发，DLCM中设计了一种新颖的损失函数Attention Rank，将排序列表的评估看作注意力的分配过程。假设序列中文档所包含的信息是相互独立的，那么排序后的列表所包含的信息可以看作单一文档的信息累计。如果进一步假设文档的相关性得分等同它的信息量，那么最大化信息量的策略就是给相关的结果大的注意力权重，给不相关的结果小的注意力权重。Attention Rank的思想为保持预测打分和真实标签在分配策略上的一致性。令 \(y(q, d_i)\) 表示查询 $q$ 下文档 $d_i$ 的相关性标签，它在序列上的分配策略如下：

\(a_i^\gamma = \frac{\psi(y(q, d_i))}{\sum_{d_k \in R_q^\eta} \psi(y(q, d_k))}\) (7-36)

式中， \(\psi\) 定义如下：

\[
\psi(x) = \begin{cases} e^x & x \geqslant 0 \\ 0 & x < 0 \end{cases} \quad (7-37)
\]

![](img/453a7413e6e27ae2b6c61bb72a4047b8_279_0.png)

对于局部打分函数的预测得分，采用类似的计算方式得到，，损失函数定义为2个注意力得分的交叉熵：

\[
L(R_q^n) = - \sum_{d_i \in R_q^n} (a_i^s \log(a_i^s) + (1-a_i^s) \log(1-a_i^s)) \quad (7-38)
\]

Attention Rank不直接预测真实的相关性标签，而是专注在学习序列文档间的相对重要性。假设相关性等级分1、2、3、4级，两个查询的结果序列中，一个仅包含3级和4级的文档，另外一个序列仅包含1级和2级的文档。相比于第二个序列中的1级文档，Attention Rank可能给第一个序列的3级文档分配更高的注意力权重，它建模的是相对值而不是绝对值。Attention Rank的主要优点是简单高效，在基于DLCM的实验中，Attention Rank的训练速度是ListMLE和SoftRank两种损失函数的2～20倍。

#### 6.实验效果分析

从DLCM的相关实验中可以发现如下结论。

- 1) 重排算法不一定能够带来正向的收益。如果全局的排序算法比较弱，那么重排算法带来正向收益的可能性会比较大。全局排序在采用LambdaMART的情况下，只有DLCM的算法取得了正向的效果。
- 2) 3个损失函数中，SoftRank和Attention Rank的效果最好。综合效果和效率两方面，Attention Rank更好一些。
- 3) 参数设置方面，Attention Rank对于初始序列的长度不敏感，对于效果的提升是稳定的；原始数据拼接上全连接的抽象表示再送入GRU中学习，对于效果是有提升的，抽象表示的维度不宜太小和太大。
- 4) DLCM更擅长将初始序列中的最相关结果排在前面。如图7-3所示，查询返回的文档相关性等级包包括完美（perfect）、优秀（excellent）、好（good）和一般（fair）。DLCM优化后的文档序列中，逆序对的比例得到了一定的减少，其中减少最多的是完美（perfect）等级的文档。
