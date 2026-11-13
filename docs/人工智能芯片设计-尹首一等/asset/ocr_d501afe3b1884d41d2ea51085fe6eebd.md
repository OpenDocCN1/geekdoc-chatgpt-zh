CONV 阵列 \( A_h^l \times A_w^l \)
FC 阵列 \( A_h^l \times A_h^l \)
PE 阵列

未分配的 \( B_{total} - A_h^l \) Bank
FC缓存：给FC阵列分配 \( A_h^l \) Bank
①
Bank[0] Bank[1] ... Bank[30] Bank[31] Bank[32] Bank[32] ... Bank[46] Bank[47]
②
CONV缓存：\( B_{total} - A_h^l \) Bank合并为 \( \frac{K + (A_h^l - 1)S}{K(K - S)}\) Bank
FC缓存：\( A_h^l \) Bank
Bank[0] Bank[1] Bank[2] ... Bank[0] Bank[1] ... Bank[14] Bank[15]
(a) CONV阵列和FC阵列的Bank分配

1个PE的 \( 3 \times 3 \) 输入数据
输入特征映射
(b) 一个PE的 \( 3 \times 3 \) 输入数据

数据复用
16行PE的 \( 18 \times 3 \) 输入
(c) 16行PE的 \( 18 \times 3 \) 输入数据

数据复用 融合模式
\( 18 \times 1 \) 数据
(d) 融合数据模式中的 \( 18 \times 1 \) 输入数据

Cycle1 ... Cycle6
Bank[0] Bank[1] Bank[2]
CONV缓存：(3 Bank)
\( 18 \times 1 \) 输入数据
\( 18 \times 2 \) 重用数据

顺序输入
\( 3 \times 3 \) 输入数据
PE <-> PE
16行
超级PE <-> 超级PE
\( 16 \times 16 \) PE阵列
(e) 数据流示例

# 图 4.17 基于融合数据模式的存储划分