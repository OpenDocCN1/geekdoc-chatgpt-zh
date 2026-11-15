[PRE0]

#### 中心节点特征更新机制

依据邻居聚合的输出结果，中心节点的特征向量得以刷新。该更新策略将聚合后的邻居特征与中心节点经线性变换后的特征进行求和或拼接，随后通过激活函数处理，最终产出更新后的特征表示。具体实现请参考代码清单 7-3：

[PRE1]

#### GraphSage 模型实战示例

结合前文确立的采样策略与节点特征更新逻辑，第 7.1.3 节介绍的节点嵌入计算流程可落地为可执行代码。此处构建一个双层架构，隐藏层维度设为 64，假设每跳采样 10 个邻居，中心节点的输出计算如下所示。前向传播时传入的参数 `node_features_list` 为一个列表：索引 0 处存放源节点特征，后续位置依次存放各跳采样得到的节点特征。详见代码清单 7-4：

[PRE2]

## 7.6 参考文献

[[1]](../Text/chapter73.xhtml#g1)　Hamilton W，Ying Z，Leskovec J.Inductive representation learning on large graphs[C]//Advances in Neural Information Processing Systems.2017：1024-1034.

[[2]](../Text/chapter74.xhtml#g2)　Chen J，Ma T，Xiao C.Fastgcn：fast learning with graph convolutional networks via importance sampling[J].arXiv preprint arXiv：1801.10247，2018.

[[3]](../Text/chapter74.xhtml#g3)　Huang W，Zhang T，Rong Y，et al.Adaptive sampling towards fast graph representation learning[C]//Advances in Neural Information Processing Systems.2018：4558-4567.

[[4]](../Text/chapter74.xhtml#g4)　Chen J，Zhu J，Song L.Stochastic training of graph convolutional networks with variance reduction[J].arXiv preprint arXiv：1710.10568，2017.

[[5]](../Text/chapter76.xhtml#g5)　Ying R，He R，Chen K，et al.Graph convolutional neural networks for web-scale recommender systems[C]//Proceedings of the 24th ACM SIGKDD International Conference on Knowledge Discovery&Data Mining.ACM，2018：974-983.

[[6]](../Text/chapter77.xhtml#g6)　VeličkovićP，Cucurull G，Casanova A，et al.Graph attention networks[J].arXiv preprint arXiv：1710.10903，2017.

[[7]](../Text/chapter83.xhtml#g7)　Schlichtkrull M，Kipf T N，Bloem P，et al.Modeling relational data with graph convolutional networks[C]//European Semantic Web Conference.Springer，Cham，2018：593-607.

[[8]](../Text/chapter85.xhtml#g8)　Gilmer J，Schoenholz S S，Riley P F，et al.Neural message passing for quantum chemistry[C]//Proceedings of the 34th International Conference on Machine Learning-Volume 70.JMLR.org，2017：1263-1272.

# 第 8 章 图分类任务解析

图分类是一项关键的图级学习任务。与节点级任务不同，它要求关注图数据的整体信息，涵盖拓扑结构特征与节点属性特征两大维度。给定一组图及其对应标签，图分类旨在学习一个从图到标签的映射模型；模型的核心竞争力在于能否通过学习获得高质量的全图表征向量。