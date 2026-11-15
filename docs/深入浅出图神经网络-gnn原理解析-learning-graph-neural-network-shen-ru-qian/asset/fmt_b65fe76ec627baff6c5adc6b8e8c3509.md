
### 7.5 GraphSAGE代码实现

本节我们通过代码来介绍`GraphSAGE`以加深读者对相关知识的理解。如7.1节所介绍的，`GraphSAGE`包括两个方面，一是对于邻居的采样；二是对邻居的聚合操作。

#### 邻居采样方法

首先来看下对邻居的采样方法，为了实现更高效地采样，可以将节点及其邻居存放在一起，即维护一个节点与其邻居对应关系的表。我们可以通过两个函数`sampling()`和`multihop_sampling()`来实现采样的具体操作。其中`sampling()`是进行一阶采样，根据源节点采样指定数量的邻居节点，`multihop_sampling()`则是利用`sampling()`实现多阶采样的功能。如代码清单7-1所示：

#### 代码清单7-1 对邻居节点进行多阶采样

```python
def sampling(src_nodes, sample_num, neighbor_table):
    """根据源节点采样指定数量的邻居节点，注意使用的是有放回的采样；
    某个节点的邻居节点数量少于采样数量时，采样结果出现重复的节点
    
    Arguments:
        src_nodes {list, ndarray} -- 源节点列表
        sample_num {int} -- 需要采样的节点数
        neighbor_table {dict} -- 节点到其邻居节点的映射表
    
    Returns:
        ndarray -- 采样结果构成的列表
    """
    results = []
    for sid in src_nodes:
        # 从节点的邻居中进行有放回的采样
        res = np.random.choice(neighbor_table[sid], size=(sample_num, ))
        results.append(res)
    return np.asarray(results).flatten()


def multihop_sampling(src_nodes, sample_nums, neighbor_table):
    """根据源节点进行多阶采样
    
    Arguments:
        src_nodes {list, np.ndarray} -- 源节点id
        sample_nums {list of int} -- 每一阶需要采样的个数
        neighbor_table {dict} -- 节点到其邻居节点的映射
    
    Returns:
        [list of ndarray] -- 每一阶采样的结果
    """
    sampling_result = [src_nodes]
    for k, hopk_num in enumerate(sample_nums):
        hopk_result = sampling(sampling_result[k], hopk_num, neighbor_table)
        sampling_result.append(hopk_result)
    return sampling_result
```

这样采样得到的结果仅是节点的ID，还需要根据节点ID去查询每个节点的特征，以进行聚合操作更新特征。

#### 邻居聚合操作

下面根据式（7.1）—式（7.2）来实现邻居的聚合操作，计算的过程定义在`forward()`函数中，输入`neighbor_feature`表示需要聚合的邻居节点的特征，它的维度为`N[src]`×`N[neighbor]`×`D[in]`，其中`N[src]`表示源节点的数量，`N[neighbor]`表示邻居节点的数量，`D[in]`表示输入的特征维度。将这些邻居节点的特征经过一个线性变换得到隐层特征，这样就可以沿着第1个维度进行聚合操作了，包括求和、均值和最大值，得到维度为`N[src]`×`D[in]`的输出。如代码清单7-2所示：

#### 代码清单7-2 邻居聚合

```python
class NeighborAggregator(nn.Module):
    def __init__(self, input_dim, output_dim, 
                 use_bias=False, aggr_method="mean"):
        """邻居聚合方式实现
        
        Arguments:
        ----------
            input_dim {int} -- 输入特征的维度
            output_dim {int} -- 输出特征的维度
        
        Keyword Arguments:
        -----------------
            use_bias {bool} -- 是否使用偏置 (default: {False})
            aggr_method {string} -- 聚合方式 (default: {mean})
        """
        super(NeighborAggregator, self).__init__()
        self.input_dim = input_dim
        self.output_dim = output_dim
        self.use_bias = use_bias
        self.aggr_method = aggr_method
        self.weight = nn.Parameter(torch.Tensor(input_dim, output_dim))
        if self.use_bias:
            self.bias = nn.Parameter(torch.Tensor(self.output_dim))
        self.reset_parameters()
    
    def reset_parameters(self):
        init.kaiming_uniform_(self.weight)
        if self.use_bias:
            init.zeros_(self.bias)

    def forward(self, neighbor_feature):
        if self.aggr_method == "mean":
            aggr_neighbor = neighbor_feature.mean(dim=1)
        elif self.aggr_method == "sum":
            aggr_neighbor = neighbor_feature.sum(dim=1)
        elif self.aggr_method == "max":
            aggr_neighbor = neighbor_feature.max(dim=1)
        else:
            raise ValueError("Unknown aggr type, expected sum, max, or mean, but got {}"
                             .format(self.aggr_method))
        
        neighbor_hidden = torch.matmul(aggr_neighbor, self.weight)
        if self.use_bias:
            neighbor_hidden += self.bias

        return neighbor_hidden
```

#### 节点特征更新

基于邻居聚合的结果对中心节点的特征进行更新。更新的方式是将邻居节点聚合的特征与经过线性变换的中心节点的特征进行求和或者级联，再经过一个激活函数，得到更新后的特征。如代码清单7-3所示：

#### 代码清单7-3 SageGCN定义

```python
class SageGCN(nn.Module):
    def __init__(self, input_dim, hidden_dim, 
                 activation=F.relu,
                 aggr_neighbor_method="mean", 
                 aggr_hidden_method="sum"):
        super(SageGCN, self).__init__()
        assert aggr_neighbor_method in ["mean", "sum", "max"]
        assert aggr_hidden_method in ["sum", "concat"]
        self.aggr_neighbor = aggr_neighbor_method
        self.aggr_hidden = aggr_hidden_method
        self.activation = activation
        self.aggregator = NeighborAggregator(input_dim, hidden_dim,
                                            aggr_method=aggr_neighbor_method)
        self.weight = nn.Parameter(torch.Tensor(input_dim, hidden_dim))
    
    def reset_parameters(self):
        init.kaiming_uniform_(self.weight)

    def forward(self, src_node_features, neighbor_node_features):
        neighbor_hidden = self.aggregator(neighbor_node_features)
        self_hidden = torch.matmul(src_node_features, self.weight)
        
        if self.aggr_hidden == "sum":
            hidden = self_hidden + neighbor_hidden
        elif self.aggr_hidden == "concat":
            hidden = torch.cat([self_hidden, neighbor_hidden], dim=1)
        else:
            raise ValueError("Expected sum or concat, got {}"
                             .format(self.aggr_hidden))
        if self.activation:
            return self.activation(hidden)
        else:
            return hidden
```

#### GraphSage模型示例

基于前面定义的采样和节点特征更新方式，就可以实现7.1.3节介绍的计算节点嵌入的方法。下面定义了一个两层的模型，隐藏层节点数为64，假设每阶采样节点数都为10，那么计算中心节点的输出可以通过以下代码实现。其中前向传播时传入的参数`node_features_list`是一个列表，其中第0个元素表示源节点的特征，其后的元素表示每阶采样得到的节点特征。如代码清单7-4所示：

#### 代码清单7-4 GraphSage模型示例

```python
class GraphSage(nn.Module):
    def __init__(self, input_dim, hidden_dim=[64, 64], 
                 num_neighbors_list=[10, 10]):
        super(GraphSage, self).__init__()
        self.input_dim = input_dim
        self.num_neighbors_list = num_neighbors_list
        self.num_layers = len(num_neighbors_list)
        self.gcn = []
        self.gcn.append(SageGCN(input_dim, hidden_dim[0]))
        self.gcn.append(SageGCN(hidden_dim[0], hidden_dim[1], activation=None))
    
    def forward(self, node_features_list):
        hidden = node_features_list
        for l in range(self.num_layers):
            next_hidden = []
            gcn = self.gcn[l]
            for hop in range(self.num_layers - l):
                src_node_features = hidden[hop]
                src_node_num = len(src_node_features)
                neighbor_node_features = hidden[hop + 1] \
                    .view(src_node_num, self.num_neighbors_list[hop], -1)
                h = gcn(src_node_features, neighbor_node_features)
                next_hidden.append(h)
            hidden = next_hidden
        return hidden[0]
```

## 7.6 参考文献

[[1]](../Text/chapter73.xhtml#g1)　Hamilton W，Ying Z，Leskovec J.Inductive representation learning on large graphs[C]//Advances in Neural Information Processing Systems.2017：1024-1034.

[[2]](../Text/chapter74.xhtml#g2)　Chen J，Ma T，Xiao C.Fastgcn：fast learning with graph convolutional networks via importance sampling[J].arXiv preprint arXiv：1801.10247，2018.

[[3]](../Text/chapter74.xhtml#g3)　Huang W，Zhang T，Rong Y，et al.Adaptive sampling towards fast graph representation learning[C]//Advances in Neural Information Processing Systems.2018：4558-4567.

[[4]](../Text/chapter74.xhtml#g4)　Chen J，Zhu J，Song L.Stochastic training of graph convolutional networks with variance reduction[J].arXiv preprint arXiv：1710.10568，2017.

[[5]](../Text/chapter76.xhtml#g5)　Ying R，He R，Chen K，et al.Graph convolutional neural networks for web-scale recommender systems[C]//Proceedings of the 24th ACM SIGKDD International Conference on Knowledge Discovery&Data Mining.ACM，2018：974-983.

[[6]](../Text/chapter77.xhtml#g6)　VeličkovićP，Cucurull G，Casanova A，et al.Graph attention networks[J].arXiv preprint arXiv：1710.10903，2017.

[[7]](../Text/chapter83.xhtml#g7)　Schlichtkrull M，Kipf T N，Bloem P，et al.Modeling relational data with graph convolutional networks[C]//European Semantic Web Conference.Springer，Cham，2018：593-607.

[[8]](../Text/chapter85.xhtml#g8)　Gilmer J，Schoenholz S S，Riley P F，et al.Neural message passing for quantum chemistry[C]//Proceedings of the 34th International Conference on Machine Learning-Volume 70.JMLR.org，2017：1263-1272.
