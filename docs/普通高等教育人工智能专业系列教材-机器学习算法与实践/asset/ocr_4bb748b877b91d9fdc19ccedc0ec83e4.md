```
data_left = data[0:median, :]          # 获取左子树的数据
data_right = data[ median + 1:, :]        # 获取右子树的数据

if sample_number == 1:                    # 如果样本数为 1，返回当前节点
    return kdnode
elif sample_number == 2:                  # 如果样本数为 2
    kdnode.left_tree = build_tree(kdnode.left_tree, data_left,
                                  (index + 1) % feature_number, depth + 1)  # 构建左子树
    kdnode.left_tree.parent = kdnode       # 设置左子树的父节点为当前节点
    return kdnode
else:                                     # 如果样本数大于 2
    kdnode.left_tree = build_tree(kdnode.left_tree, data_left,
                                  (index + 1) % feature_number, depth + 1)   # 构建左子树
    kdnode.left_tree.parent = kdnode       # 设置左子树的父节点为当前节点
    kdnode.right_tree = build_tree(kdnode.right_tree, data_right,
                                   (index + 1) % feature_number, depth + 1)   # 构建右子树
    kdnode.right_tree.parent = kdnode      # 设置右子树的父节点为当前节点
    return kdnode
```

# （8）搜索 k-d 树

kd_search 函数是用于在 k-d 树中搜索距离最近的样本点。首先，定义全局变量 nearest_point 和 nearest_value，分别用于保存最近的样本点和最近的距离。然后，定义了一个内部函数 travel，用于递归遍历 k-d 树。在 travel 函数中，首先判断当前节点是否为空，如果不为空，则计算样本点的特征数 n 和划分特征的索引 axis。根据划分特征的值，选择左子树或右子树进行遍历。然后，计算样本点与当前节点的距离，并根据最近距离的大小更新最近的样本点和最近的距离。最后，判断当前维度上的距离是否小于等于最近距离，如果是，则根据划分特征的值选择左子树或右子树进行遍历。最后，在主函数中调用 travel 函数从根节点开始遍历 k-d 树，并返回最近的样本点。

# 代码 3-8 kd_search 函数

```
def kd_search(node, sample):
    """
    k-d 树搜索函数
    参数:
    node: 当前节点
    sample: 待搜索的样本点
    返回:
    nearest_point: 距离最近的样本点
    """
    global nearest_point
    global nearest_value
    nearest_point = None    # 初始化最近的样本点
    nearest_value = 0       # 初始化最近的距离

    def travel(node, depth=0):
        """
        递归遍历 k-d 树的函数
```