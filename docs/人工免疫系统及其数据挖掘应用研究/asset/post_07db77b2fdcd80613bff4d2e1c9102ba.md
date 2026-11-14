
- [18] HUNT J E, FELLOWS A. Introducing an Immune Response into a CBR system for Data Mining[C]. In BCS ESG'96 Conference and published as Research and Development in Expert System XIII, 1996.

- [19] HUNT J E, COOKE D E, HOLSTEIN H. Case memory and retrieval based on the Immune System[C]. In the First International Conference on Case Based Reasoning, Published as Case-Based Reasoning Research and Development, Ed. Manuela Weloso and Agnar Aamodt, Lecture Notes in Artificial Intelligence 1010, 1995(10):205-216.

- [20] GONZÁLEZ F, CARDONA C, et al. A Scalable Artificial Immune System Model for Dynamic Unsupervised Learning[C]. In the proceedings of the Genetic and Evolutionary Computation Conference (GECCO), July 12-16, 2003. LNCS 2723, p. 219 ff.

- [21] DASGUPTA D, GONZALEZ F, et al. An Novel Artificial Immune System Approach to Robust Data Mining[C]. In the proceedings of the International Conference Genetic and Evolutionary Computation (GECCO), New York, July 9-13, 2002.

- [22] ABBATTISTA F, DANTO G. An associative Memory Based on the Immune Network[C]. Proceedings of IEEE International Workshop on Neural Networks. Washington; USA, IEEE. 1996.

- [23] TIMMIS J, NEAL M. A resource limited artificial immune system for data analysis[J]. Knowledge Based Systems, 2001, 14(3-4):121-130.

- [24] KNIGHT T, TIMMIS J. Assessing the performance of the resource limited artificial immune system AINE[A]. Technical Report 3-01, Canterbury, Kent. CT2 7NF, May 2001.

- [25] JIAWEI HAN, MICHELINE KAMBER. Data Mining: Concepts and Techniques[M]. London: Academic Press, 2000.

- [26] 陆建江, 宋自林, 钱祖平. 挖掘语言值关联规则[J]. 软件学报, 2001, 12(4):607-611.

- [27] HAN S J, PEI J, YIN Y. Mining frequent patterns without candidate generation[C]. In: ACM-SIGMOD, Dallas, 2000.

- [28] AGRAWAL R, SRIKANT R. Fast algorithms for mining association rules [C]. In: Proc of the 20th VLDB Conf Santiago, Chile, 1994: 487-499.

- [29] 倪晏, 徐晓飞, 邓胜春, 等. 基于频繁项目对支持矩阵的 Apriori 优化算法[J]. 小型微型计算机系统, 2004(05):88-90.

- [30] 王新宇, 杜孝平, 谢昆青. FP-growth 算法的实现方法研究[J]. 计算机工程与应用, 2004, 9:174-176.

- [31] MOHAMMED J ZAKI. Scalable algorithms for association mining[J]. IEEE Trans on Knowledge and Data Engineering, 2000, 12 (3) : 372-390.

- [32] BLAKE C L, MERZ C J. UCI Repository of Machine Learning Databases[DB/OL]. http://www.ics.uci.edu/.

## 第 7 章 自适应免疫应答网络(AIRN)及其聚类挖掘方法

免疫应答机理是生物免疫系统的一个重要特征，免疫应答的实质是一个识别、效应和记忆的过程。本章根据生物免疫系统的应答机制和原理，从模拟免疫响应中 B 细胞的结合、B 细胞的激励以及复制等方面入手，建立一个自适应的人工免疫应答模型，并将其应用在聚类数据挖掘中。

### 7.1 免疫应答和免疫网络

人类的免疫系统保护自身不受细菌、病毒等的感染。人类免疫系统有两类：固有性免疫系统和适应性免疫系统。固有性免疫系统（如皮肤）对不同病菌都具有抵抗作用，没有记忆功能；适应性免疫系统专门针对特定的入侵者，并具有一定的免疫记忆功能[1]。适应性免疫系统对工程应用领域具有下述启发意义的特点：

- (1) 免疫系统从数百个抗体基因中能产生至少数百万种的抗体，防止外在抗原的感染。

- (2) 免疫系统是无中心控制器的分布系统。

- (3) 免疫系统能对动态变化的情况快速适应。

- (4) 免疫系统的记忆是按内容寻址的，一种抗原被记忆后，类似的抗原也可以被抗体识别。

本章所说的免疫特指适应性免疫。根据免疫学的细胞学机理，生物体内有大量循环的免疫细胞，其中淋巴细胞是参与免疫应答的最主要的免疫细胞，具有专一性、多样性、记忆和自适应等特点。淋巴细胞有 T 细胞和 B 细胞两大类，B 细胞能产生抗体以消灭入侵的抗原，T 细胞具有促进和抑制 B 细胞反应的作用，免疫系统的作用机制如图 7-1 所示。

![](img/29cc31ba9619fdc287c02dcd2a1fbc46_187_0.png)

图 7-1 免疫系统的作用机制

#### 7.1.1 免疫应答

为切实模拟免疫机理构造设计网络，有必要对免疫应答过程做一个回顾。当免疫系统第一次遇到某一类型的抗原时，只有少数的免疫细胞能识别该抗原的表位，能够识别抗原的细胞受到激励，进行快速的自身复制，该过程即为克隆扩增(clonal expansion)。克隆扩增的过程同时伴随着超频变异(hypermutation)，也就是在细胞复制过程中，细胞产生一定的变异，使产生的克隆越来越能够识别抗原。大部分克隆在干扰素、细胞因子等分子的作用下分化为浆细胞，浆细胞能够产生抗体以清除入侵抗原，这个过程称为首次应答(primary response)。少量的克隆分化为记忆细胞，如果有类似的抗原入侵，会导致更快的和更多的抗体产生，这个过程称为二次应答(secondary response)。这里要注意的是，不一定要完全相同的抗原的入侵，类似的抗原入侵也会导致二次应答。

免疫应答作为生物免疫系统调节生物体自身动态平衡的一个重要手段，是抗原选择具有特异性的淋巴细胞，使之活化、增殖、分化并产生免疫效应，使机体避免抗原侵害的过程。简而言之，免疫应答是机体对入侵的自身防御反应。

免疫系统具有两种形式的应答：初次应答和再次应答。初次应答发生在免疫细胞初次遇到某种抗原并与其发生作用时。初次应答一般需要一段相当长的时间来消灭抗原。当同样的抗原再次入侵时，免疫系统迅速产生特定的二次应答。从计算科学的观点看初次应答对应于对训练数据进行学习，二次应答对应于模式聚类问题，即把新输入的数据归为相应的类别。值得注意的是，二次应答不仅会由同样的抗原的再次侵入引起，还会由相似抗原的侵入引起，这也就是通常所说免疫记忆具有联想性。

免疫应答的基本过程由三个阶段组成：感应阶段、增殖分化阶段和效应阶段。在人工免疫系统中，可以通过抗原匹配、抗体克隆选择、抗体变异以及亲和力成熟匹配过程描述上述三个阶段。

#### 7.1.2 免疫网络假设

为了解释免疫系统的各种现象，N. K. Jerne 提出了独特型免疫网络假设[2]，根据该假设，在免疫系统中免疫细胞不是一个个孤立的个体，而是通过细胞与细胞间的相互激励和抑制作用，形成一种网络结构。

N. K. Jerne 提出，抗原的表面具有反映抗原特性的部位，即表位(epitope)或抗原决定位。特定的抗体通过自身的可变区与抗原决定位的结合来识别这种抗原，抗体上可以与抗原决定位结合的部位，即对位(paratope)，表位和对位的关系如同锁和钥匙的关系，这种结合关系导致了免疫应答的特异性。不仅是抗原具有能够被抗体识别并结合的抗原决定簇，抗体本身也具有能够被其他抗体识别并结合的抗原决定簇，即抗体也具有抗原性。抗体上能够被其他抗体识别的抗原决定位一般为独特型（idiotype）抗原决定簇，或是独特位（idiotope）。

当某种抗体的对位与表位或独特位结合后，分泌这种抗体的 B 细胞会受到激励，当激励超过一定阈值后，进行克隆扩增，从而产生免疫应答。同时，被结合的抗原或抗体受到抑制，抗体对应的 B 细胞数量会减少。大量抗体的相互作用以及与抗原的作用组成一个网络，如图 7-2 所示。图中，ns 为 B 细胞间的激励，nn 为 B 细胞间的抑制，ps 为抗原对 B 细胞的激励。

![](img/29cc31ba9619fdc287c02dcd2a1fbc46_189_0.png)

#### 7.1.3 相关假设的简化

免疫应答是一个非常复杂的生物化学过程，要建立一个面面俱到的模型来描述免疫应答所有现象是不可能的，在工程上也没有特别大的意义。因此，从信息处理和工程应用的角度出发，结合免疫网络假设和细胞学的机理，建立免疫应答的模型，使其可以用于复杂系统的建模、控制以及模式识别等方面，这是人工免疫系统研究应该关注的课题。为此，我们在人工免疫系统的模型建立时，对免疫系统作了如下假设：

- (1) 由于 T 细胞的作用机理比较复杂，在建立细胞之间的相互作用模型时没有考虑 T 细胞的作用，只考虑 B 细胞之间的激励和抑制关系。

- (2) 在免疫系统中，同一个 B 细胞分泌的抗体性质都是一样的，这里对 B 细胞和其分泌的抗体不加以区分。当考虑与抗原作用时，采用抗体的概念；在克隆扩增时，采用 B 细胞的概念。整个系统由抗原、抗体（B 细胞）群和骨髓组成。

- (3) 不考虑抗原提呈、细胞介导等作用的时间，即抗原侵入后周围 B 细胞会立刻产生作用。

- (4) B 细胞在整个网络中不是固定连接的，而是随机流动的。在每个时刻，B 细胞（抗体）只与当时处于它周围的 B 细胞相互作用。

通过以上假设，希望构造一种以搜索原始数据的模式为目标的自适应的人工免疫应答网络。在探讨的人工免疫网络模型设计中，同时借鉴已有的人工免疫网络模型和算法中的成熟有效的方法[3-10]，它们包括：

- (1) 用抗体网络的结构形式来实现学习和记忆；

- (2) 用抗原和抗体的相似性度量来实现识别；

- (3) 通过克隆和超变异机制来产生适当的抗体，从而使网络中的抗体结构可以反映所遇到的抗原特征；

- (4) 通过抗体之间的刺激和抑制作用来保持网络的稳定，同时使网络具有继续学习的能力，即一旦有新的抗原的刺激，网络结构马上做出相应的反应；

- (5) 控制网络的规模，使得在新抗体加入的同时，那些长期得不到刺激的无用抗体被删除。

下面将详细描述自适应免疫应答网络各部分的组成和功能的实现过程。

### 7.2 自适应免疫应答网络 (AIRN) 模型

#### 7.2.1 AIRN 的组成

从上述简化的免疫应答和免疫网络假设出发，明确网络的数据分析目标，根据问题特点确定自适应免疫应答网络（Adaptive Immune Response Network，AIRN）的抗原、抗体的形式，以及系统的构成。

- (1) 抗原——原始数据组；

- (2) 抗体——各模式类的特征值，在网络中表现为一个数据项；

- (3) 系统结构——抗体通过相互作用构成的网络；

- (4) 应用目标就是使网络经过“学习”后，能够反映原始数据集所包含的模式类。

于是，整个网络的构成规则为：

- (1) 网络由数据项组成，每个数据项包括一个数据值、一个刺激度值；

- (2) 网络中的数据项之间存在连接，两个数据项之间是否连接由两个数据项的相似度以及一个阈值确定：如果数据项之间的相似度大于阈值则连接，否则，就不连接；

- (3) 每一组互连的数据项所构成的子网络，就代表一个模式类；

- (4) 通过训练，网络最终达到一个稳定的结构，这个稳定的结构就是模式分析的结果。

#### 7.2.2 AIRN 的初始化

抗体对抗原的识别是免疫系统最重要的功能，也是引起免疫应答的首要条件。识别必须满足一些条件。当两个具有互补特性的细胞相遇时，它们相互之间结合力（亲和力）的大小取决于两者之间的互补程度。为了解决抗体对抗原的匹配识别问题，这里引入 A. S. Perelson 提出的形态空间概念[11]：

假如 \(m\) 个特征影响分子之间的相互作用，\(P_i\) 是第 \(i(i=1,2,\cdots,m)\) 个特征，则每个分子可被表示为 \(m\) 维空间 \(S(S=P_1 \times \cdots \times P_m)\)，\(S\) 可以是 \(m\) 维海明空间或 \(m\) 维欧氏空间中的一个点。

将待处理的数据定义为抗原集合 \(AG=\{ag_1, ag_2, \cdots, ag_I\}, AG \in R^I\)，\(I\) 为抗原空间规模。抗体识别抗原的过程就是免疫应答识别待处理数据模式的过程。通过免疫应答识别过程，获得的记忆细胞集合 \(M\) 即为需求解的模式。设 \(M\) 的规模为 \(I'\)，则 \(M \in R^{I'}\) 免疫应答结束后，应有 \(I > I'\)，抗体空间被压缩。免疫应答过程的形式化描述为：

![](img/29cc31ba9619fdc287c02dcd2a1fbc46_192_0.png)

设系统中的抗原具体表示为：

![](img/29cc31ba9619fdc287c02dcd2a1fbc46_192_1.png)

式中，\(ag_i\) 表示第 \(i\) 个抗原；\(ag_{ij}\) 表示第 \(i\) 个抗原的第 \(j\) 个向量。

通过随机产生：

$$
AB = (ab_1 \cdots ab_k)^T = \begin{pmatrix} ab_{11} & \cdots & ab_{1m} \\ \vdots & \ddots & \vdots \\ ab_{k1} & \cdots & ab_{km} \end{pmatrix} \quad (7-2)
$$

\(ab_{ij} \in [0,1]^m, i=1, \cdots, k; j=1, \cdots, m\)

作为新生成的抗体。式中，\(ab_i\) 表示第 \(i\) 个抗体，\(ab_{ij}\) 表示第 \(i\) 个抗体的第 \(j\) 个向量。

#### 7.2.3 抗原提呈

把抗原与抗体间的欧氏距离作为衡量两者之间匹配程度的一个指标。将抗原中的每个元素分别与抗体中的每个元素相匹配，对任意抗原 \(ag_i\) 和任意抗体 \(ab_j\)，两者之间的距离 \(d(i, j)\) 可通过下式计算：

$$
d(i,j) = \left[ \sum_{t=1}^m (ag_{it} - ab_{jt})^2 \right]^{1/2} \quad (7-3)
$$

对任意抗体 \(ab_i\) 和任意抗体 \(ab_j\)，两者之间的距离 \(s(i, j)\) 可通过下式计算：

$$
s(i,j) = \left[ \sum_{t=1}^m (ab_{it} - ab_{jt})^2 \right]^{1/2} \quad (7-4)
$$

对任意 \(ab_j (ab_j \in AB, j=1, \cdots, k)\)，所受到的来自抗原 \(ag_i (ag_i \in AG, i=1, \cdots, I)\) 的激励 \(sl(i, j)\) 为：

$$
sl(i,j) = \begin{cases} 1/ \sqrt{d(i,j)} & d(i,j) < T_x \\ 0 & \text{other} \end{cases} \quad (7-5)
$$

\(i = 1, 2, \cdots, I; j = 1, 2, \cdots, k\)

式(7-5)中：

$$
T_x = k_x \frac{\sum_{i=1}^I \sum_{j=1}^k d(i,j)}{\frac{I \times k}{2}} \quad (7-6)
$$

表示抗原对抗体激励的阈值，其中：\(k_d \in (0, 1]\) 且为常数。

#### 7.2.4 克隆和变异

当抗体 \(ab_j\) 所受到来自抗原的刺激达到一定程度 \([d(i, j) < T_d]\) 时，免疫系统被激活，系统开始对抗体进行克隆增殖。增殖是指免疫细胞发生无性、有丝分裂。自我复制（没有交叉）的过程。抗体克隆的数目与其所受到的激励水平成正比，受激励越大的抗体克隆的数目越多；受激励越小的抗体克隆的数目越少。由于受抗原 \(ag_i\) 激励所导致的抗体 \(ab_j\) 的克隆数目为：

\(c_1(i, j) = \begin{cases} \lfloor sl(i, j) \rfloor & d(i, j) > T_d \\ 0 & \text{other} \end{cases}\)  
(7-7)

式中，\(\lfloor \cdot \rfloor\) 表示取与 \(sl(i, j)\) 最接近的整数。抗体 \(ab_j\) 经 \(ag_i\) 刺激产生克隆的集合为 \(Ab_{ji}\)，抗体 \(ab_j\) 受抗原群体刺激所产生的克隆集合 \(C_j\) 为：

\(C_j = Ab_{j1} \cup Ab_{j2} \cup \cdots \cup Ab_{jc_1(i, j)} = \bigcup_{t=1}^{c_1(i, j)} Ab_{jt}\)  
(7-8)

相应的抗体集合 \(AB\) 经克隆过程后新生成的抗体集合 \(C\) 为：

\(C = C_1 \cup C_2 \cup \cdots \cup C_t = \bigcup_{t=1}^k C_t\)  
(7-9)

克隆细胞要经历突变（伴随着高变异率）过程，从而使得新产生的细胞与所选择的抗原以更高的亲和力相匹配。在变异过程中每种免疫细胞变异的速率与细胞受体受抗原的激励程度的大小成反比：其受抗原的激励越大，变异越小；其受抗原的激励越小，变异越大。定义：\(Mut(i, j)\) 为抗体 \(ab_j\) 受到抗原 \(ag_i\) 刺激后新产生的抗体的变异率。

\(Mut(i, j) = d(i, j)\)  
(7-10)

由抗原 \(ag_i\) 入侵引起的 \(ab_j\) 的克隆所生成的 \(c_j\) 变异为：

\(c'_j = c_j + Mut(i, j) \times (c_j - ag_i)\)  
(7-11)
