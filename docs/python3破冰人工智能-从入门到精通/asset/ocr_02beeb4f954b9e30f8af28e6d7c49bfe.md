在 Scikit-learn 下，GaussianNB 实现了高斯分布数据的朴素贝叶斯算法。数据以鸢尾花的特征作为数据源（相关数据介绍见 11.2.2 节），通过 load_iris()加载数据，可通过命令 dir(iris) 查看 iris 所具有的属性。实现案例见例 12-3。

# 【例 12-3】GaussianNB 实现

输入：

```
```python
from sklearn import datasets
iris = datasets.load_iris()
X=iris.data
y=iris.target

from sklearn.naive_bayes import GaussianNB
gnb = GaussianNB()
gnb.fit(X, y)
y_pred = gnb.fit(X, y).predict(iris.data)

print("Number of mislabeled points out of a total %d points : %d"
      % (iris.data.shape[0], (iris.target != y_pred).sum()))
```
```

输出：

```
```
Number of mislabeled points out of a total 150 points : 6
```
```

在 Scikit-learn 下，朴素贝叶斯算法提供了大规模数据集的处理方法。处理大规模数据集的时候，其训练集不适合放在内存中，MultinomialNB、BernoulliNB 和 GaussianNB 实现了增量学习方法 partial_fit，可以动态地增加数据，它的使用方法与其他分类器的 一样。所有的朴素贝叶斯分类器都支持样本权重。partial_fit 函数形式如下：

```
```
partial_fit(X, y, classes=None, sample_weight=None)
```
```

X 和 y 分别为增量训练的样本特征数据特征与标记数据。

classes 是一个数组，shape = [n_classes] (default=None), 是可能出现在 y 中的所有类的列表。

sample_weight 是一个数组，shape = [n_samples] (default=None)。给出每个样本的权重，如果未指定，则全为 1。

注：在第一次调用 pritial_fit 时，必须传入参数，后续调用不必传入该参数。

# 2. 模型对比

在机器学习的背景下，很多数据均是来源于生活的真实数据，其往往不能满足众多的样本特征独立。通过 12.1 节与 12.2 节我们了解：朴素贝叶斯中的“朴素”就是样本特征的独立，往往都需要假设其独立，以构建模型。下面通过一个葡萄酒数据来演示。

wine.data 数据集是描述在意大利同一地区生产的 3 种不同品种的酒，这些数据包括了 3 种酒中 13 种不同成分的数量。13 种成分分别为： Alcohol、 Malicacid、 Ash、 Alcalinity of ash、