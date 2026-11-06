```
dtree = { best_feature: { } }

if feature_type[ best_feature_index ] == 0:               # 对离散型特征
    best_feature_values = dataset[ :, best_feature_index ]   # 最优划分特征的数据
    best_feature_set = list( set( best_feature_values ) )    # 统计该特征的取值集合
    sub_datasets = dataset_split( dataset, best_feature_index )  # 划分数据集

    for i in range( len( sub_datasets ) ):
        dtree[ best_feature ][ best_feature_set[ i ] ] = create_tree_c45(
            sub_datasets[ i ], feature_list, feature_type )  # 递归划分
else:                                                       # 对连续型特征
    sub_datasets = dataset_split_c45( dataset, best_feature_index,
        best_split_value )

    dtree[ best_feature ][ '<' + str( best_split_value ) ] = create_tree_c45(
        sub_datasets[0], feature_list, feature_type )      # 递归划分左子集
    dtree[ best_feature ][ '>=' + str( best_split_value ) ] = create_tree_c45(
        sub_datasets[1], feature_list, feature_type )      # 递归划分右子集

return dtree
```

# （5）创建主函数

下面我们使用一个数据集来测试 C4.5 决策树的构造过程。首先，我们创建一个名为 dataset 的数据集，包含了某些样本以及对应的特征和类别标签。然后定义了特征列表 feature_list，列出了数据集中所有特征的名称。接下来调用 create_tree_c45 函数，使用 C4.5 算法构建决策树，得到决策树的字典表示，赋值给 decision_tree 变量。最后，输出决策树字典 decision_tree。

# 代码 7-12 run_c45 函数

```
def run_c45():
    # 定义数据集
    dataset = np.array([
        ['蜡缩', '浊响', '清晰', '凹陷', '硬滑', 0.677, '是'],
        ['蜡缩', '沉闷', '清晰', '凹陷', '硬滑', 0.697, '是'],
        ['蜡缩', '浊响', '清晰', '凹陷', '硬滑', 0.797, '是'],
        ['蜡缩', '沉闷', '清晰', '凹陷', '硬滑', 0.897, '是'],
        ['蜡缩', '浊响', '清晰', '凹陷', '硬滑', 0.697, '是'],
        ['稍蜡', '浊响', '清晰', '稍凹', '软粘', 0.697, '是'],
        ['稍蜡', '浊响', '稍糊', '稍凹', '软粘', 0.857, '是'],
        ['稍蜡', '浊响', '清晰', '稍凹', '硬滑', 0.797, '是'],
        ['稍蜡', '沉闷', '稍糊', '稍凹', '硬滑', 0.697, '否'],
        ['硬挺', '清脆', '清晰', '平坦', '软粘', 0.347, '否'],
        ['硬挺', '清脆', '模糊', '平坦', '硬滑', 0.697, '否'],
        ['蜡缩', '浊响', '模糊', '平坦', '软粘', 0.317, '否'],
        ['稍蜡', '浊响', '稍糊', '凹陷', '硬滑', 0.697, '否'],
        ['稍蜡', '沉闷', '稍糊', '凹陷', '硬滑', 0.227, '否'],
        ['稍蜡', '浊响', '清晰', '稍凹', '软粘', 0.737, '否'],
        ['蜡缩', '浊响', '模糊', '平坦', '硬滑', 0.197, '否'],
    ])
```