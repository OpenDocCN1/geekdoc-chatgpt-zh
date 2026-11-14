$$ AB = (ab_1 \cdots ab_k)^T = \begin{pmatrix} ab_{11} & \cdots & ab_{1m} \\ \vdots & \ddots & \vdots \\ ab_{k1} & \cdots & ab_{km} \end{pmatrix} \quad (7-2) $$

$ab_{ij} \in [0,1]^m, i=1, \cdots, k; j=1, \cdots, m$

作为新生成的抗体。式中，$ab_i$ 表示第 $i$ 个抗体，$ab_{ij}$ 表示第 $i$ 个抗体的第 $j$ 个向量。

# 7.2.3 抗原提呈

把抗原与抗体间的欧氏距离作为衡量两者之间匹配程度的一个指标。将抗原中的每个元素分别与抗体中的每个元素相匹配，对任意抗原 $ag_i$ 和任意抗体 $ab_j$，两者之间的距离 $d(i, j)$ 可通过下式计算：

$$ d(i,j) = \left[ \sum_{t=1}^m (ag_{it} - ab_{jt})^2 \right]^{1/2} \quad (7-3) $$

对任意抗体 $ab_i$ 和任意抗体 $ab_j$，两者之间的距离 $s(i, j)$ 可通过下式计算：

$$ s(i,j) = \left[ \sum_{t=1}^m (ag_{it} - ab_{jt})^2 \right]^{1/2} \quad (7-4) $$

对任意 $ab_j (ab_j \in AB, j=1, \cdots, k)$，所受到的来自抗原 $ag_i (ag_i \in AG, i=1, \cdots, I)$ 的激励 $sl(i, j)$ 为：

$$ sl(i,j) = \begin{cases} 1/ \sqrt{d(i,j)} & d(i,j) < T_x \\ 0 & \text{other} \end{cases} \quad (7-5) $$ 
$i = 1, 2, \cdots, I; j = 1, 2, \cdots, k$

式(7-5)中：

$$ T_x = k_x \frac{\sum_{i=1}^I \sum_{j=1}^k d(i,j)}{\frac{I \times k}{2}} \quad (7-6) $$