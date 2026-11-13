
![](img/7c49b278a5834275e89b631ebe8836f8_197_0.png)

图 6-7 4 种算法的最优约简次数和 ECRS 对比

集的测试用例集进行约简，其中 ALO 算法进行测试用例集约简后得到最优子集的次数分别为 58、73、77、74 和 80，占比分别为 49.15%、59.34%、65.81%、56.49%和 62.5%，可以看出，在 4 种算法中，随着测试用例集的规模增大，ALO 算法的约简效果越来越好，具体数据如表 6-9 所示。

表 6-9 不同规模下的最优次数

| 算法 | 100 | 200 | 500 | 1 000 | 全集 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| ALO 算法 | 58 | 73 | 77 | 74 | 80 |
| FA | 41 | 19 | 22 | 34 | 39 |
| GRE 算法 | 15 | 24 | 11 | 23 | 0 |
| 贪心算法 | 4 | 7 | 7 | 0 | 9 |

### (3) 问题三

本书通过对 5 个不同的被测程序的实验结果进行统计，得到如表 6-10 所示的数据。在 5 个被测程序中，ALO 算法的最优次数占比分别为 60%、48.48%、67.6%、68.1%、52.38%，由此可以看出基于 ALO 的优化算法在不同的被测程序中都有着不错的表现，该 ALO 算法在测试用例集约简问题上有着良好的鲁棒性。

表 6-10 不同被测程序下的最优次数

| 算法 | ap_ad.c | ap_CommProgram.c | ap_recorder.c | GuoWangPrepay.c | Ap_ClockBatLowV.c |
| :--- | :--- | :--- | :--- | :--- | :--- |
| ALO 算法 | 69 | 64 | 73 | 79 | 77 |
| FA | 31 | 31 | 21 | 18 | 54 |
| GRE 算法 | 12 | 28 | 9 | 12 | 12 |
| 贪心算法 | 2 | 9 | 5 | 7 | 4 |

## 本章参考文献

- [1] 马军勇,杨胜建.软件回归测试研究[J]. 电子测试, 2009(6): 56-59.
- [2] 李香菊.软件工程课程设计教程[M]. 北京：北京邮电大学出版社,2016.
- [3] 冯灵霞,邵开丽,张亚娟,等.软件测试技术[M]. 西安：西安电子科技大学出版社,2017.
- [4] 杨胜利.软件测试技术[M].广东:广东高等教育出版社,2015.
- [5] Harrold, Jean M. Reduce, reuse, recycle, recover: techniques for improved regression testing[C]//IEEE International Conference on Software Maintenance. IEEE, 2009:5-5.
- [6] Lee J G, Chung C G. An optimal representative set selection method[J]. Information and Software Technology, 2000,42(1):17-25.
- [7] Chen T Y, Lau M F. Heuristics towards the optimization of the size of a test suite [C]. Proceedings of the 3rd International Conference on Software Quality Management,1995,2: 415-424.
- [8] Johnson D S. Approximation algorithms for combinatorial problems[J]. Journal of Computer and System Sciences,1982,9(3):256-278.
- [9] Harrold M J, Gupta R, Soffa M L. A methodology for controlling the size of a test suite[C]//International Conference on Software Maintenance (ICSM), 1990: 302-310.
- [10] 全君林, 陆璐. 基于遗传算法测试用例集极小化研究[J]. 计算机工程与应用, 2009,45(19):58-61.
- [11] Yang X S. Nature-Inspired Metaheuristic Algorithms[M]. Beckington; Luniver Press,2008.
- [12] 王元. 数论方法在近似分析中的应用[J]. 数学的实践与认识,1980(3):73-79.
- [13] 张铃,张铉. 佳点集遗传算法[J]. 计算机学报,2001(9):22-27.
- [14] 周冲波,楼俊钢,程龙. 基于矩阵行列变换的测试用例约简算法[J]. 计算机应用研究,2013(3): 145-148.
- [15] Chvatal V. A greedy heuristic for the set-covering problem[J]. Mathematics of Operations Research,1979,4(3): 233-235.
- [16] Wang S, Ali S, Gotlieb A. Cost-effective test suite minimization in product lines using search techniques [J]. Journal of Systems and Software, 2015, 103: 370-391.
- [17] Lin C T, Tang K W, Wang J S, et al. Empirically evaluating greedy-based test suite reduction methods at different levels of test suite complexity[J]. Science of Computer Programming,2017:S0167642317301077.
- [18] 宫云战,徐健豪,邢颖.萤火虫算法在测试用例集约简中的应用[J]. 哈尔滨工程大学学报,2020,41(4): 577-582.

## 第 7 章 测试用例优先级排序

### 7.1 测试用例优先级排序

### 7.1.1 测试用例优先级排序的定义

Rothermel 等人给出了测试用例优先级的定义，如下所示。
假设存在以下已知条件：T，一组测试用例集；PT，T 的一组排序；f(pi)，从 pi ∈ PT 映射到实数的函数，则求解目标为找到 T' ∈ PT，使得对任意 T'' ∈ PT 且 T'' ≠ T', |f(T')| ≥ |f(T'')| 成立[1]。

其中：PT 表示 T 的所有可能的优先级排列集合；f(pi) 代表适应度函数，用于对排序的有效性进行衡量，一般情况下，适应度函数的值越大表明测试用例排序的有效性越高。

### 7.1.2 多目标优化问题

在实际的工程或者研究中，遇到的决策类问题往往不只包含一个目标，而是由多个或一组相互冲突的目标所组成的，具有多个目标的特点和性质，使这些目标在指定维度的空间内同时达到最优的问题为多目标优化问题。但是在实际情况下，其中一个解往往只能使多个目标中的一个达到较好的结果，对于其他的目标无法得出较好的结果，即无法使所有子目标同时达到最优。在解决多目标优化问题时一般采用折中处理的方法，使各子目标达到平衡，所以目标优化问题的解在一般情况下并不是唯一的，这种性质是多目标优化问题和单目标优化问题两者之间最本质的区别。而通过折中方法得到的解的集合称为 Pareto 最优解集，解集中的元素称为 Pareto 最优解[2]。

- 1. 多目标优化问题的数学建模

多目标优化问题一般情况下是由以下内容组成的：目标函数、决策变量以及约束条件。其数学模型如式(7-1)所示。

```
\begin{cases} \min y = F(x) = (f_1(x), f_2(x), \cdots, f_n(x))^T \\ s. t. \quad g_i(x) \leqslant 0, \quad i=1, 2, \cdots, k \\ \quad h_j(x) = 0, \quad j=1, 2, \cdots, l \\ X = (x_1, x_2, \cdots, x_p) \\ x_{\min} \leqslant x_d \leqslant x_{\max} \end{cases}
```

其中，y 为 n 个目标函数，f_i(x) 代表第 i 个目标函数，g_i(x) \leqslant 0 代表不等式约束条件，h_j(x) = 0 表示等式约束条件，X 为 p 维的决策向量，x_min 表示向量空间的下界，x_max 表示向量空间的上界[33]。

### 2. 多目标优化问题的基本概念

定义 7-1 (可行解) 对于 \forall x \in X，若 x 满足式(7-1)中的约束条件 g_i(x) \leqslant 0 (i = 1, 2, \cdots, k) 和 h_j(x) = 0 (j = 1, 2, \cdots, l)，称 x 为可行解。

定义 7-2 (可行解集) 可行解构成的集合称为可行解集，如式(7-2)所示。

```
X_f = \{x \in X | h(x) \leqslant 0, g(x) = 0\}
```

X_f 在向量空间中对应的可行解表示为式(7-3)。

```
Y_f = f(x) = \bigcup_{x \in X_f} \{f(x)\}
```

定义 7-3 (支配关系) 设 u 和 v 是 R^n 中任意两个不同的向量，如果满足以下两个条件，则称 u 支配 v：

- ① \forall i \in \{1, 2, \cdots, n\}，都满足 u_i \geqslant v_i；
- ② \exists i \in \{1, 2, \cdots, n\}，使得 u_i > v_i。

在这种情况下，称 u 为非支配解，v 为支配解，记作 u \prec v。

定义 7-4 (Pareto 占优) 设 x_A, x_B \in X_f 是式(7-1)中的两个解，对两者进行比较，若 x_A 是 Pareto 占优，则需要满足式(7-4)所表示的条件。

```
\forall i=1, 2, \cdots, m, f_i(x_A) \leqslant f_i(x_B) \wedge \exists j=1, 2, \cdots, m, f_j(x_A) < f_j(x_B)
```

定义 7-5 (Pareto 最优解) Pareto 最优解的条件如式(7-5)所示。

```
\neg \exists x \in X_f : x \succ x^*
```

满足上述条件的解 x^* \in X_f 称为最优解。

定义 7-6 (Pareto 最优解集) 满足多目标优化问题的解集中所有 Pareto 最优解的集合称为 Pareto 最优解集，其定义如式(7-6)所示。

```
P^* \triangleq \{x^* | \neg \exists x \in X_f : x \succ x^* \}
```

定义 7-7 (Pareto 前沿面) 由 Pareto 最优解集 P^* 中所有 Pareto 最优解所对应的目标向量在目标空间内构成的曲面称为 Pareto 前沿面，记作：

```
PF^* = \{F(x^*) \triangleq (f_1(x^*), f_1(x^*), \cdots, f_1(x^*))^T | x^* \in P^* \}
```

### 3. 多目标 Pareto 最优解集

在求解多目标优化算法的研究中，多目标进化算法逐渐为研究学者们所重视，其中最常见的方法是通过构造进化群体的非支配解集并在算法执行过程中不断进行补充，随着迭代过程逐渐接近真正的最优边界。因此，该方法相当于将构建多目标优化问题的最优解集转化为求解进化群体的非支配解集。影响这类进化算法效率的主要因素是构造非支配解集的过程，因为每一次迭代即进化过程都需要构建一次非支配集。高效的构建过程会对算法整体效率的提升起至关重要的作用。常用的构造非支配集的方法主要有枸家法、擂台赛法、递归法和快速排序法。这些方法都有自己的长处和不足，但都是常见的效率较高的方法，这里主要介绍一下枸家法和擂台赛法。

### (1) 枸家法构造 Pareto 最优解集

- 步骤 1: 初始集合为 P，设构造集为 Q，初始时 Q=P，P 的非支配解集为 NDSet，算法开始时 NDSet=∅。
- 步骤 2: 从 Q 中随机取出一个解 x，使得 Q=Q-{x}，初始化偏序集 D=∅。
- 步骤 3: 令 D=D U {y | x > y, ∀ y ∈ Q}。
- 步骤 4: 令 Q=Q-D，若 ∃ z ∈ Q，使得 z > x，则令 NDSet=NDSet U {x}。
- 步骤 5: 当 Q≠∅ 时，跳到步骤 2，否则输出 NDSet。

### (2) 擂台赛法构造 Pareto 最优解集

- 步骤 1: 初始集合为 P，目标个数为 r，设构造集为 Q，初始时 Q=P，P 的非支配解集为 NDSet，算法开始时 NDSet=∅，从 Q 中随意选择一个个体 x，并使用 x 作为比较对象。
- 步骤 2: 令 Q=Q-x, PK=∅, R=∅。
- 步骤 3: 当 Q≠∅ 时，跳到步骤 4，否则跳到步骤 5。
- 步骤 4: 从 Q 中按顺序取出个体，设为 y，并将 y 与 x 进行比较，共有如下 3 种情况。
- 如果 x > y，则令 Q=Q-y，跳到步骤 3。
- 如果 y > x，则令 x=y, Q=Q-y, PK=PK U R, R=∅，跳到步骤 3。
- 如果 x 和 y 无关，则令 R=R U {y}, Q=Q-y，跳到步骤 3。
- 步骤 5: 令 PK' = {y ∈ PK | not(x > y)}, NDSet=NDSet U {x}。
- 步骤 6: 令 Q = PK' U R，如果 |Q| > 1，则跳到步骤 1，否则令 NDSet U Q，输出 NDSet。

### 4. 多目标群体分布性

多目标进化算法中一个重要的内容是解群体的多样性。当在进化的过程中由于进化算子的影响，收敛陷入单个解时，往往得到的解集会聚集到一个小的范围之内，无法得到最优的解集，因此如何保持进化群体的多样性是得到一个好的解集的关键问题。保持多目标进化群体分布性的方法主要有小生境技术、信息熵技术、聚集密度方法、网格和聚类方法[4]，本书主要介绍前两种方法。

### (1) 小生境技术

小生境技术的核心是 3 种机制：预选择机制、排挤机制和共享机制。其实现思想是在群体周围设置一个共享半径，称为小生存半径，计算在小生存半径内群体个体的相似程度。

设个体 $i$ 的适应度是 fitness$(i),m_i$ 为个体 $i$ 的小生存计数,如式(7-8)所示。

$$m_i = \sum_{j \in Pop} sh[d(i, j)]$$ (7-8)

其中,Pop 代表的是进化群体,$d(i,j)$表示个体 $i$ 和个体 $j$ 之间的相似程度,$sh[d]$为共享函数,其定义为

$$sh[d] = \begin{cases} 0, & d > \sigma_{share} \\ 1 - \frac{d}{\sigma_{share}}, & d \leqslant \sigma_{share} \end{cases}$$ (7-9)

其中,$\sigma_{share}$代表小生境半径,在计算时既可以设定为常数,也可以进行动态调整,其动态调整公式如下。

$$\sigma_{share} = N^{\frac{1}{1-m}} \times \frac{d^n}{x}$$ (7-10)

其中:$d^n$ 代表第 $n$ 代的超球半径,其决定因素为非劣解所构建的均衡面;$m$ 代表目标函数的个数;$N$ 代表种群的规模。

定义共享适应度为 fitness$(i)/m_i$,个体的适应度利用群体中的支配关系来定义,非支配集 NDSet 中的非支配个体的个体适应度定义为

$$fitness(i) = N_i / (N + 1)$$ (7-11)

其中,$i \in NDSet, N_i$ 代表个体 $i$ 在群体进化中所支配的个体数目。

支配个体的适应度定义为

$$fitness(j) = 1 + \sum_{i \in NDSet, i > j} fitness(i)$$ (7-12)

根据上文的讨论可知,个体的适应度为

$$fitness(k) = \begin{cases} [0, 1), & k \text{ 为非支配个体} \\ [1, N), & k \text{ 为支配个体} \end{cases}$$ (7-13)

### (2) 信息熵技术

设群体 $Pop=\{X_1, X_2, \cdots, X_n\}$ 规模为 $N$,个体的组成为 $L$ 个基因,$X_i=[X_i^{(1)}, X_i^{(2)}, \cdots, X_i^{(L)}], i \in \{1, 2, \cdots, N\}, \overline{X}=\{\overline{x}^{(1)}, \overline{x}^{(2)}, \cdots, \overline{x}^{(L)}\}$ 是个体的期望,具体定义如下：

$$\overline{x}^{(j)} = \sum_{i=1}^N (x_i^{(j)} - \overline{x}^{(j)})^2 / N$$ (7-14)

则解集的方差为 $D=(D^{(1)}, D^{(2)}, \cdots, D^{(L)})$,其中个体方差定义为

$$D^{(j)} = \sum_{i=1}^N (\overline{x}^{(j)} / N), j = 1, 2, \cdots, L$$ (7-15)

将规模为 $N$ 的群体根据下列性质划分为 $m$ 个子集 $P_1, P_2, \cdots, P_m$, 其划分的规则如下：

$\cup_{p \in \{P_1, P_2, \cdots, P_m\}} P = Pop, \forall i, j \in \{1, 2, \cdots, m\} \wedge i \neq j, P_i \cap P_j = \varnothing$。定义解集的熵为

$$E = -\sum_{i=1}^m q_i \log q_i$$, 其中 $q_i = |P_i| / N, |P_i|$ 表示 $P_i$ 的规模[5]。解集的方差代表的是解集的群体在求解空间中的分布情况,如果解的群体都是相同的,即 $m=1$, 这种情况下熵的值为 0, 是最小值; 当 $m=N$ 时, 熵则取最大值, 为 $E = \log N$。如果个体越多, 并且在求解空
