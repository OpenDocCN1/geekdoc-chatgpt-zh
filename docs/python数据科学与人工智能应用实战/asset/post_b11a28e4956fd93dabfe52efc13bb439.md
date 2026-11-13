
所示：

```
print("准确度:", dtree.score(XTest, yTest))
```

上述程序代码调用 score() 函数计算预测模型的准确度，其执行结果如下所示：

```
准确度： 0.96
```

然后，输出测试数据集的原始值和预测值，如下所示：

```
print(dtree.predict(XTest))
print(yTest.values)
```

上述程序代码中，第 1 行是测试数据集的预测分类，第 2 行是原始分类。其运行结果如下所示：

```
[0 1 1 0 2 1 2 0 0 2 1 0 2 1 1 0 1 1 0 0 1 2 0 2 1 0 0 1 2 1 2 1 2 2 0 1
0 1 2 2 0 1 2 1 2 0 0 0 1]
[0 1 1 0 2 1 2 0 0 2 1 0 2 1 1 0 1 1 0 0 1 1 1 0 2 1 0 0 1 2 1 2 1 2 2 0 1
0 1 2 2 0 2 2 1 2 0 0 0 1]
```

上述列表的 0、1、2 代表 3 种类别，仔细检查可以找出预测分类和原始分类的不同之处。

Python 程序：Ch16_1_3b.py 建立的 tree2.dot 文件是使用 GraphViz 绘制的决策树图形，如图 16-6 所示。

![](img/26e9944e14d74e7e03dda869acb3f1a8_444_0.png)

图 16-6

## 16.2 K 近邻算法

简单地说，分类预测就是使用已知的分类数据建立预测模型来预测未知数据所属的类别，除了使用第 15.4 节的 Logistic 回归、第 16.1 节的决策树外，另一个常见的分类算法是 K 近邻算法。

### 16.2.1 认识 K 近邻算法

K 近邻算法是使用 K 个最接近目标数据的数据来预测目标数据所属的类别。

通过一个简单实例的计算过程来说明 K 近邻算法。例如，某家面纸厂商使用问卷调查客户对面纸的好恶，问卷共使用 2 个属性（耐酸性、强度）判断面纸的好或坏，如图 16-7 所示。

厂商在今年开发出一种面纸的新产品，其实验室测试结果的耐酸性是 3，强度是 7。在 K = 3 的情况下，请使用 K 近邻算法判断新产品是好面纸还是坏面纸，其步骤如下所示。

- Step 1 计算新产品与所有数据集的距离。需要计算新产品与所有数据集其他面纸产品的距离，其公式是各属性与新产品属性差的平方和。例如，编号 1 是 (7, 7)，新产品是 (3, 7)，各属性差的平方和是 (7-3)² + (7-7)² = 4² = 16，如图 16-8 所示。

| 编号 | 耐酸性 | 强度 | 分类 |
| :--- | :--- | :--- | :--- |
| 1 | 7 | 7 | 坏 |
| 2 | 7 | 4 | 坏 |
| 3 | 3 | 4 | 好 |
| 4 | 1 | 4 | 好 |

| 编号 | 耐酸性 | 强度 | 分类 | 距离 (3, 7) |
| :--- | :--- | :--- | :--- | :--- |
| 1 | 7 | 7 | 坏 | (7-3)² + (7-7)² = 16 |
| 2 | 7 | 4 | 坏 | (7-3)² + (4-7)² = 25 |
| 3 | 3 | 4 | 好 | (3-3)² + (4-7)² = 9 |
| 4 | 1 | 4 | 好 | (1-3)² + (4-7)² = 13 |

图 16-7

图 16-8

- Step 2 排序找出最近的 K 笔距离。在计算出距离后，因为 K=3，所以可以找出距离最近 3 笔的编号是 1、3 和 4，距离分别是 16、9 和 13，距离 25 被排除，如图 16-9 所示。

| 编号 | 耐酸性 | 强度 | 分类 | 距离 (3, 7) |
| :--- | :--- | :--- | :--- | :--- |
| 1 | 7 | 7 | 坏 | (7-3)² + (7-7)² = 16 |
| 2 | 7 | 4 | 坏 | (7-3)² + (4-7)² = 25 |
| 3 | 3 | 4 | 好 | (3-3)² + (4-7)² = 9 |
| 4 | 1 | 4 | 好 | (1-3)² + (4-7)² = 13 |

图 16-9

- Step 3 新产品分类是最近 K 笔距离的多数分类。现在，知道距离最近 3 笔编号是 1、3 和 4，其分类分别是坏、好和好，好比较多，所以新产品的分类是“好”，这就是 K 近邻算法。

### 案例：分类面纸是好或坏

在了解 K 近邻算法的运算过程后，可以自行建立 Python 程序来实现 K 近邻算法。这里直接使用 Scikit-learn 包的 K 近邻分类器（Python 程序：Ch16_2_1.py），如下所示：

```
import pandas as pd
import numpy as np
from sklearn import neighbors

X = pd.DataFrame({
    "durability": [7, 7, 3, 1],
    "strength": [7, 4, 4, 4]
})

y = np.array([0, 0, 1, 1])
k = 3
```

上述程序代码导入相关包后，建立训练数据 X 和 y，其数据就是之前的范例数据，变量 k 即 K=3。然后，可以使用 K 近邻分类器来进行分类，如下所示：

```
knn = neighbors.KNeighborsClassifier(n_neighbors=k)
knn.fit(X, y)

# 预测新产品[3, 7]的分类，1 为好，0 为坏
new_tissue = pd.DataFrame(np.array([[3, 7]]))
pred = knn.predict(new_tissue)
print(pred)
```

上述程序代码建立 KNeighborsClassifier 对象，参数是 K 值；然后调用 fit() 函数训练模型，完成后使用新产品数据进行预测分类，其运行结果的分类如下所示：

```
[1]
```

上述预测结果中，1 就是好，0 就是坏。

### 16.2.2 案例——使用 K 近邻算法分类鸢尾花

本节使用 K 近邻算法来分类鸢尾花，使用的是花瓣和花萼的尺寸。在实际分类之前，先用可视化方式来探索鸢尾花数据集。

### 1. 使用散布图探索鸢尾花数据集

鸢尾花 Iris 数据集的内容是花瓣和花萼尺寸的长和宽，在第 16.1.3 节已经探索过数据集，这节将使用可视化方式来探索数据，即输出花瓣和花萼长宽的散布图，并且套上已知分类的色彩（Ch16_2_2.py），如下所示：

```
import pandas as pd
import numpy as np
from sklearn import datasets
import matplotlib.pyplot as plt
iris = datasets.load_iris()
X = pd.DataFrame(iris.data, columns=iris.feature_names)
X.columns = ["sepal_length", "sepal_width", "petal_length", "petal_width"]
target = pd.DataFrame(iris.target, columns=["target"])
y = target["target"]
```

上述程序代码加载数据集后，建立 DataFrame 对象且更改字段名。然后建立 2 个子图，调用 subplots_adjust() 函数调整间距，如下所示：

```
colmap = np.array(["r", "g", "y"])
plt.figure(figsize=(10, 5))
plt.subplot(1, 2, 1)
plt.subplots_adjust(hspace = .5)
plt.scatter(X["sepal_length"], X["sepal_width"], color=colmap[y])
plt.xlabel("Sepal Length")
plt.ylabel("Sepal Width")
plt.subplot(1, 2, 2)
plt.scatter(X["petal_length"], X["petal_width"], color=colmap[y])
plt.xlabel("Petal Length")
plt.ylabel("Petal Width")
plt.show()
```

上述程序代码分别绘制以花萼和花瓣的长和宽为坐标(x, y) 的散布图，其运行结果如图 16-10 所示。

![](img/26e9944e14d74e7e03dda869acb3f1a8_447_0.png)

图 16-10

图 16-10 所示的散布图已经显示了一些分类的线索。在图 16-10（b）中可以看出红色点的 Iris-Setosa 花瓣比较小，绿色点的 Iris-Versicolour 是中等尺寸，最大的是黄色点的 Iris-Virginica。Iris-Setosa、Iris-Versicolour 和 Iris-Virginica 是 3 种鸢尾花的分类。

在图 16-10（a）中可以看出 Setosa 的花萼（Sepal）明显比另外两类的尺寸较短且较宽。

### 2. 建立 K 近邻模型分类鸢尾花

现在，可以使用 K 近邻算法分类 Scikit-learn 内置的鸢尾花 Iris 数据集（Python 程序：Ch16_2_2a.py），如下所示：

```
import pandas as pd
from sklearn import datasets
from sklearn import neighbors
from sklearn.model_selection import train_test_split
```

```
iris = datasets.load_iris()
X = pd.DataFrame(iris.data, columns=iris.feature_names)
X.columns = ["sepal_length", "sepal_width", "petal_length", "petal_width"]
target = pd.DataFrame(iris.target, columns=["target"])
y = target["target"]
```

上述程序代码导入相关包模块后，调用 load_iris() 函数加载鸢尾花数据集；然后建立 DataFrame 对象，DataFrame 对象 X 有 4 个字段，变量 y 是字段 target。将数据集切割成 67% 的训练数据集和 33% 的测试数据集后，开始训练模型，如下所示：

```
XTrain, XTest, yTrain, yTest = train_test_split(X, y, test_size=0.33,
        random_state=1)
```

```
k = 3
```

```
knn = neighbors.KNeighborsClassifier(n_neighbors=k)
knn.fit(X, y)
```

上述程序代码建立 KNeighborsClassifier 对象，参数 n_neighbors 是 K 值；然后调用 fit() 函数使用训练数据集来训练模型，完成后即可使用测试数据集检查准确度，如下所示：

```
print("准确度:", knn.score(XTest, yTest))
```

上述程序代码调用 score() 函数计算预测模型的准确度，其运行结果如下所示：

```
准确度: 0.98
```

然后，输出测试数据集的原始值和预测值，如下所示：

```
print(knn.predict(XTest))
print(yTest.values)
```

上述程序代码中，第 1 行是测试数据集的预测分类，第 2 行是原始分类。其运行结果如下所示：

```
[0 1 1 0 2 1 2 0 0 2 1 0 2 1 1 0 1 1 0 0 1 1 0 2 1 0 0 1 2 1 2 1 2 2 0 1
0 1 2 2 0 1 2 1 2 0 0 0 1]
[0 1 1 0 2 1 2 0 0 2 1 0 2 1 1 0 1 1 0 0 1 1 0 2 1 0 0 1 2 1 2 1 2 2 0 1
0 1 2 2 0 2 2 1 2 0 0 0 1]
```

上述列表中的 0、1、2 代表 3 种类别，仔细检查可以找出预测分类和原始分类的不同之处。

### 3. 如何选择 K 值

因为 K 近邻算法的 K 值会影响分类的准确度，所以可以使用循环执行多次不同 K 值的分类来找出最佳的 K 值。一般来说，K 值的上限是训练数据集的 20%（Python 程序：Ch16_2_2b.py），如下所示：

```
import pandas as pd
import numpy as np
from sklearn import datasets
from sklearn import neighbors
from sklearn.model_selection import train_test_split
import matplotlib.pyplot as plt

iris = datasets.load_iris()

X = pd.DataFrame(iris.data, columns=iris.feature_names)
X.columns = ["sepal_length", "sepal_width", "petal_length", "petal_width"]
target = pd.DataFrame(iris.target, columns=["target"])
y = target["target"]
```

```
XTrain, XTest, yTrain, yTest = train_test_split(X, y, test_size=0.33,
    random_state=1)
```

```
Ks = np.arange(1, round(0.2*len(XTrain) + 1))
accuracies=[]
for k in Ks:
    knn = neighbors.KNeighborsClassifier(n_neighbors=k)
    knn.fit(X, y)
```

```
accuracy = knn.score(XTest, yTest)
accuracies.append(accuracy)

plt.plot(Ks, accuracies)
plt.show()
```

上述程序代码建立 K 值范围，Ks 是从 1 至训练数据集的 20%。在使用 for 循环计算不同 K 值的准确度后，绘制折线图。其运行结果如图 16-11 所示。

![](img/26e9944e14d74e7e03dda869acb3f1a8_450_0.png)

图 16-11

从图 16-11 中可以看出，很多 K 值的准确度都在 98% 以上，变动并非很大，并不容易通过观察 K 值走势来找出最佳 K 值的区间。因此，需要使用第 16.2.3 节的交叉验证（Cross Validation）来找出最佳 K 值。

### 16.2.3 交叉验证的 K 值优化

第 15.3.3 节是将数据集分割成训练数据集和测试数据集，使用训练数据集训练模型，使用测试数据集验证模型，这种方式称为持久性验证（Holdout Validation）。该方式的缺点是有些数据并没有用来训练，单纯只用在验证，即并没有使用完整的数据集来进行模型的训练。

### 1. K-fold 交叉验证

交叉验证用于解决持久性验证的问题，其将数据集分割成 2 个或更多分隔区（Partitions），并且将每一个分隔区都作为测试数据集，将其他分隔区作为训练数据集。最常用的交叉验证是 K-fold 交叉验证（K-fold Cross Validation）。

图 16-12 中，K-fold 将数据集随机分割成相同大小的 K 个分隔区，或称为折（Folds），第 1 次使用第 1 个分隔区作为测试数据集来验证模型，其他 K-1 个分隔区用来训练模型；第 2 次是使用第 2 个分隔区作为测试数据集来验证模型，其他 K-1 个分隔区用来训练模型。重复执行 K 次，即可组合出最后的模型。所以，交叉验证可以让使用数据集的所有数据来训练和建立模型。

![](img/26e9944e14d74e7e03dda869acb3f1a8_451_0.png)

图 16-12

### 2. 交叉验证的 K 值优化

在了解 K-fold 交叉验证后，可以调用 K-fold 交叉验证的 cross_val_score() 函数来找出最佳 K 值（Python 程序：Ch16_2_3.py），如下所示：

```
import pandas as pd
import numpy as np
from sklearn import datasets
from sklearn import neighbors
from sklearn.model_selection import cross_val_score
import matplotlib.pyplot as plt

iris = datasets.load_iris()

X = pd.DataFrame(iris.data, columns=iris.feature_names)
X.columns = ["sepal_length", "sepal_width", "petal_length", "petal_width"]
target = pd.DataFrame(iris.target, columns=["target"])
y = target["target"]

Ks = np.arange(1, round(0.2*len(X) + 1))
accuracies=[]
for k in Ks:
    knn = neighbors.KNeighborsClassifier(n_neighbors=k)
    scores = cross_val_score(knn, X, y, scoring="accuracy",
        cv=10)
    accuracies.append(scores.mean())
```
