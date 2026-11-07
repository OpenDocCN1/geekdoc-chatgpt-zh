```
from sklearn.model_selection import train_test_split
# 红酒数据集
wine = load_wine()
# 把数据集拆分为训练集和数据集
X_train, X_test, y_train, y_test = train_test_split(wine.data,
                                                    wine.target,
                                                    random_state=62)

print(X_train.shape, X_test.shape)
# 设置神经网络的参数
# MLP 的隐藏层为 2 个，每层有 100 个节点，最大迭代数为 400
# 指定 random_state 的数值为 62，为了重复使用模型，其训练的结果都是一致的
mlp = MLPClassifier(hidden_layer_sizes=[100,100],max_iter=400,
                    random_state=62)

# 拟合数据训练模型
mlp.fit(X_train, y_train)
# 输出模型得分
print('数据没有经过预处理模型得分:{:.2f}'.format(mlp.score(X_test, y_test)))
from sklearn.preprocessing import MinMaxScaler
scaler = MinMaxScaler()
scaler.fit(X_train)
X_train_pp = scaler.transform(X_train)
X_test_pp = scaler.transform(X_test)
mlp.fit(X_train_pp, y_train)
print('数据预处理后的模型得分:{:.2f}'.format(mlp.score(X_test_pp,y_test)))
MinMaxScaler(feature_range=(0, 1), copy=True)
MaxAbsScaler(copy=True)
```

运行代码，结果如图 7-9 所示，训练集样本数目为 133，而测试集中样本数量为 45。我们用训练数据集来训练一个 MLP 多层神经网络（后面章节会详细介绍神经网络），在没有经过预处理的情况下，模型的得分只有 0.24。当对数据集进行预处理后，模型的得分大幅提高，直接提升到了 1.00。

![](bbox=[0.68, 0.56, 0.93, 0.64])

# 7.3 常见的模型评估指标

“没有测量，就没有科学。”这是科学家门捷列夫的名言。在计算机科学中，特别是在机器学习领域，对模型的测量和评估同样至关重要。只有选择与问题相匹配的评估方法，才能够快速发现在模型选择和训练过程中可能出现的问题，迭代地对模型进行优化。本节将总结机器学习、深度学习中最常见的模型评估指标，其中包括：

+   - Confusion Matrix（混淆矩阵）。
+   - Precision。