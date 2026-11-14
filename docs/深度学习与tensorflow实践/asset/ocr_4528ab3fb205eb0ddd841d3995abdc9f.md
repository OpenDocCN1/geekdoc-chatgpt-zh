```
X = wine.data
y = wine.target
```

如果对 Python 语法比较熟悉，那么上述语句还能合并为一行代码。

```
X, y = wine.data, wine.target
```

下面，我们就利用函数 train_test_split()分别把 X 和 y 分割为两个测试集合和训练集合。由于 X 和 y 都被分割为两部分，因此需要 4 个变量来分别接收它们。

```
In [16]:
01 from sklearn.model_selection import train_test_split
02 X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.3,
random_state = 0)
```

上述代码的第 01 行需要导入训练集合与测试集合的分割函数 train_test_split。第 02 行表示实施分割任务，将 X（第 1 个参数表示特征数据）和 y（第 2 个参数表示标签数据）分割为两部分，其中测试集合占 30%（第 3 个参数的值可以自定义，默认值为 0.25）。第 4 个参数表示配合抽取数据的随机种子，如果 random_state 取值为 0 或 None，那么每次生成的数据都是随机，抽样结果可能都不一样。如果 random_state 取值为某个整数值，那么每次抽取的数据都是相同的，这对需要重复验证的实验比较有用。

如前所述，train_test_split 函数同时返回 4 个值（实际上返回的是一个把这 4 个元素打包的元组），分别赋值给 X_train、X_test、y_train、y_test，这 4 个接收变量的名称自然可以不同，但它们的逻辑顺序一定要正确，它们依次分别为训练集合的特征数据、测试集合的特征数据、训练集合的标签数据和测试集合的标签数据。

# 四、构造多层感知机模型

在数据分割完成后，下面我们就可以构造多层感知机模型了。

```
In [17]:
01 from sklearn.neural_network import MLPClassifier #导入多层感知机分类器
02 #创建多层感知机分类器模型
03 model = MLPClassifier(solver = "lbfgs",hidden_layer_sizes=(100,))
```

在上述有效 Python 代码中，第 01 行导入多层感知机分类器（Multi-layer Perceptron Classifier，MLPClassifier），这里的多层通常不超过 7 层，即浅度神经网络。这个模型是由 sklearn 提供的，无须自己编写。

第 03 行创建一个多层感知机分类器模型的实例 model，该模型共有 20 多个参数，若不显式地给这些参数赋值，则通常会启用该参数的默认值。例