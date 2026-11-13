# 16.1.3 案例——使用决策树分类鸢尾花

在 Scikit-learn 包内置的 Iris 数据集是鸢尾花的数据，可以让训练模型使用花瓣和花萼来分类鸢尾花。

# 1. 探索鸢尾花数据集

Scikit-learn 内置数据集是 datasets 对象，Python 程序只需导入 datasets，就可以调用相关方法来加载数据集。例如，载入鸢尾花数据集（Python 程序：Ch16_1_3.py），如下所示：

```
from sklearn import datasets
```

```
iris = datasets.load_iris()
```

上述程序代码导入 datasets 对象后，调用 load_iris()函数加载鸢尾花数据集。在成功载入鸢尾花数据集后，因为是字典，所以可以先探索此数据集。首先输出字典的键值列表，如下所示：

```
print(iris.keys())
```

上述程序代码可以输出键值列表，其运行结果如下所示：

```
dict_keys(['data', 'target', 'target_names', 'DESCR', 'feature_names'])
```

上述程序代码中，data 是鸢尾花的特征数据，target 是分类（分为 Iris-Setosa、Iris-Versicolour 和 Iris-Virginica 3 类），feature_names 是特征名称，DESCR 是数据集描述。然后输出特征数据的形状，如下所示：

```
print(iris.data.shape)
```

上述程序代码输出数据有几行和几列，即形状。其运行结果 4 个字段共 150 项，如下所示：

```
(150, 4)
```

数据的字段名是 feature_names 键，如下所示：

```
print(iris.feature_names)
```

上述程序代码可以输出字段名列表，其运行结果如下所示：

```
['sepal length (cm)', 'sepal width (cm)', 'petal length (cm)', 'petal width (cm)']
```

上述列表是 data 的 4 个字段名，分别是花瓣（Petal）和花萼（Sepal）的长和宽，单位是厘米。可以使用 DESCR 输出数据集的描述，如下所示：

```
print(iris.DESCR)
```

上述程序代码输出数据集的完整描述、各字段数据说明和基本统计数据，如图 16-5 所示。