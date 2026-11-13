分析一下本节算法和对比算法求出来的不同测试程序的 APTC 指标,图 7-13 中主要体现了两种信息,分别是范围和均值。首先看范围,由于不同的测试用例集大小、覆盖率等环境不同,可以看出经过排序后的解集中,APTC 的数量范围等没有明显的规律,例如,在 flex、grep 和 space 3 个被测程序中,APTC 的范围和波动较大,而在 schedule 和 printtokens 测试程序中,APTC 的范围与波动较小,这与测试用例集有关。但是从均值上来看,本书提出的算法均值普遍高于 A-PSO 算法,因此在这个方面,本书提出的算法具有更好的求解能力。

# 本章参考文献

+   [1] Rothermel G,Untch R H,Chu C,et al. Prioritizing test cases for regression testing[J]. IEEE Transactions on Software Engineering,2000,27(5): 102-112.
+   [2] 郑金华. 多目标进化算法及其应用[M]. 北京: 科学出版社,2007.
+   [3] Pareto V. Cours Economic Politique,volume I and II [M]. Lausanne and Paris: Rougeand Cie, 1897.
+   [4] Michalewicz Z, Krawczyk J B, Kazemi M, et al. Genetic algorithms and optimal control problems[C]//IEEE Conference on Decision & Control. IEEE, 1990.
+   [5] Cavicchio J,Daniel J. Reproductive adaptive plans[C]//In Proc of the ACM 1972 Annual Conf. ,1972.
+   [6] Colorni A. Distributed optimization by ant colonies[C]//Proc of the First European Conference on Artificial Life. The MIT Press, 1991.
+   [7] Elbaum S, Malishevsky A, Rothermel G. Incorporating varying test costs and fault severities into test case prioritization[C]//Icse. IEEE, 2001.
+   [8] Li Z,Harman M,Hierons R. Search Algorithms for Regression Test Case Prioritization [J]. IEEE Transactions on Software Engineering,2007,33(4): 225-237.
+   [9] 张卫祥,魏波,社会森. 一种基于遗传算法的测试用例优先排序方法[J]. 小型微型计算机系统,2015,36(9): 1998-2002.
+   [10] Kennedy J, Eberhart R. Particle Swarm Optimization[C]//Icnn95-international Conference on Neural Networks. IEEE, 1995.
+   [11] Tyagi M, Malhotra S. Test case prioritization using multi objective particle swarm optimizer[C]//2014 International Conference on Signal Propagation and Computer Technology (ICSPCT). IEEE, 2014.
+   [12] Niu B, Zhu Y, He X. Multi-population Cooperative Particle Swarm Optimization [C]// ECAL’05, Springer, Berlin, Heidelberg, 2005: 874-883.
+   [13] Chen Y,Li Z,Zhao R. Applying PSO to multi-objective test cases prioritization