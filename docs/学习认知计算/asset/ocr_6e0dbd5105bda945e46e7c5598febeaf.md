\(P(R_{e_1} \mid R_{e_2}) = \frac{\text{Count}((e_2, e_1) = (1,1)) + \lambda_p}{\sum_{r_1=0}^1 \text{Count}((e_2, e_1)=(1, r_1)) + \lambda_p}\)

\(P(R_{e_1} \mid R_{e_2}, W_{e_2}) = \frac{\sum_{r_2=0}^1 \text{Count}((e_2, e_1)=(r_2, 1)) + \lambda_p}{\sum_{r_2=0}^1 \sum_{r_1=0}^1 \text{Count}((e_2, e_1)=(r_2, r_1)) + \lambda_p}\)

同样，也可以估计 \(P(W_{e_2} \mid W_{e_1})\) 和 \(P(W_{e_2} \mid R_{e_1}, W_{e_1})\)。支持值定义为以下两个组成部分的总和。在这里，max 函数被用来保证支持值的非负。

\(\text{Sup}(e_1 \to e_2) = \max \left(0, \ln \frac{P(R_{e_1} \mid R_{e_2})}{P(R_{e_1} \mid R_{e_2}, W_{e_2})}\right) + \max \left(0, \ln \frac{P(W_{e_2} \mid W_{e_1})}{P(W_{e_2} \mid R_{e_1}, W_{e_1})}\right)\)

基于以上约束，本节提出了几种基于相似性规则的图结构。

+   (1) 基于知识的方法：生成一个密集连接的图，其中，如果两个不同的试题 \(e_1\) 和 \(e_2\) 有相同的知识，那么邻接矩阵元素 \(A_{i,j}\) 为 1；否则为 0。

+   (2) 余弦相似方法：通过两个试题的 BERT (bidirectional encoder representations from transformers) 嵌入的余弦相似度生成一个图，其中，如果两个不同试题 \(e_1\) 和 \(e_2\) 之间的相似度大于超参数 \(\omega\)，那么 \(A_{i,j}\) 为 1；否则为 0。

+   (3) 试题转移方法：生成邻接矩阵是转移概率矩阵的图，其中，如果 \(\frac{n_{i,j}}{\sum_{k=1}^{|E|} n_{i,k}} > \omega\)，那么 \(A_{i,j}\) 为 1；否则为 0。在这里 \(n_{i,j}\) 代表在回答了试题 \(i\) 之后，立即回答试题 \(j\) 的次数。

+   (4) 试题支持方法：通过贝叶斯统计推断生成图形，其中，如果 \(\text{Sup}(e_i, e_j) > \omega\)，那么 \(A_{i,j}\) 为 1；否则为 0。

# 2. 问题模式表征学习

首先，HGKT 构建了两个名为 \(\text{GNN}_\text{excer}\) 和 \(\text{GNN}_\text{sche}\) 的 GNN，其参数用于相应地更新试题嵌入和问题模式嵌入。前 \(k\) 层的节点特征矩阵是每个试题 \(F\) 和直接支持图 \(\mathcal{A}_e\) 的一个 one-hot 嵌入。在这里，\(\mathbf{H}_e\) 与 \(\mathbf{H}_s\) 对应于试题和问题模式的嵌入。注意 \(\mathbf{H}^0 = F\)。在第 \(k\) 层，如式 (6.102) 所示，利用池操作来直接支持图，以获得更小的间接支持图 \(\mathcal{A}_s\)。式 (6.103) 中引入的线性变换将试题表示聚合为相应的问题模式表示。最后，\(\text{GNN}_\text{sche}\) 更新问题模式的表示，并将该模式表示发送到 HGKT 的序列处理阶段。

\(\mathbf{H}_e^{(l+1)} = \text{GNN}_\text{excer}(\mathcal{A}_e, \mathbf{H}_e^{(l)}), l < k\)