

再者，这种算法是基于目标函数的聚类算法，一般都采用梯度法求解极值，由于梯度法的搜索方向总是沿着能量减小的方向进行，因此易陷入局部极小值。这些缺陷较大地限制了它的应用范围。为了克服上述缺点，近几年来人们提出了对目标函数进行优化的各种算法，如文献[18]根据遗传算法的原理，提出了以 K-均值算子代替遗传算法中的交叉算子，设计了一种混合遗传算法，并根据有限状态齐次马尔可夫链方法证明了该算法是收敛于全局最优的。此后有的研究采用了聚类中心的浮点编码方式，设计了浮点数交叉和变异算子，从而提高了遗传算法的搜索效率。但是，实验表明，当样本数目、类别和维数较多时，这些基于遗传算法的聚类方法常会产生早熟现象。由于进化算法在进化过程中不可避免地会产生退化现象，因此将导致进化后期的波动以及迭代次数过长和聚类准确率不高的缺点。近年来免疫计算研究的迅速发展，给解决以上的问题带来了新的思路和方法[19, 20, 21]。

### 5.3.2 K-均值聚类算法[2]

该算法将 \( n \) 个向量 \( x_j(1, 2, \cdots, n) \) 划分为 \( c \) 个组 \( G_i(i=1, 2, \cdots, c) \)，并求每组的聚类中心，使得非相似性（或距离）指标的价值函数（目标函数）达到最小。当选择欧氏距离为组 \( j \) 中向量 \( x_k \) 与相应聚类中心 \( c_i \) 间的相似性指标时，价值函数可定义为：

$$
J(u, x) = \sum_{i=1}^{c} J_i = \sum_{i=1}^{c} \sum_{j=1}^{n} (u_{ij}) \| x_j - c_i \|^2 \quad (5-17)
$$

这里 \( u_{ij} \) 是向量 \( x_j \) 属于组 \( G_i(i=1, 2, 3, \cdots, c) \) 的程度，它的值取 0 和 1。每一次分组时，\( n \) 个数据向量按距离的大小，分属于不同的 \( G_i(i=1, 2, 3, \cdots, c) \)，其中

$$
u_{ij} = \begin{cases} 1 & \text{对每个 } k \neq i, \text{如果 } \| x_j - c_i \| \leqslant \| x_j - c_k \| \\ 0 & \text{其他} \end{cases} \quad (5-18)
$$

如果 \( c_i \) 是 \( x_j \) 最近的聚类中心，则 \( x_j \) 属于 \( i \) 组。由于一个给定的数据点只能属于一个组，因此求属矩阵 \( U \) 具有如下性质：

\(\sum_{i=1}^{c} u_{ij} = 1, \forall_j = 1, \cdots, n, \text{且} \sum_{i=1}^{c} \sum_{j=1}^{n} u_{ij} = n\) (5-19)

另一方面，如果固定 \( u_{ij} \)，则使式 (5-17) 最小的最佳的聚类中心就是 \( i \) 组中所有向量的均值：

\(c_i = \frac{1}{|G_i|} \sum_{k, x_k \in G_i} x_k\) (5-20)

式中，\( |G_i| = \sum_{j=1} u_{ij} \) 是 \( G_i \) 的模。

显然，数据聚类问题本质上是一个组合优化问题，可以利用迭代算法求解。

### 5.3.3 聚类问题的免疫表示

在聚类分析中，把要聚类的数据对象视为抗原，把聚类中心看做是免疫系统中的抗体，数据对象的聚类过程就是免疫系统不断地产生抗体，识别抗原，最后产生出可以捕获抗原的最佳抗体过程。

设集合中的对象已抽象为空间 \( R^n \) 中的点，其相似程度已抽象为 \( R^n \) 中的距离。为了将集合中的点按照其相互间距离分为 \( K \) 类，可采取如下的步骤。

产生初始群体及其编码：在集合中随机选取 \( N \) 组对象，每组 \( K \) 个点，代表着聚类的 \( K \) 个中心。将每一组对象编码为一个 \( N \times K \) 维的种群个体 \( c \)，随机选取的 \( N \) 组对象构成了初始种群 \( C_0 \)。也就是说，对搜索空间进行 \( N \times K \) 维的实数编码。初始群体的规模为 \( N \)。

### 5.3.4 抗体浓度计算

把抗原和抗体分别对应于待求解的聚类数据对象和聚类中心 C，其中的一个解中心（或抗体）\(c_i\)，这个抗体的亲和度函数为 \(f(c_i)\)。\(n\) 个抗体构成了一个非空免疫系统集合 \(C\)。若规定抗体 \(c_i\) 在集合 \(C\) 上的距离为：

\(d_i = d(c_i) = \sum_{j=1, i \neq j}^n \| c_i - c_j \|\) (5-21)

所有抗体之间的距离和为：

\(d = \sum_{i=1}^n d(c_i) = \sum_{i=1}^n \sum_{j=1, j \neq i}^n \| c_i - c_j \|\) (5-22)

则抗体 \(c_i\) 的距离浓度可定义为：

\(C(c_i) = 1 - \frac{d_i}{d} = 1 - \frac{\sum_{j=1, i \neq j}^n \| c_i - c_j \|}{\sum_{i=1}^n \sum_{j=1, i \neq j}^n \| c_i - c_j \|}\) (5-23)

显然，抗体之间的距离越大，其距离浓度越小，反之则浓度越大。

### 5.3.5 亲和度函数计算

亲和度函数选择。当前抗体群体中的每一个体 \(c\)，都对应着 \(K\) 个中心中的一个，对于这 \(K\) 个中心，按照 \(K\)-均值算法将集合分为 \(K\) 簇。对于第 \(i\) 簇 \(G_i\)，其簇内的距离定义为：

\(S_i = \frac{1}{|G_i|} \sum_{x \in c_i} |x - \bar{c_i}|\) (5-24)

式中，\(\bar{c_i}\) 是 \(G_i\) 簇的均值。定义簇 \(G_i\) 和簇 \(G_j\) 之间的簇间距离为 \(d_{ij} = \| \bar{c_i} - \bar{c_j} \|\)，并定义 \(R_i = \max_{j, j \neq i} \{ (S_i + S_j) / d_{ij} \}\)，则聚类指标可定义为：

\(DB = \frac{1}{K} \sum_{i=1}^K R_i\) (5-25)

那么抗体 \(c_i\) 的亲和度可定义为：

\(f(c_i) = 1/DB\) (5-26)

对亲和度函数线性变换,此处定义：

\(f'(c_i) = \frac{f_{max} - f(c_i)}{f_{max} - f_{min}}\)

\(f_{max}\) 和 \(f_{min}\) 为种群中亲和度的最大值和最小值,这样抗原与抗体之间的亲和度函数转换为：

\(D(c_i) = \frac{1}{1 + f'(c_i)}\)

在计算完一个抗体的亲和度之后,用所产生的 K 个均值 \(\{\bar{c}_i | i = 1, \cdots, K\}\) 构成新的个体,并以此代替当前的个体 \(c\)。

### 5.3.6 克隆变异和终止条件选择

很多免疫算法采用变异算子对二进制抗体进行克隆变异,在基于距离浓度的免疫算法聚类挖掘中,可以利用实数值表示抗原和抗体,通过对一个给定抗体加入来自某区间的随机数来产生变异的新抗体,可选择实数交叉与非均匀变异法。

群体更新按照个体亲和度函数 \(f(c)=1/DB\) 和浓度选择;最后,进行停机条件的判断, \(|J(t+1)-J(t)|<\epsilon\)。若条件满足,则停止运行并输出结果。否则,继续。

### 5.3.7 仿真实验

采用 K-均值算法、GA 算法和我们提出的基于距离浓度免疫算法的聚类方法分别对下列两组数据进行仿真计算。遗传算法的交叉概率取为 0.60,变异概率取为 0.001,群体规模为 100。

第一组数据是 400 个随机分布的 4 维随机向量,聚类数目是 8,不仅规模大,而且完全是随机分布的,没有明显的类别分界,存在大量的局部极优点,是很困难的优化问题。

第二组是 Fisher 的 Iris 植物样本数据,由分别属于 3 种植物的 150 个样本组成,每个样本均为 4 维模式向量,代表植物的 4 种特征数据。用 3 种算法分别实验，其中的一次实验的聚类指标函数曲线如图 5-3 和图 5-4 所示，其中的三次实验的聚类指标函数结果见表 5-8 和表 5-9，对 Iris 数据集实验获得正确聚类的性能比较见表 5-10。

![](img/29cc31ba9619fdc287c02dcd2a1fbc46_159_0.png)

图 5-3 随机分布数据集聚类指标函数曲线

![](img/29cc31ba9619fdc287c02dcd2a1fbc46_159_1.png)

图 5-4 Iris 数据集聚类指标函数曲线

从表 5-8、表 5-9 和表 5-10 中不难看出我们所提出的算法局部收敛速度和全局收敛速度都明显优于 K-均值算法和 GA 算法。特别是对大规模、完全随机分布的数据聚类问题，其优越性更加明显。

表 5-8 随机分布数据集聚类实验结果比较

| 实验次数 | K-均值算法 | GA 算法 | 本算法 |
| :--- | :--- | :--- | :--- |
| 1 | 101.535 | 101.321 | 101.273 |
| 2 | 101.486 | 101.273 | 101.273 |
| 3 | 101.796 | 101.273 | 101.273 |

表 5-9 Iris 数据集聚类实验结果比较

| 实验次数 | K-均值算法 | GA 算法 | 本算法 |
| :--- | :--- | :--- | :--- |
| 1 | 97.073824 | 97.000 126 | 97.000 126 |
| 2 | 97.08453 | 97.000 126 | 97.000 126 |
| 3 | 97.073824 | 97.000 126 | 97.000 126 |

表 5-10 三种方法性能的比较

| 实验方法 | 迭代次数 | 实验次数 | 正确聚类次数 | 聚类正确率/% |
| :--- | :--- | :--- | :--- | :--- |
| K-均值算法 | 60 | 50 | 37 | 74 |
| GA 算法 | 60 | 50 | 47 | 94 |
| 本算法 | 60 | 50 | 48 | 96 |

## 5.4 小结

本章研究了基于距离浓度免疫算法的数据分类、关联和聚类挖掘方法。

通过对数据挖掘分类任务的分析，提出了距离浓度免疫算法的数据分类方法，这种方法克服了遗传分类等算法的早熟问题，算法给出的特征选择策略，避免了常规分类方法需要考查各特征属性之间关联度的问题。实验显示我们提出的算法可以获得较高的预测准确率，算法也具有更加稳定的性能。

提出的基于距离浓度免疫算法的关联挖掘方法采用多点随机搜索策略，实现了频繁项集的并行搜索，免疫记忆策略保护了有效模式，提高了算法的有效性。实验结果验证了我们提出的算法的可行性。

提出的基于距离浓度免疫算法的聚类挖掘方法是在传统的K-均值算法中有机地集成了免疫进化的机理，提高了聚类算法的性能。实验结果均表明，我们提出的聚类算法不仅有效地避免了传统聚类算法在收敛时易陷入局部极小值问题，而且明显地克服了对初始化选值敏感性的缺点，同时有较快的收敛速度。

## 参考文献

- [1] 廖开际. 数据仓库与数据挖掘[M]. 北京:北京大学出版社, 2008.
- [2] JIAWEI HAN, MICHELINE KAMBER. Data Mining: Concepts and Techniques[M]. London: Academic Press, 2000.
- [3] VAFAIE H, DE JONG K A. Robust feature selection algorithms [C]. In: Proceedings of IEEE Conference on Tools with Artificial Intelligence: 356-363.
- [4] 陈彬, 洪家荣, 王亚东. 最优特征选择问题[J], 计算机学报, 1997, 20(2): 133-138.
- [5] HALEH VAFAIE, IBRAHIM F IMAM. Feature selection methods; Genetic Algorithms vs Greedy-like search[R]. George Mason University, Fairfax, VA, 22030.
- [6] FOROUTAN I, SKLANSKY J. Feature selection for automatic classification of non-Gaussian data[J]. IEEE Transactions on Systems, Man, and Cybernetics, 1987, 17: 187-198.
- [7] NARENDRA P, FUKUNAGA K. A branch and bound algorithm for feature subset selection [J]. IEEE Transactions on Computers, 1997, 26: 917-922.
- [8] DOM B, NILACK W, SHEINVALD J. Feature selection with stochastic complexity[C]. In: Proceedings of IEEE Conference on Computer Vision and Pattern Recognition, 1999.
- [9] BLAKE C L, MERZ C J. UCI Repository of Machine Learning Databases [DB/OL]. http://www.ics.uci.edu.html.
- [10] DOMINGOS P. Unifying instance based and rule based induction[J]. Machine Learning, 1996, 24(2):141-168.
- [11] VENTURINI G. A supervised inductive algorithm with genetic search for learning attributes based concepts[C]. In: Proceedings of the European Conference on Machine Learning, Vienna, Austria, 1993:280-296.
- [12] JULIET JUAN LIU, JAMES TIN, YAU KWOK. An extended genetic rule induction algorithm[C]. In: Proceedings of IEEE Congress on Evolutionary Computation, San Diego, USA, 2000:458-463.
- [13] AGRAWAL R, SRIKANT R. Fast algorithms for mining association rules[C]. In: Proc of the 20th VLDB Conf Santiago, Chile, 1994: 487-499.
- [14] PARK J, CHEN M, YU P. An effective hash-based algorithm for mining association rules[C]. In: Proc 1995 ACM-SIGMOD Int. Conf Management of Data, San Jose, CA, 1995-05:175-186.
- [15] ZAKI M J. Parallel and distributed association mining: A survey[J]. IEEE Concurrency, Special Issue on Parallel Mechanisms for Data Mining, 1997, 7(40):14-25.
- [16] BRIN S, MOTWANI R, ULLMAN J D, et al. Dynamic itemset counting and implication rules for market basket data[C]. In: Proc 1997ACM-SIGMOD Int Conf Management of Data, Tucson, Arizona, 1997-05: 225-264.
- [17] HAN J, PEI J, YIN Y. Mining frequent patterns without candidate generation [C]. In: ACM-SIGMOD, Dallas, 2000.
- [18] KRISHNA K, MURTY M N. Genetic K-Means Algorithm[J]. IEEE Trans on System, Man Cybernetics, Part B, 1999, 29(3):433-439.
- [19] CASTRO D E, ZUBEN V. Artificial Immune Systems: Part I Basic Theory and Applications [R/OL]. Technical Report-RT DCA 01/99. http://www.dca.fee.unicamp.br.
- [20] 莫宏伟. 人工免疫系统原理与应用[M]. 哈尔滨: 哈尔滨工业大学出版社, 2002.
- [21] 刘韬, 王耀才, 王致杰. 一种基于人工免疫系统的聚类算法[J]. 计算机工程与应用, 2005, 40(19):182-184.

