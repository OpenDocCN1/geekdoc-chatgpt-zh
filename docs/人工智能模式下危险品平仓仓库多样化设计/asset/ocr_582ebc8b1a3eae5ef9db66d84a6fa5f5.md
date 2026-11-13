\(\min v = \sum_{s=1}^{M_2} \sum_{s=1}^{M_1} x_{ij} \times \left( -w_1 \times \frac{d\left(p_{li}, p_{2j}\right)}{\max_{\substack{1<s<N_0 \\ 1<t<N_2}} d\left(p_{li}, p_{2j}\right)} + w_2 \times \frac{\left(d\left(p_{li}, T_1\right) + d\left(p_{2j}, T_2\right)\right)}{\max_{\substack{1<s<N_0 \\ 1<t<N_2}} \left(d\left(p_{li}, T_1\right) + d\left(p_{2j}, T_2\right)\right)} + \cdots, \right. \\
\left. + w_3 \times \left( 1 - \frac{abs\left(d\left(p_{li}, T_1\right) - d\left(p_{2j}, T_2\right)\right)}{\max \left(d\left(p_{li}, T_1\right) d\left(p_{1j}, T_2\right)\right)} \right) \right) \\
s.t. \sum_{i=1}^{M_1} x_{ij} = 1, \sum_{j=1}^{M_2} x_{ij} = 1, x_{ij} \in \{0,1\}, w_1, w_2, w_3 \in [0,1], w_1 + w_2 + w_3 = 1\)

阶段 2：建立 \( P_1 \) 和 \( P_2 \) 元素间的匹配。任取 \( p_{li} \in P_1 \)，在本阶段将从 \( P_2 \) 中寻找合适的入库货位与 \( p_{li} \) 匹配。记得到的匹配结果为 \( p_{2j} \in P_2 \)，则安排 \( F_1 \) 和 \( F_2 \) 分别对 \( p_{li} \) 与 \( p_{2j} \) 同时进行入库作业。为了实现危险品的同时出入库，记与 \( p_{li} \) 对应的出库货位为 \( q_{1s} \)，记与 \( p_{2j} \) 对应的出库货位为 \( q_{2t} \)，为了实现叉车运行线路的最短，\( d\left(p_{li}, q_{1s}\right) + d\left(p_{2j}, q_{2t}\right) \) 需尽可能小。记

\( d_{l1is} = d\left(T_1, p_{li}\right) + d\left(p_{li}, q_{1s}\right) + d\left(q_{1s}, T_3\right), \quad d_{2jt} = d\left(T_2, p_{2j}\right) + d\left(p_{2j}, q_{2t}\right) + d\left(q_{2t}, T_3\right) \)

为了给 \( F_1 \) 和 \( F_2 \) 协调作业的空间，\( d_{l1is} \) 与 \( d_{2jt} \) 的相似度应尽可能小。据此，建立 最优化模型

\(\min v = \left( \sum_{s=1}^{N_0} \sum_{j=1}^{M_2} \sum_{i=1}^{M_1} w_1 \times \frac{x_{i1s} \times d\left(p_{li}, q_s\right) + x_{2jp} \times d\left(p_{2j}, q_s\right)}{\max \left( \max_{\substack{1<s<M_0 \\ 1<t<N_0}} d\left(p_{li}, q_s\right), \max_{\substack{1<s<M_0 \\ 1<t<N_0}} d\left(p_{2j}, q_s\right) \right)} + x_{1is} \times x_{2jp} \times w_2 \times \left( 1 - \frac{abs\left(d_{l1is} - d_{2jt}\right)}{\max \left( d_{l1is}, d_{2jt} \right)} \right) \right) \\
s.t. \sum_{s=1}^{N_0} x_{i1s} = 1, \sum_{s=1}^{M_2} x_{2j} = 1, \sum_{j=1}^{M_1} \sum_{i=1}^{M_1} \left(x_{1is} + x_{2jp}\right) = 1, \ x_{1is} \in \{0,1\}, x_{2jp} \in \{0,1\}, w_1, w_2, w_3 \in [0,1], w_1 + w_2 = 1\)

求解模型（3.2），并记求得的匹配为

\( v_1 = \begin{pmatrix} p_{11} & p_{12} & \cdots & p_{1M_1} \\ q_{11} & q_{12} & \cdots & q_{1M_1} \end{pmatrix}, \quad v_2 = \begin{pmatrix} p_{21} & p_{22} & \cdots & p_{2M_2} \\ q_{21} & q_{22} & \cdots & q_{2M_2} \end{pmatrix}, \quad v_3 = \begin{pmatrix} p_{31} & p_{32} & \cdots & p_{3M_1} \\ p_{3j_1} & p_{3j_2} & \cdots & p_{3j_{M_2}} \end{pmatrix} \)

# 阶段 3：分别计算

\( d'_1 = \sum_{i=1}^{M_1} \left( d\left(T_1, p_{li}\right) + d\left(p_{li}, q_{1i}\right) + d\left(q_{1i}, T_3\right) + d\left(T_1, T_3\right) \right) \\
d'_2 = \sum_{j=1}^{M_2} \left( d\left(T_2, p_{2j}\right) + d\left(p_{2j}, q_{2j}\right) + d\left(q_{2j}, T_3\right) + d\left(T_1, T_3\right) \right) \)