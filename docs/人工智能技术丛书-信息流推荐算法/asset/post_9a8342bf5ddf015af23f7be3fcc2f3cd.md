
## 参考文献

- [1] CARBONELL J, GOLDSTEIN J. The use of MMR, diversity-based reranking for reordering documents and producing summaries [C] //Proceedings of the 21st Annual International ACM SIGIR conference on Research and Development in Information Retrieval. New York: Association for Computing Machinery, 1998: 335-336.

- [2] ABDOOL M, HALDAR M, RAMANATHAN P, et al. Managing diversity in Airbnb search [C] //Proceedings of the 26th ACM SIGKDD International Conference on Knowledge Discovery & Data Mining. New York: Association for Computing Machinery, 2020: 2952-2960.

- [3] WILHELM M, RAMANATHAN A, BONOMO A, et al. Practical diversified recommendations on YouTube with determinantal point processes [C] //Proceedings of the 27th ACM International Conference on Information and Knowledge Management. New York: Association for Computing Machinery, 2018: 2165-2173.

- [4] AI Q, BI K, GUO J, et al. Learning a deep listwise context model for ranking refinement [C] //The 41st International ACM SIGIR Conference on Research & Development in Information Retrieval. New York: Association for Computing Machinery, 2018: 135-144.

- [5] PEI C, ZHANG Y, ZHANG YF, et al. Personalized re-ranking for recommendation [C] //Proceedings of the 13th ACM Conference on Recommender Systems. New York: Association for Computing Machinery, 2019: 3-11.

- [6] BELLO I, KULKARNI S, JAIN S, et al. Seq2slate: re-ranking and slate optimization with RNNs [EB/OL] .(2018-10-04) [2022-09-16] .https://arxiv.org/abs/1810.02019.

- [7] FENG Y, HU B, GONG Y, et al. GRN: generative rerank network for context-wise recommendation [EB/OL] .(2021-04-02) [2022-06-04] .https://arxiv.org/abs/2104.00860.

- [8] FENG Y, GONG Y, SUN F, et al. Revisit recommender system in the permutation prospective [EB/OL] .(2021-02-24) [2022-07-01] .https://arxiv.org/abs/2102.12057.

- [9] GONG Y, JIANG Z, FENG Y, et al. EdgeRec: recommender system on edge in Mobile Taobao [C] //Proceedings of the 29th ACM International Conference on Information & Knowledge Management. New York: Association for Computing Machinery, 2020: 2477-2484.

- [10] YAO J, WANG F, JIA K, et al. Device-cloud collaborative learning for recommendation [C] //Proceedings of the 27th ACM SIGKDD Conference on Knowledge Discovery & Data Mining. New York: Association for Computing Machinery, 2021: 3865-3874.

- [11] BAHDANAU D, CHO K, BENGIO Y. Neural machine translation by jointly learning to align and translate [EB/OL] .(2014-09-01) [2022-03-29] .https://arxiv.org/abs/1409.0473.

- [12] HOULSBY N, GIURGIU A, JASTRZEBSKI S, et al. Parameter-efficient transfer learning for NLP [C] //International Conference on Machine Learning. PMLR, 2019: 2790-2799.

## Chapter 8 第8章 推荐建模中的数据预处理和模型后处理

本章重点介绍推荐建模中的数据预处理和模型后处理。数据预处理是指在建模之前，我们需要对模型的“原材料”进行一定的数据加工以符合模型输入的要求，比如构建评分矩阵时需要对多维度的反馈行为进行降噪、归一化、融合等操作，召回和排序模型中的特征工程也是重要且必需的建模前置工作。模型后处理是指通常排序模型的输出打分只具有偏序关系的意义，但某些场景下我们需要让排序分具有实际的物理意义，以便参与后续的业务运算，因此需要对模型输出进行合理的校准。本章将对上述内容逐一展开介绍。

### 8.1 评分矩阵构建

我们基于用户反馈行为构建评分矩阵。用户的反馈行为一般可以分为显式反馈和隐式反馈两类，显式反馈是指用户主动提供的对物品和内容喜好程度的打分，而隐式反馈是指用户在内容消费过程中留下的行为“足迹”。在信息流的推荐系统中，严格意义上的显式反馈非常稀缺，大量反馈都是用户对物品的交互行为（如，电商场景的点击、收藏、加购物车、购买，短视频场景的播放、点赞、快切、转发等），也就是隐式反馈。隐式反馈数据不能直接当作主动评分使用，因为它往往具有如下特点。

- 1）隐式反馈的维度多、量纲不同、数据完整性差异大。比如，点赞、收藏是布尔型变量，点击、购买是离散型的计数累加值，播放行为既可以表达为计数累加值的“次数”，也可以表达为连续型的“时长”；点击、收藏、购买，这3类代表用户对物品的不同交互“深度”的数据完整性往往在3个不同的量级上。

- 2）隐式反馈天然带有大量的噪声。如，单排和沉浸式的视频信息流里播放都是自动的，所以被动观看的行为就不能代表用户的主观偏好，还需要从视频播放的完成度上推断；用户主动点击的行为也往往带有一定的随意性或者误点等操作，需要结合页面的停留时长等后续相关行为为进行分析判别。

- 3）隐式反馈中的指标值大小往往不等同于主动评分中的偏好程度（Preference），而只是反映了偏好的置信度（Confidence）。如，电商场景中，牙刷的购买频次远高于手机，仅仅因为牙刷是易耗品，复购周期较短，而手机是相对耐用的物品，复购周期较长，购买频次不能用来判断用户对两者的偏好度高低。

因此，基于大量隐式反馈的行为日志生成用户物品的评分矩阵时，需要做如下多个方面的数据预处理工作。

### 1. 隐式反馈数据预处理

### (1) 指标噪声

推荐系统的隐式反馈数据中不可避免地存在数据的噪声，这些噪声的来源通常有3个方面：

- 1）因用户随意性或者误操作行为引起的数据异常，比如用户打开某个视频后一直循环播放导致播放时长过高。

- 2）前端埋点日志收集过程中的数据丢失引起的异常，比如，埋点往往基于用户主动触发的事件来确定行为日志的类型，但停留时长相关的日志收集在于没有唯一且明确的事件来表示停留的“结束”，因此容易引起埋点数据的丢失。

- 3）外部的机器人爬虫模拟内部用户行为产生的异常数据。

如果对隐式反馈数据不加修正地使用显然会引起模型的偏差，因此我们需要从数据中发现异常并合理地处理异常。

发现异常数据的方法通常有如下几种：

- 1）基于业务经验的异常发现。通常我们基于对业务的理解，对大部分的数据和指标都有一个合理范围的认知，比如，一个人观看视频的时长一天不可能超过24h，APP用户合理的年龄分布应该在8～80岁等，如果出现大幅超过认知范围的数据，则可以判定为异常值。

- 2）基于技术手段的异常发现。我们结合风控规则、反欺诈模型可以识别机器人的爬虫行为、电商的刷单行为等，这部分数据属于非正常用户产生的非正常反馈行为，也不是推荐算法的研究对象。

- 3）基于数据分布的异常发现。异常数据通常是那些占总样本量比重很小，但与正常值偏离很大的特征值，通常我们称之为“离群点”。我们可以基于聚类算法检测数据的离群点，比如DBSCAN和孤立森林（Isolation Forests）都是比较常见的异常值检测方法。

- 4）基于统计规则的异常发现。对于服从正态分布的指标值，我们用σ代表标准差，μ代表均值，可以得到如图8-1所示的概率密度函数。从概率密度函数中，可以得到指标值的概率分布遵循3σ原则：该指标有99.74%的概率分布在（μ-3σ，μ+3σ）之间。也就是说，超过这个范围的数值概率分布不到0.3%，属于极个别的小概率事件。基于这个原则，我们可以把超过（μ-3σ，μ+3σ）范围的数据归为异常数据。

![](img/453a7413e6e27ae2b6c61bb72a4047b8_320_0.png)

图8-1 正态分布数据遵循3σ原则

需要注意的是，异常数据并不完全等同于脏数据，比如，电商场景中个别用户远超正常范围的大量购买行为，可能是出于某个目的而进行的交易，确实是真实的行为，不属于脏数据，甚至对风控模型来说是一类优质的样本，但这样的用户行为已经跳出了推荐算法需要个性化建模的用户范畴，所以属于推荐建模中的异常数据和噪声，需要特别处理。处理异常数据的技术手段通常有三种：

- 1）剔除法。直接将异常数据对应的样本剔除，一般这种处理方法对应的是上述第二种原因产生的异常数据，因为爬虫、刷单行为等产生的样本本身就属于非正常样本，所以直接剔除不会对样本的真实分布造成影响，反而有利于模型的学习。

- 2）盖帽法。顾名思义，将指标的数值限定在一个固定的范围内，通常对于符合正态分布的指标，我们以（μ-3σ，μ+3σ）作为限定范围，对小于μ-3σ的值赋予μ-3σ，超过μ+3σ的值赋予μ+3σ。对不符合正态分布的指标，我们也可以采用百分位数作为限定范围，比如以第1个百分位数为下限，第99个百分位数为上限，进行盖帽法处理。

- 3）缺失值处理法。将异常值直接作为缺失值处理，参照下一节特征工程中的缺失值处理方法，用相关均值、中位数、众数等数值或者模型预测方法进行填充处理。

在将多维的交互行为指标融合为统一的评分前，我们必须解决一个问题，即指标的“内部归一”问题。上文提到隐式反馈的行为指标值只能代表偏好的置信度，不代表偏好的强度，主要是因为不同物品存在属性差异的情况，导致相同指标之间不具有可比性，比如，有电视剧 A（共40集）、电视剧 B（共20集）、电影 C，用户全部都看完结后生成的观看日志里， A 有40条， B 有20条， C 只有1条，这不代表用户对 A 的偏好度是 C 的40倍，是 B 的2倍，如果不解决指标内部的归一问题，不加处理地直接使用这份行为数据做协同过滤，最后就会出现电视剧内容的推荐度要远远大于电影的问题。一般情况下，我们可以对相同指标因为业务量级不同引起的差异进行指标转换统一量级，比如，上述视频APP中的行为，将播放行为次数归一化为播放完成度，看一集 A 代表完成1/40，看一集 B 代表完成1/20，看完 C 代表完成1。

### (3) 指标的时间衰减

历史和最近的行为对用户当前的兴趣偏好的反映程度是不一样的，这很容易理解，所以通常需要对反馈行为引入时间衰减机制。简单的做法是基于行为发生的时间点距今的天数对行为频次乘以指数系数：

$e^{-\alpha \cdot \text{days}}$ (8-1)

式中， \(\alpha\) 是冷却因子，指标的值越大变化就越明显；days是行为发生的时间点距今的天数。

### 2. 多维隐式反馈的融合

完成了指标内部归一后，就可以进行多维交互行为指标的融合，但不同类型的反馈行为同样存在“量纲”的差异，比如电商场景里，浏览行为的数量级天然大于收藏和加购物车，收藏和加购物车行为的数量级天然大于购买。对不同维度的指标进行加权融合通常有几种方法：基于业务专家视角的主观赋权法、AHP、基于统计方法的熵权法等。

### (1) 主观赋权法

主观赋权法是基于业务专家的经验和领域知识，直接对每个指标赋予固定的权重。比如，在电商场景中，我们通常将单次行为按不同类型直接给定一个固定的权重分：浏览记为1、收藏记为2、加购物车记为3、购买记为5；在沉浸式体验的正向播放行为中，我们以播放时长3s作为阈值，3s及以上记为正向播放为1，否则记为0。所以在上一步指标内部归一的基础上，我们可以用主观赋权法直接对指标进行融合，完成评分矩阵的构建。

### (2) AHP

层次分析法（Analytic Hierarchy Process, AHP）与主观赋权法同样需要专家知识，但在AHP中，业务专家只决定了指标之间的重要性比值，并不直接指定权重的绝对数值，具体的权重值由AHP推导出。仍以电商场景为例，我们定义用户对物品的交互行为为包括：浏览、收藏、加购物车、下单、支付5种类型，用户在这些行为上的指标分别为指标1、指标2、指标3、指标4、指标5，AHP输出的权重分别为 \(w_1\)、\(w_2\)、\(w_3\)、\(w_4\)、\(w_5\)。业务专家对5个指标的权重定义了4个约束条件。

- 1）“收藏”和“加购物车”的重要性是“浏览”的3倍。

- 2）“加购物车”的重要性是“收藏”的1.6倍。

- 3）“下单”和“支付”是“加购物车”和“收藏”的2倍，并且是“浏览”的5倍。

- 4）“支付”是“下单”的1.2倍。

基于上述约束条件的AHP的推导过程如下。

1) 首先基于业务专家定义的重要性比值，得到判断矩阵：

\( A = \begin{bmatrix} a_{11} & \cdots & a_{1j} & \cdots & a_{1n} \\ \vdots & \ddots & \vdots & & \vdots \\ a_{i1} & \cdots & a_{ij} & \cdots & a_{in} \\ \vdots & & \vdots & \ddots & \vdots \\ a_{n1} & \cdots & a_{nj} & \cdots & a_{nn} \end{bmatrix} = \)

| | | | | |
|---|---|---|---|---|
| 1 | 0.5 | 0.5 | 0.2 | 0.2 |
| 3 | 1 | 0.63 | 0.5 | 0.5 |
| 3 | 1.6 | 1 | 0.5 | 0.5 |
| 5 | 2 | 2 | 1 | 0.83 |
| 5 | 2 | 2 | 1.2 | 1 |

=1, \( a_{ij} \cdot a_{ji}=1 \), n 为指标个数。判断矩阵中的元素 \( a_{ij} \) 代表业务专家确定的指标 i 与指标 j 的重要性比值。

### 2）对判断矩阵 A 列向量上归一化，计算得到归一化矩阵 B =

![](img/453a7413e6e27ae2b6c61bb72a4047b8_323_0.png)

### 3）对矩阵 B 在行维度上求和，得到列向量

![](img/453a7413e6e27ae2b6c61bb72a4047b8_323_1.png)

P = \begin{bmatrix} \frac{v_1}{\sum_{i=1}^n v_i} \\ \frac{v_2}{\sum_{i=1}^n v_i} \\ \vdots \\ \frac{v_n}{\sum_{i=1}^n v_i} \end{bmatrix} = \begin{bmatrix} 0.07 \\ 0.15 \\ 0.18 \\ 0.29 \\ 0.32 \end{bmatrix}

4) 对 V 做归一化得到

5) 最终得到权重向量 W = P^T = [0.07 0.15 0.18 0.29 0.32] 。

6) 计算评分矩阵 R，其中， R 的第 i 行第 s 列的值 R [ i ] [ s ] 代表第 i 个用户对第 s 个物品的评分，假设第 i 个用户对第 s 个物品的原始交互行为为 [redacted]，其中， [redacted] 为 k 维向量，代表 k 个类型的反馈行为（在本例中 k=5），则最终的评分值等于用户对物品的各种反馈行为值的带权重求和。

### (3) 熵权法

熵权法顾名思义就是利用指标自身的“熵值”来决定它的权重大小。熵是热力力学中的一个物理概念，是体系混乱度或无序度的度量，熵越大表示系统越混乱、携带的信息越少，熵越小表示系统越有序、携带的信息越多。熵权法的计算步骤如下。

1) 数据归一化。隐式反馈的数据在使用熵权法计算之前还需要先进行数据归一化处理，一般情况，可以是基于最大最小值的归一化，也可以是z-score归一化。设有 n 个用户，第 i 个用户的 k 个隐式反馈指标为 \( X_i=\{ x_{i1}, x_{i2}, ..., x_{ij}, ..., x_{ik} \} \)，归一化后的指标为 \( Y_i=\{ y_{i1}, y_{i2}, ..., y_{ij}, ..., y_{ik} \} \)，其中 \( y_{ij} = \frac{x_{ij} - \min(X_j)}{\max(X_j) - \min(X_j)} \)，j=1, 2, ..., k。

2) 计算各指标信息熵，指标 j 的信息熵为 \( e_j = - \frac{1}{\ln m} \sum_{i=1}^{m} p_{ij} \ln p_{ij} \)，其中 \( p_{ij} = \frac{y_{ij}}{\sum_{i=1}^{m} y_{ij}} \)。

3) 确定各指标权重，指标 j 的权重为 \( w_j = \frac{1 - e_j}{\sum_{j=1}^{k} (1 - e_j)} \)。

4) 计算评分矩阵 R，其中，R 的第 i 行第 s 列的值 R [i] [s] 代表第 i 个用户对第 s 个物品的评分，假设第 i 个用户对第 s 个物品的原始交互行为为 \( X_s^i\)，其中，\( X_s^i \) 为 k 维向量，代表 k 个类型的反馈行为，则 \( R[i][s] = X_i^T W\)，即最终的评分值等于用户对物品的各种反馈行为值的带权求和。

上述每一种方法的原理都简洁明确、通俗易懂，但是否有效则需要根据业务场景的实际情况分析，并没有一个通用的标准答案，在我们过去的经验里，往往基于最简单的业务专家视角的主观赋权方法也能有比较好的效果。

通过上述步骤的数据预处理后，我们得到了完整的评分矩阵，为下一步的模型训练提供了结构化的样本。
