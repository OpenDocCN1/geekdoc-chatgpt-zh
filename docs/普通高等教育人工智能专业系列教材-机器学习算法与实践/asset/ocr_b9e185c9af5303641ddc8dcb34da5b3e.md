$(j^*,p^*) = \arg\max_{j,p}\left[\sum_{x_i<p}(y_i - c_{jp}^{(1)})^2 + \sum_{x_i\ge p}(y_i - c_{jp}^{(2)})^2\right] \qquad (7-15)$

需要注意的是，CART算法中特征在每次划分后是可重用的，因为同一个特征在不同节点划分时选择的分割点是不同的。

# 7.4.3 代码实现

本节主要给出 CART 回归树的实现。CART 算法的流程与前文介绍的 ID3 算法和 C4.5 算法基本一致，仅在划分特征选择的评价标准上有些差异，因此不再赘述。在算法实现方面，CART 分类树与 C4.5 算法仅在基尼系数的计算部分有所不同，因此仅给出此部分函数的实现，完整代码可以通过扫描右侧二维码获取。

(1) 计算 CART 分类树的基尼系数

代码 7-13 用于计算基尼系数。首先，初始化总基尼系数为 0。然后，遍历每个子数据集，对于每个子数据集，获取其类别标记集合，并以字典形式存储每种标记的数量。接下来，对于每个标记数量，根据基尼系数的计算公式，计算子数据集的基尼系数。最后，将每个子数据集的基尼系数乘以子数据集占完整数据集的比例，加到总基尼系数上。最终返回总基尼系数。

代码 7-13 cal_gini 函数

```
def cal_gini(dataset, sub_datasets):
    """
    计算基尼系数。
    参数：
    - dataset：完整数据集。
    - sub_datasets：子数据集列表，每个子数据集是从完整数据集中划分得到的。
    返回：
        基尼系数。
    """
    total_gini = 0.0
    for sub_dataset in sub_datasets:
        # 遍历每个子集，计算基尼系数
        labels = sub_dataset[:, -1]
        # 获取当前数据集的类别标记集合
        label_count = {}
        # 以字典存储每种标记的数量
        for i in range(len(labels)):
            # 遍历标记集合统计标记数量
            label = labels[i]
            label_count[label] = label_count.get(label, 0) + 1
        gini = 1.0
        for k, v in label_count.items():
            # 遍历标记数量集合计算基尼系数
            gini -= float(v / len(labels)) * float(v / len(labels))
        total_gini += len(sub_dataset) / len(dataset) * gini
    return total_gini
```