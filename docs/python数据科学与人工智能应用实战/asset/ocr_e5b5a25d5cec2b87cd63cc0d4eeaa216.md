图 16-10 所示的散布图已经显示了一些分类的线索。在图 16-10（b）中可以看出红色点的 Iris-Setosa 花瓣比较小，绿色点的 Iris-Versicolour 是中等尺寸，最大的是黄色点的 Iris-Virginica。Iris-Setosa、Iris-Versicolour 和 Iris-Virginica 是 3 种鸢尾花的分类。

在图 16-10（a）中可以看出 Setosa 的花萼（Sepal）明显比另外两类的尺寸较短且较宽。

# 2. 建立 K 近邻模型分类鸢尾花

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