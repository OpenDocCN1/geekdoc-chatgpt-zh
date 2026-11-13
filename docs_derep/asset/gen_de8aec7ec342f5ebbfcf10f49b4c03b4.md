# 7.3 基于List-wise建模的多样性算法详解

本节将系统梳理几种代表性的List-wise多样性建模技术。大体上，这些方法可归为两大流派：一类是相对轻量的**上下文感知重排模型**，典型代表有DLCM（Deep Listwise Context Model）与PRM（Personalized Re-Ranking Model）；另一类则融合了**序列生成与评估机制**的重排框架，如Seq2Slate、GRN（Generative Rerank Network）及PRS（Permutation Recommender System）。

---

## 7.3.1 深度列表上下文模型

DLCM的核心定位是“精细修正”：针对初始排序列表，利用深度神经网络捕捉局部排序上下文，并将其融入学习排序（LTR）范式中。其整体思路是：先用循环神经网络（RNN）从初始序列中提炼局部上下文特征，再依据这些特征对候选项重新打分排序。

### 1. 问题建模与动机

DLCM源于搜索场景的实战需求。给定查询 $q$，提取文档 $d$ 的特征向量 $\boldsymbol{x}(q, d)$。传统LTR假设存在全局最优打分函数 $f$，其损失函数形式为：

$$L = \sum_{q \in Q} l( \{ y_{(q, d)}, f(\boldsymbol{x}_{(q, d)}) \mid d \in D \} ) \qquad (7-24)$$

其中 $Q$ 为查询集，$D$ 为候选文档集。然而，全局函数 $f$ 往往忽略了不同查询下的**局部列表特性**。DLCM引入局部校准模块 $I(R_q, X_q)$，其中 $R_q$ 为按 $f$ 排序后的初始列表，$X_q$ 为对应特征集。引入上下文后，损失函数演变为：

$$L = \sum_{q \in Q} l( \{ y_{(q, d)}, \phi(\boldsymbol{x}_{(q, d)}, I(R_q, X_q)) \mid d \in D \} ) \qquad (7-25)$$

$\phi$ 为新打分函数，输入融合了原始特征与上下文模型输出。

局部上下文模型 $I$ 需满足两大刚需：
1.  **原生支持标量处理**：避免繁琐特征工程，显著提升工程落地效率。
2.  **深度利用位置先验**：初始排名顺序本身蕴含强相关性信号，忽之则大损排序质量。

DLCM的模型架构如下图所示。

### 图 DLCM模型架构示意图

### 2. 特征预处理与表示增强

DLCM沿用全局排序阶段的特征体系，但在送入上下文模型前增加了非线性变换层：

$$\boldsymbol{z}_i^{(0)}=\boldsymbol{x}(q, d_i) \qquad (7-26)$$
$$\boldsymbol{z}_i^{(l)} = \text{elu}( \boldsymbol{W}_z^{(l-1)} \boldsymbol{z}_i^{(l-1)} + \boldsymbol{b}_z^{(l-1)} ), \quad l=1,2 \qquad (7-27)$$

ELU激活函数定义为：
$$\text{elu}(x)=\begin{cases} x & x>0 \\ \alpha( e^x - 1 ) & x<0 \end{cases} \qquad (7-28)$$

经过两层全连接网络得到 $\boldsymbol{z}_i^{(2)}$，再与原始输入 $\boldsymbol{x}(q, d_i)$ 拼接，形成增强特征 $\boldsymbol{x}'(q, d_i)$。此操作类似特征工程中的根号变换，有助于调节特征尺度，利于后续表示学习。后文统一用 $\boldsymbol{x}(q, d_i)$ 代指增强后特征。

### 3. 基于GRU的局部上下文编码

给定全局排序返回的前 $n$ 个文档及特征集 $\boldsymbol{X}_q^s$，DLCM采用**单向GRU**提取局部序列编码。RNN擅长序列建模，包含输入、输出、隐状态三要素。每步输入一个元素，更新隐状态并输出特征 $\boldsymbol{o}_i$，最终隐状态 $\boldsymbol{s}_n$ 蕴含全序列信息。

选择GRU的考量：
- 天然适配序列数据，对位置/顺序敏感，完美契合“利用位置信息”的需求。
- 处理标量极其高效。
- **单向优于双向**：实验表明，双向GRU虽在NLP任务中常胜，但在DLCM中无增益；若仅用反向（违背全局排序序），反而引入大幅负向效应。单向GRU按全局得分**从低到高**喂入，使高分文档对隐状态影响更大，隐式保证了局部模型与全局模型的一致性。

### 4. 局部打分函数设计

模型右侧展示了如何基于GRU输出为每个文档打分：

$$\phi(\boldsymbol{o}_{n+1-i}, \boldsymbol{s}_n) = \boldsymbol{V}_{\phi}( \boldsymbol{o}_{n+1-i} \cdot \tanh(\boldsymbol{W}_{\phi} \cdot \boldsymbol{s}_n + \boldsymbol{b}_{\phi}) ) \qquad (7-29)$$

其中 $\boldsymbol{W}_{\phi} \in \mathbb{R}^{a \times k \times a}$，$\boldsymbol{b}_{\phi} \in \mathbb{R}^{a \times k}$，$\boldsymbol{V}_{\phi} \in \mathbb{R}^{k}$。该结构类似**注意力机制**：以全局上下文 $\boldsymbol{s}_n$ 为Query，各位置输出 $\boldsymbol{o}$ 为Key/Value，计算相关性权重。作者尝试过更深的MLP等复杂结构，但无显著增益。

### 5. 三种损失函数的对比与创新

DLCM实验了ListMLE、SoftRank，并创新提出了**Attention Rank**。

#### (1) ListMLE：序列化选择视角
将排序视为无放回顺序抽样。序列 $\pi_m^n$ 中选中文档 $d_i$ 的概率：
$$P(d_i | \pi_m^n) = \frac{e^{S_i}}{\sum_{j=m}^{n} e^{S_j}} \qquad (7-30)$$
$S_i, S_j$ 为局部打分。观察到真实排序序列 $R_q^n$ 的联合概率：
$$P(R_q^n | S) = \prod_{i=1}^{n} P(d_i | \pi_m^n) \qquad (7-31)$$
优化目标转化为极大似然，即最小化负对数似然：
$$l = -\log(P(R_q^* | S)) \qquad (7-32)$$

#### (2) SoftRank：直接优化评价指标
区别于ListMLE，SoftRank可直接优化NDCG等指标。假设文档 $d_i$ 真实得分服从 $\mathcal{N}(S_i, \sigma^2)$，$\sigma^2$ 为共享超参。两文档得分差的概率：
$$\pi_{ij} = Pr(S'_i - S'_j > 0) = \int_0^\infty \mathcal{N}(S | S_i - S_j, 2\sigma^2) dS \qquad (7-33)$$
基于插入排序思路，利用 $\pi_{ij}$ 迭代更新文档位置分布：
$$P_j^{(i)}(r) = P_j^{(i-1)}(r-1) \pi_{ij} + P_j^{(i-1)}(r) (1 - \pi_{ij}) \qquad (7-34)$$
最终损失函数模拟NDCG形式：
$$l = 1 - \frac{1}{z} \sum_{j=1}^n (2^{y_{(q, d_i)}} - 1) \sum_{r=1}^n \eta(r) P_j(r) \qquad (7-35)$$
$\eta(r)$ 为位置衰减权重，越靠前权重越大。

#### (3) Attention Rank：相对重要性建模
[PRE0]
受注意力机制启发，DLCM设计了**Attention Rank**。核心假设：文档信息相互独立，列表总信息量为单文档信息量之和；文档相关性等价于其信息量。最优策略即给相关文档分配高注意力、无关文档低注意力。

定义真实标签 $y(q, d_i)$ 在序列上的注意力分配策略：
$$a_i^\gamma = \frac{\psi(y(q, d_i))}{\sum_{d_k \in R_q^\eta} \psi(y(q, d_k))} \qquad (7-36)$$
其中映射函数 $\psi$ 为：
$$\psi(x) = \begin{cases} e^x & x \geqslant 0 \\ 0 & x < 0 \end{cases} \qquad (7-37)$$

对预测打分同理计算获得 $a_i^s$，损失函数定义为两套注意力分布的**交叉熵**：
$$L(R_q^n) = - \sum_{d_i \in R_q^n} (a_i^s \log(a_i^s) + (1-a_i^s) \log(1-a_i^s)) \qquad (7-38)$$

**Attention Rank的独特价值**：
- **建模相对序而非绝对值**：不直接回归真实标签，而是学习序列内文档的**相对重要性排序**。例如，全是高相关(3/4级)的列表中，3级文档可能获得比全低相关(1/2级)列表中的1级文档更高的注意力权重。
- **极致的训练效率**：在DLCM实验中，Attention Rank训练速度是ListMLE与SoftRank的 **2~20倍**，兼顾简洁与高效。

### 6. 实验结论与实战洞察

DLCM相关实验揭示了极具指导意义的四大结论：

1.  **重排收益依赖全局模型强弱**：全局排序越弱（如简单逻辑回归），重排正向增益概率越大；全局模型若已很强（如LambdaMART），仅DLCM能稳健获益。
2.  **损失函数选型**：SoftRank与Attention Rank效果领先；综合效果与效率，**Attention Rank为首选**。
3.  **超参鲁棒性与特征工程**：
    - Attention Rank对初始列表长度不敏感，增益稳定。
    - “原始特征拼接全连接抽象表示”再喂入GRU，优于直接喂原始特征；但抽象表示维度**不宜过小亦不过大**，需适度。
4.  **擅长“头部精排”**：DLCM显著降低了初始序列中的逆序对比例，**最相关文档（Perfect级）的前置效果最明显**)，如下图所示。

[PRE1]

图 DLCM在不同相关等级文档上的优化效果对比（详见彩插）