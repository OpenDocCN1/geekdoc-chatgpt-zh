
用例优先级排序问题，将排序问题转化为有向图，并对粒子采用了序列编码来表示测试用例的排序，同时为了解决易陷入局部最优问题引入了 Additional 策略。

### (1) 问题一

为了验证 AIBAT 得到的 Pareto 解集的质量，用 AIBAT 与对比算法来对测试用例集进行排序，并把二者所得的解集进行比较。二者的 Pareto 解集分布如图 7-12 所示。其中，横坐标代表 EET，纵坐标代表 APTC，图中 • 表示用 AIBAT 得到的 Pareto 解集，★表示用 A-PSO 得到的 Pareto 解集。

![](img/7c49b278a5834275e89b631ebe8836f8_228_0.png)

![](img/7c49b278a5834275e89b631ebe8836f8_228_1.png)

![](img/7c49b278a5834275e89b631ebe8836f8_228_2.png)

![](img/7c49b278a5834275e89b631ebe8836f8_228_3.png)

![](img/7c49b278a5834275e89b631ebe8836f8_228_4.png)

图 7-12 Pareto 解集

### (2) 问题二

缺陷检测速率如图 7-13 所示。其中，横坐标为不同的 TCP 问题求解方法，纵坐标为 APTC 评价指标。

![](img/7c49b278a5834275e89b631ebe8836f8_229_0.png)

图 7-13 缺陷检测速率

分析一下本节算法和对比算法求出来的不同测试程序的 APTC 指标，图 7-13 中主要体现了两种信息，分别是范围和均值。首先看范围，由于不同的测试用例集大小、覆盖率等环境不同，可以看出经过排序后的解集中，APTC 的数量范围等没有明显的规律，例如，在 flex、grep 和 space 3 个被测程序中，APTC 的范围和波动较大，而在 schedule 和 printtokens 测试程序中，APTC 的范围与波动较小，这与测试用例集有关。但是从均值上来看，本书提出的算法均值普遍高于 A-PSO 算法，因此在这个方面，本书提出的算法具有更好的求解能力。

## 本章参考文献

- [1] Rothermel G, Untch R H, Chu C, et al. Prioritizing test cases for regression testing[J]. IEEE Transactions on Software Engineering, 2000, 27(5): 102-112.
- [2] 郑金华. 多目标进化算法及其应用[M]. 北京: 科学出版社, 2007.
- [3] Pareto V. Cours Economic Politique, volume I and II [M]. Lausanne and Paris: Rougeand Cie, 1897.
- [4] Michalewicz Z, Krawczyk J B, Kazemi M, et al. Genetic algorithms and optimal control problems[C]//IEEE Conference on Decision & Control. IEEE, 1990.
- [5] Cavicchio J, Daniel J. Reproductive adaptive plans[C]//In Proc of the ACM 1972 Annual Conf., 1972.
- [6] Colorni A. Distributed optimization by ant colonies[C]//Proc of the First European Conference on Artificial Life. The MIT Press, 1991.
- [7] Elbaum S, Malishevsky A, Rothermel G. Incorporating varying test costs and fault severities into test case prioritization[C]//Icse. IEEE, 2001.
- [8] Li Z, Harman M, Hierons R. Search Algorithms for Regression Test Case Prioritization [J]. IEEE Transactions on Software Engineering, 2007, 33(4): 225-237.
- [9] 张卫祥, 魏波, 社会森. 一种基于遗传算法的测试用例优先排序方法[J]. 小型微型计算机系统, 2015, 36(9): 1998-2002.
- [10] Kennedy J, Eberhart R. Particle Swarm Optimization[C]//Icnn95-international Conference on Neural Networks. IEEE, 1995.
- [11] Tyagi M, Malhotra S. Test case prioritization using multi objective particle swarm optimizer[C]//2014 International Conference on Signal Propagation and Computer Technology (ICSPCT). IEEE, 2014.
- [12] Niu B, Zhu Y, He X. Multi-population Cooperative Particle Swarm Optimization [C]// ECAL’05, Springer, Berlin, Heidelberg, 2005: 874-883.
- [13] Chen Y, Li Z, Zhao R. Applying PSO to multi-objective test cases prioritization [J]. Computer Science, 2014, 41(5): 72-77.
- [14] 陈云飞, 李征, 赵瑞莲. 基于 PSO 的多目标测试用例预优化[J]. 计算机科学, 2014, 41(5): 72-77.
- [15] 杨芳. 基于多目标粒子群算法的测试用例优先级排序研究[D]. 重庆: 西南大学, 2017.
