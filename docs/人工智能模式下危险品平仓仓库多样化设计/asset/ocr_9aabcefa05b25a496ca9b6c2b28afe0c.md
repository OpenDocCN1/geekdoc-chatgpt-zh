车 $F_1$ 和 $F_2$ 在仓库内行驶距离的期望，即

$A_v = A \times m^2 + B \times n^2 + C \times m \times n + D \times m + E \times n + F$ （4.1）

其中，参数 $A, B, C, D, E, F$ 由拟合得到。

最后，我们给出仓库的效用评价模型。

在仓储作业过程中，效用评价函数由叉车行驶所消耗的燃油及单次出入库作业的时间两个因素决定。经过积分分析可得，在设置规则和不设置规则两种环境下，在双叉车三集卡模型中的效用评价函数分别为

$F_s(m,n) = w_1 \times (c_s + c_f) + w_2 \times \left( \frac{d_s}{v_s} + \frac{d_{out}}{v_f} \right)$

以及

$F_{ns}(m,n) = w_1 \times (c_{ns} + c_f) + w_2 \times \left( \frac{d_{ns}}{v_{ns}} + \frac{d_{out}}{v_f} \right)$

将两式标准化后可得

$F_s(m,n) = \left[ w_1 \times \left( \frac{c_s + c_f}{c_s + c_f + c_{ns} + c_f} \right) + w_2 \times \left( \frac{d_s v_s^{-1}}{d_s v_s^{-1} + d_{ns} v_{ns}^{-1}} + \frac{d_{out} v_f^{-1}}{d_{out} v_f^{-1} + d_{out} v_{ns}^{-1}} \right) \right]^{-1}$ （4.2）

$F_{ns}(m,n) = \left[ w_1 \times \left( \frac{c_{ns} + c_f}{c_s + c_f + c_{ns} + c_f} \right) + w_2 \times \left( \frac{d_{ns} v_{ns}^{-1}}{d_s v_s^{-1} + d_{ns} v_{ns}^{-1}} + \frac{d_{out} v_f^{-1}}{d_{out} v_f^{-1} + d_{out} v_f^{-1}} \right) \right]^{-1}$ （4.3）

其中，$w_1, w_2 \in [0,1], w_1 + w_2 = 1$; $d_s, d_{ns}$ 表示关于入库货位数 $m$ 与出库货位数 $n$ 的函数; $F_s$ 表示设置环岛单向行车规则时的效用; $F_{ns}$ 表示允许环岛区域双向行车时的效用。

在已知入库货位数 $m$ 与出库货位数 $n$ 时，随机选取入库货位和出库货位的位置，并在设置环岛行车规则与不设环岛行车规则两种前提下，通过叉车库内运行的算法控制模型，分别计算出每种情况下叉车在仓库内行驶的最短距离 $d_s$ 与 $d_{ns}$，再通过入库集卡与出库集卡的位置，计算出叉车在仓库内及仓库外行驶的最短距离 $d_{in}$ 和 $d_{out}$。其计算公式分别为

$d_{in} = \min \left[ d(P_{i_1}, P_{i_1}) + d(P_{i_2}, P_{i_2}) + d(P_{i_1}, P_{j_1}) + d(P_{i_2}, P_{j_2}) + d(q_{j_1}, P_{i_1}) + d(q_{j_2}, P_{i_1}) \right]$

$d_{out} = \min \left[ d(P_{i_1}, P_{i_1}) + d(P_{i_2}, P_{i_1}) \right]$

其中，$i_1, i_2 \in \{1, 2, \cdots, m\}, j_1, j_2 \in \{1, 2, \cdots, n\}$。

与此同时，叉车行驶的速度也会影响到叉车的油耗，且叉车在仓库内行驶时，速度越慢油耗越多。由于在设置环岛单向行车规则时，叉车避免了会车时的减速，