
等比例的。因为其过程类似于赌场中轮盘赌的选择过程，所以也叫作轮盘赌选择 (roulette wheel selection)。这种方法是遗传算法中最简单也最常用的选择方法。

遗传算法已经被应用于很多领域，本书主要关心其在测试用例生成中的应用。提出遗传算法可以应用于测试用例生成的记载最早可以追溯到文献[101-103]的研究。后来也有很多学者在这方面进行了研究[104-107]。国内薛云志等人提出基于 Messy GA 的测试用例自动生成方法[7]，把覆盖率表示成测试输入集的函数 F(X)，通过 Messy GA 不需要染色体模式排列的先验知识即可对 F(X) 进行迭代寻优，提高了搜索的并行性，最终提高了覆盖率。英伟等人[108]在进行基于路径覆盖的 Ada 软件测试用例自动生成时采用了遗传算法，并对它和爬山法以及随机法进行了生成测试用例效率的对比实验。

CPU 的运算时间在使用遗传算法时随着输入变量取值范围的增大呈亚线性增长，而随机法则为超线性增长，所以遗传算法比随机法更适合用于大型程序[109]。遗传算法本身很复杂，作为其理论基石之一的隐性并行性的证明还存在严重缺陷[110]。另外，文献[111]在计算评价函数时，只考虑产生分支分歧的那部分分支谓词，而考虑路径上所有分支谓词才能更好地反映当前输入数据的适应程度。

在 2007 年的 ICTAI 国际会议上，Sofokleous 等人提出了一种用遗传算法生成测试用例的方法[112]。在实验部分，作者通过构造的程序对算法的效果进行了验证，其中代码行从 20 行到 2000 行不等，结果如表 2-3 所示。第一列是被测代码行数；第二列是生成的用例数；第三列是被测程序中包含了 if 语句的个数；第四列是覆盖率；第五列是嵌套 if 的个数；第六列是每个表达式的复杂程度，Simple 指包含一个表达式，Medium 指通过一个逻辑运算符连接两个表达式，High 指使用两个以上逻辑运算符连接 3 个以上表达式；最后一列是生成测试用例所用时间。以最后一行推算，作者为一条包含 40 个左右表达式的路径生成测试用例的时间大概是 4 min。随着代码行的增加，覆盖率下降明显。本书在第 5 章也做了类似的实验。本书给出的算法可以在 100%覆盖率的基础上处理更大规模的表达式。

## 表 2-3 文献[112]方法的实验结果

| 代码行数 | 用例数 | 包含 if 个数 | 覆盖率 | 嵌套 if 个数 | 表达式的复杂程度 | 测试时间 |
|---|---|---|---|---|---|---|
| 20 | 3 | 2 | 100% | 0 | Simple | ~0 s |
| 20 | 3 | 2 | 100% | 1 | Medium | ~1 s |
| 20 | 4 | 3 | 100% | 1 | High | ~5 s |
| 50 | 4 | 3 | 100% | 1 | Simple | ~15 s |
| 50 | 5 | 4 | 100% | 1 | Medium | ~23 s |
| 100 | 4 | 3 | 100% | 1 | Simple | ~45 s |
| 100 | 5 | 4 | 100% | 1 | Simple | ~150 s |
| 250 | 4 | 3 | 100% | 1 | Simple | ~350 s |
| 250 | 5 | 4 | 95% | 1 | Medium | ~385 s |
| 500 | 6 | 5 | 86% | 2 | Simple | ~10 s |
| 500 | 7 | 6 | 78% | 2 | Medium | ~15 s |
| 1000 | 8 | 7 | 77% | 3 | Simple | ~15 s |
| 1000 | 9 | 8 | 75% | 3 | Medium | ~20 s |
| 1250 | 10 | 9 | 70% | 4 | Medium | ~30 s |
| 1250 | 14 | 10 | 67% | 5 | High | ~31 s |
| 1500 | 13 | 11 | 69% | 6 | Simple | ~43 s |
| 1500 | 13 | 12 | 68% | 7 | Medium | ~48 s |
| 2000 | 15 | 13 | 65% | 7 | High | ~61 s |

## 4. 蚁群算法

遗传算法和模拟退火算法是在动态测试用例生成领域应用最多的两类 MHS 方法。蚁群优化（Ant Colony Optimization，ACO）算法，又称蚁群算法、蚂蚁算法，在管理和工业上应用较多，可以用来寻找最优解。它由 Marco Dorigo[113]提出，其灵感来自蚂蚁在寻找食物过程中发现路径的行为。蚁群算法是一种模拟进化算法，初步研究表明，该算法具有许多优良的性质。Marco Dorigo 的数值仿真结果表明，蚁群算法具有一种新的模拟进化优化方法的有效性和应用价值。

世界各地的研究人员多年来对蚁群算法进行了大量的研究和应用开发，该算法现已被大量应用于众多领域。究其原因，是因为蚁群算法的求解模式结合了问题求解的快速性、全局优化特征和有限时间内答案的合理性。经过相关领域研究人员的努力，这种优越的问题分布式求解模式已在最初的算法模型基础上得到了很大的拓展和改进。现在也出现了将其用于测试用例生成的研究[114]。在文献[114]中，作者详细描述了用 ACO 算法进行测试用例生成的过程，发现在某些 benchmark 的对比实验中，该算法的性能超过了遗传算法和模拟退火算法。

## 5. 粒子群算法

粒子群优化（Particle Swarm Optimization，PSO）算法，简称粒子群算法，是近年来发展起来的一种新的进化算法。PSO 算法是一种进化计算技术，源于对鸟群捕食的行为研究。于 1995 年由 Eberhart 和 Kennedy 提出[115]。类似于遗传算法，PSO 算法也是一种基于迭代的优化算法。系统先初始化一组随机解，再经过迭代搜索最优值。但是它没有使用遗传算法使用的交叉（crossover）以及变异（mutation），而是粒子在解空间追随最优粒子进行搜索。相比于遗传算法，PSO 算法的优势是容易实现且没有必要调整许多参数。PSO 算法以其实现容易、精度高、收敛快等优点引起了学术界的重视，并且在解决实际问题中展现了其优越性。近些年大量研究将 PSO 算法应用到测试用例生成[116,117]中，并在一些小型的 benchmark 上获得了不错的实验效果。

除了以上介绍的方法，南京大学徐宝文等人[118-120]将演化测试和组合测试技术用于测试用例的自动生成，研发了一个演化测试框架（ETF），为演化测试研究提供了实验平台。北京化工大学的赵瑞莲等人提出面向 EFSM 路径的测试数据生成方法[121,122]，利用禁忌搜索（TS）策略实现了 EFSM 测试数据的自动生成，并使用前向分析的动态程序切片技术提高基于路径的测试用例生成效率[123]。湖南大学的李军义等人[124]利用分支函数线性逼近和极小化方法生成测试用例，并基于选择性冗余思想提高测试性能。动态方法有自身的固有问题，例如，如何处理指针变量的适应值函数等。目前大多数动态方法也仅能处理基本数值类型。

## 2.3 动静结合的测试用例自动生成方法

在静态和动态方法的基础上，研究人员提出了动静结合的方法，即动态符号执行（dynamic symbolic execution）方法或者 concolic（concrete symbolic）方法[9]。这种方法是动态方法和静态方法的折中，使各自的优势最大化。动态符号执行方法输入值的表现形式与静态符号执行方法不同，它是以具体数值来执行程序代码的。在执行过程中，首先启动代码模拟器进行符号执行，在当前路径分支语句的谓词中进行符号约束的收集；然后对该符号约束进行修改得到一条新的可行路径约束，并对其进行求解得出新的可行具体输入，继续对该输入进行新一轮的分析。这种方法相比于静态符号执行的优点是：它使用具体输入进行执行，当符号执行遇到问题时（如难处理的复杂表达式或代码不可见的函数调用），会用具体执行所得结果来代替这些难处理的符号表达式，从而使得求解过程更加容易。

贝尔实验室的 Patrice Godefroid 是动态符号执行方法研究方面的佼佼者[8,10-11]。他在 2005 年首次提出基于动态符号执行的测试用例生成方法 DART[8]。这种方法首先随机生成一个输入用例，通过执行用例记录执行的程序路径，然后静态符号执行此路径获取路径上的约束集合，把约束集合中最后的分支取反，得到另一条路径和此路径的约束集合，求解此约束得到对应路径的测试用例。DART 将动态测试用例生成和模型检测相结合，试图生成覆盖所有程序路径的测试用例，并通过运行时的检测工具如 Purify 发现程序错误。

DART 只处理整型约束。当符号执行无法进行的时候，则采用随机测试。Koushik Sen 作为 DART 的开发者之一，对此方法进行了扩展，提出了对指针指向的结构体类型的测试用例生成方法。而 CUTE[9]扩展了其指针处理方式，能够处理非和非空这类约束的指针表达式。

动静结合法近年来被广泛应用，研究人员不断对其进行改进以提高其性能[125-126]，但依然存在一些问题。2009 年，Kiran Lakhotia 对动静结合的 CUTE 和 AUSTIN 工具通过大型的实际开源应用工程进行测试[127]。实验结果表明，这些方法在一些条件下确实对一些小的程序有很高的覆盖率，但对于实际工程的效果却不甚理想。实际工程的高覆盖率要求对于测试用例生成技术仍然是个很大的挑战。

## 参考文献

- [1] King J C. Symbolic execution and program testing [J]. Communications of the ACM, 1976, 19(7): 385-394.
- [2] Boyer R S, Elspas B, Levitt K N. SELECT—a formal system for testing and debugging programs by symbolic execution [J]. ACM SIGPLAN Notices, 1975, 10(6): 234-245.
- [3] Xu Z, Zhang J. A test data generation tool for unit testing of c programs [C]// Proceedings of the Sixth International Conference on Quality Software (QSIC), 2006: 107-116.
- [4] Zhang J. Symbolic execution of program paths involving pointer structure variables [C]// Proceedings of the Sixth International Conference on Quality Software (QSIC), 2004: 87-92.
- [5] Korel B. Automated software test data generation [J]. IEEE Transactions on Software Engineering, 1990, 16(8): 870-879.
- [6] Phil McMinn. Search-based software test data generation: a survey [J]. Software Testing, Verification and Reliability, 2004, 14(2): 105-156.
- [7] 薛云志, 陈伟, 王永吉, 等. 一种基于 Messy GA 的结构测试数据自动生成方法 [J]. 软件学报, 2006, 17(8): 1688-1697.
- [8] Godefroid P, Klarlund N, Sen K. DART: directed automated random testing [C]// Proceedings of the 2005 ACM SIGPLAN Conference on Programming Language Design and Implementation, 2005, 40(6): 213-223.
- [9] Sen K, Marinov D, Agha G. CUTE: a concolic unit testing engine for C [C]// Proceedings of the 10th European Software Engineering Conference Held Jointly with 13th ACM SIGSOFT International Symposium on Foundations of Software Engineering (ESEC/FSE'05), 2005, 30(5): 263-272.
- [10] Godefroid P. Compositional dynamic test generation [C]// Proceedings of the 2007 POPL Conference, 2007, 42(1): 47-54.
- [11] Godefroid P, Levin M Y, Molnar D A. Automated whitebox fuzz testing [C]// Proceedings of the Network and Distributed Systems Security Symposium (NDSS), 2008, 8: 151-166.
- [12] Nielson F, Nielson H R, Hankin C. Principles of program analysis [M]. Berlin: Springer-Verlag, 1999: 211-282.
- [13] 李梦君, 李舟军, 陈火旺. 基于抽象解释理论的程序验证技术 [J]. 软件学报, 2008, 19(1): 17-26.
- [14] 姬孟洛, 王怀民, 李梦君, 等. 一种基于抽象解释和通用单调数据流框架的值范围分析方法[J]. 计算机研究与发展, 2006, 43(11): 2020-2026.
- [15] 姬孟洛, 李军, 王馨, 等. 一种基于抽象解释的 WCET 自动分析工具[J]. 计算机工程, 2006, 32(14): 54-56.
- [16] Cousot P, Cousot R. Abstract interpretation: a unified lattice model for static analysis of programs by construction or approximation of fixpoints [C]// Proceedings of the 4th ACM SIGACT-SIGPLAN symposium on Principles of programming languages. New York:ACM press, 1977: 238-252.
- [17] Cousot P, Cousot R. Static determination of dynamic properties of programs [C]//Proceedings of the second International Symposium on Programming. Dunod, Paris, 1976: 106-130.
- [18] Cousot P. Abstract interpretation based formal methods and future challenges [C]//Proceedings of Informatics-10 Years Back, 10 Years Ahead. Berlin: Springer, 2001: 138-156.
- [19] 王雅文, 宫云战, 肖庆, 等. 基于抽象解释的变量值范围分析及应用[J]. 电子学报, 2011, 39(2): 296-303.
- [20] 王雅文, 宫云战, 肖庆, 等. 扩展区间运算的变量值范围分析技术[J]. 北京邮电大学学报, 2009, 32(3): 36-41.
- [21] Burkill J C. Functions of intervals[J]. Proceedings of the London Mathematical Society, 1924, 2(1): 275-310.
- [22] Young R C. The algebra of many-valued quantities[J]. Mathematische Annalen, 1931, 104(1): 260-290.
- [23] Alefeld G, Herzberger J. Introduction to interval computation[M]. New York: Academic press, 1984.
- [24] Hansen E. Topics in interval analysis[M]. Oxford: Clarendon Press, 1969.
- [25] Moore R E, Cloud M J, Kearfott R B. Introduction to interval analysis[M]. Philadelphia: Society for Industrial and Applied Mathematics(SIAM), 2009.
- [26] Moore R E. Interval analysis[M]. New Jersey: Prentice-Hall, 1966.
- [27] Moore R E. Methods and applications of interval analysis[M]. Philadelphia: Society for Industrial and Applied Mathematics (SIAM), 1979.
- [28] Moore R E. Interval arithmetic and automatic error analysis in digital computing [R]. Stanford Univ Calif Applied Mathematics and Statistics Labs, 1962.
- [29] Mudur S P, Koparkar P A. Interval methods for processing geometric objects [J]. Computer Graphics and Applications, IEEE, 1984, 4(2): 7-17.
- [30] Maekawa T. Robust computational methods for shape interrogation[D]. Boston: Massachusetts Institute of Technology, 1993.
- [31] Enger W. Interval ray tracing—a divide and conquer strategy for realistic computer
