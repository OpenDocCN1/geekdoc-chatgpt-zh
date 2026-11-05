映射矩阵。如下所示算法 1 是 Ullmann 算法的伪代码。在图论中，子图同构判定是一个经典的问题，除了上述算法，还有一些常见的算法，例如 VF 算法[108]、VF2 算法[109]、Nauty 算法[320] 等。感兴趣的读者可以参考文献 “A Performance Comparison of Five Algorithms for Graph Isomorphism”[160]，该文献对常用的五种子图同构算法进行了详细的比较。

# 算法 1 Ullmann 子图判定算法

```
输入： 图 A、图 B 的邻接矩阵 M_A、M_B
输出： 所有合法的映射矩阵集合 ANS
1: ANS={} ▷ 合法的映射矩阵集合
2: 创建矩阵 F, F_(i,j) = 1 当且仅当图 A 的顶点 i 和图 B 的顶点 j 具有相同的标签，并且图 A 中顶点 i 的度不大于图 B 中顶点 j 的度。
3: for F_(i,j) == 1 do
4:   if 图 A 中顶点 i 的某个邻接点 i' 在图 B 中没有 j 的邻接点与之对应 then
5:     重置 F_(i,j) 的值为 0
6:   end if
7: end for
8: if F 中某行或某列的元素全部为 0 then
9:   图 A 和图 B 没有子图同构，结束程序
10: end if
11: C_F={} ▷ 候选的 F 矩阵集合
12: for F_(i,j) == 1 do ▷ 枚举所有可能的合法 F 矩阵
13:   F_(i,j) = 0
14:   if F 的每一行和每一列都至多有一个 1 then
15:     F 加入到候选集合 C_F 中
16:   else
17:     递归地修改 F，并将满足条件的 F 加入到集合 C_F 中
18:   end if
19:   F_(i,j) = 1
20:   if F 的每一行和每一列都至多有一个 1 then
21:     F 加入到候选集合 C_F 中
22:   else
23:     递归地修改 F，并将满足条件的 F 加入到集合 C_F 中
24:   end if
25: end for
26: for F ∈ C_F do
27:   if F 满足公式 8.1 then
28:     将 F 加入到 ANS
29:   end if
30: end for
```