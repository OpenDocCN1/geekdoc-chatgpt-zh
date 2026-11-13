程度。

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

# (2) 信息熵技术

设群体 $Pop=\{X_1, X_2, \cdots, X_n\}$ 规模为 $N$,个体的组成为 $L$ 个基因,$X_i=[X_i^{(1)}, X_i^{(2)}, \cdots, X_i^{(L)}], i \in \{1, 2, \cdots, N\}, \overline{X}=\{\overline{x}^{(1)}, \overline{x}^{(2)}, \cdots, \overline{x}^{(L)}\}$ 是个体的期望,具体定义如下：

$$\overline{x}^{(j)} = \sum_{i=1}^N (x_i^{(j)} - \overline{x}^{(j)})^2 / N$$ (7-14)

则解集的方差为 $D=(D^{(1)}, D^{(2)}, \cdots, D^{(L)})$,其中个体方差定义为

$$D^{(j)} = \sum_{i=1}^N (\overline{x}^{(j)} / N), j = 1, 2, \cdots, L$$ (7-15)

将规模为 $N$ 的群体根据下列性质划分为 $m$ 个子集 $P_1, P_1, \cdots, P_m$, 其划分的规则如下：

$\cup_{p \in \{P_1, P_1, \cdots, P_m\}} P = Pop, \forall i, j \in \{1, 2, \cdots, m\} \wedge i \neq j, P_i \cap P_j = \varnothing$。定义解集的熵为

$$E = -\sum_{i=1}^m q_i \log q_i$$, 其中 $q_i = |P_i| / N, |P_i|$ 表示 $P_i$ 的规模[5]。解集的方差代表的是解集的群体在求解空间中的分布情况,如果解的群体都是相同的,即 $m=1$, 这种情况下熵的值为 0, 是最小值; 当 $m=N$ 时, 熵则取最大值, 为 $E = \log N$。如果个体越多, 并且在求解空