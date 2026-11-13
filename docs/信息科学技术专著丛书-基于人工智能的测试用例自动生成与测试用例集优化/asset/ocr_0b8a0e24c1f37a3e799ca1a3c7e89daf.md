# 2. BFS-BB

首先将本书中出现的一些算法和对它们的描述列在表 3-1 中。接下来是对算法及其伪代码的介绍。

# 表 3-1 本书中的一些算法及其描述

| 名字 | 描述 |
|---|---|
| BFS-BB | 本书提出的全局搜索算法 |
| Dynamic Variable Ordering (DVO) | 对 FV 进行排序并返回待赋值的下一个变量 |
| Path Tendency Calculation (PTC) | 计算所有路径变量性质 |
| Initial Domain Calculation (IDC) | 根据 PTC 的返回值，计算为每个变量选取初始值的区间 |
| Iterative Interval Arithmetic(IIC) | 优化迭代的区间运算，用在 BFS-BB 所有涉及区间运算的部分 |
| Hill Climbing (HC) | 对于每个变量赋值进行判断的局部搜索算法，调用区间运算判断为某个变量赋的值是否会产生矛盾，并计算相应的目标函数值 |

# 算法 3-1 BFS-BB

```
输入 p: 待覆盖路径
输出 result{Variable -> Value}: 覆盖 p 的测试用例
Begin
阶段 1. 预处理
1: result←-null;
2: 路径约束提取；
3: 求相关变量集和相关变量闭包；
4: 变量级别确定；
5: 调用算法 3-2 Irrelevant Variable Removal；
6: 区间初始化，并调用 IIA 进行判断；
7: 调用算法 3-3 Dynamic Variable Ordering；
8: 调用算法 3-4 Path Tendency Calculation；
9: 调用算法 3-5 Initial Domain Calculation；
10: V11←select(D11);
11: initial state←(null, x1, D11, V11, active);
12: Scur←initial state;
阶段 2. 状态空间搜索
13: while(xi ≠ null)
14: 调用算法 3-6 BB-HC;
15: if (Scur = (Pre, xj, Dj, Vj, inactive))
16: Pre←Scur;
17: Scur←(Pre, xij, Dij, Vij, active);
```