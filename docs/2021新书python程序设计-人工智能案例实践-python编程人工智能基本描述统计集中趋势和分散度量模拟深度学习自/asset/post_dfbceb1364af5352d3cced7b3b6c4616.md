
![](img/bf43b6caad271ba92b343a48c79c2155_691_0.png)

山鸢尾

资料来源：美国国家公园

![](img/bf43b6caad271ba92b343a48c79c2155_691_1.png)

变色鸢尾

资料来源：Jefficus提供，见 https://commons.wikimedia.org/w/index.php?title=User:Jefficus&action=edit&redlink=1

![](img/bf43b6caad271ba92b343a48c79c2155_692_0.png)

维吉尼亚鸢尾

资料来源：Christer T Johansson

## 14.7.1 加载Iris数据集

使用ipython --matplotlib启动IPython，然后使用 sklearn.datasets模块的load_iris函数获取包含数据集的Bunch对象：

```
In [1]: from sklearn.datasets import load_iris
In [2]: iris = load_iris()
```

Bunch对象的DESCR属性表示有150个样本（Number of Instances），每个样本有4个特征（Number of Attributes）。该数据集中没有缺失值。数据集通过用整数0、1和2标记样本来对样本进行分类，整数0、1和2分别代表山鸢尾、变色鸢尾和维吉尼亚鸢尾三种鸢尾花。忽略这些标签并尝试利用k均值聚类算法来确定样本的类别。下面的粗体字显示的是DESCR属性中比较关键的信息：

In [3]: print(iris.DESCR)
.. _iris_dataset:

Iris plants dataset
===================

**Data Set Characteristics:**

:Number of Instances: 150 (50 in each of three classes)
:Number of Attributes: 4 numeric, predictive attributes and the class
:Attribute Information:
    - sepal length in cm
    - sepal width in cm
    - petal length in cm
    - petal width in cm
    - class:
        - Iris-Setosa
        - Iris-Versicolour
        - Iris-Virginica

:Summary Statistics:

| 特征 | Min | Max | Mean | SD | Class Correlation |
|------|-----|-----|------|-----|-------------------|
| sepal length | 4.3 | 7.9 | 5.84 | 0.83 | 0.7826 |
| sepal width | 2.0 | 4.4 | 3.05 | 0.43 | -0.4194 |
| petal length | 1.0 | 6.9 | 3.76 | 1.76 | 0.9490 (high!) |
| petal width | 0.1 | 2.5 | 1.20 | 0.76 | 0.9565 (high!) |

:Missing Attribute Values: None
:Class Distribution: 33.3% for each of 3 classes.
:Creator: R.A. Fisher
:Donor: Michael Marshall (MARSHALL%PLU@io.arc.nasa.gov)
:Date: July, 1988

The famous Iris database, first used by Sir R.A. Fisher. The dataset is taken from Fisher's paper. Note that it's the same as in R, but not as in the UCI Machine Learning Repository, which has two wrong data points.

This is perhaps the best known database to be found in the pattern recognition literature. Fisher's paper is a classic in the field and is referenced frequently to this day. (See Duda & Hart, for example.) The data set contains 3 classes of 50 instances each, where each class refers to a type of iris plant. One class is linearly separable from the other 2; the latter are NOT linearly separable from each other.

.. topic:: References

- Fisher, R.A. "The use of multiple measurements in taxonomic problems"
  Annual Eugenics, 7, Part II, 179-188 (1936); also in "Contributions to Mathematical Statistics" (John Wiley, NY, 1950).
- Duda, R.O., & Hart, P.E. (1973) Pattern Classification and Scene Analysis.
  (Q327.D83) John Wiley & Sons. ISBN 0-471-22361-1. See page 218.
- Dasarathy, B.V. (1980) "Nosing Around the Neighborhood: A New System Structure and Classification Rule for Recognition in Partially Exposed Environments". IEEE Transactions on Pattern Analysis and Machine Intelligence, Vol. PAMI-2, No. 1, 67-71.
- Gates, G.W. (1972) "The Reduced Nearest Neighbor Rule". IEEE Transactions on Information Theory, May 1972, 431-433.
- See also: 1988 MLC Proceedings, 54-64. Cheeseman et al's AUTOCLASS II conceptual clustering system finds 3 classes in the data.
- Many, many more ...

### 检查样品、特征和目标的数量

可以通过data数组的shape来确认样本数和每个样本的特征数，并且可以通过target数组的shape来确认目标数：

```
In [4]: iris.data.shape
Out[4]: (150, 4)

In [5]: iris.target.shape
Out[5]: (150,)
```

数组target_names包含target数组的数字标签的名称，dtype='<U10'表示这些名称是最多包含10个字符的字符串：

```
In [6]: iris.target_names
Out[6]: array(['setosa', 'versicolor', 'virginica'], dtype='<U10')
```

数组feature_names包含一个data数组中每列字符串名称的列表：

```
In [7]: iris.feature_names
Out[7]:
['sepal length (cm)',
 'sepal width (cm)',
 'petal length (cm)',
 'petal width (cm)']
```

## 14.7.2 探索Iris数据集：使用pandas进行描述性统计

下面使用DataFrame来探索鸢尾花数据集。正如我们在加利福尼亚房价案例研究中所做的，需要设置pandas选项来规范输出格式：

```
In [8]: import pandas as pd
In [9]: pd.set_option('max_columns', 5)
In [10]: pd.set_option('display.width', None)
```

使用feature_names数组作为列名，创建一个包含data数组内容的DataFrame：

```
In [11]: iris_df = pd.DataFrame(iris.data, columns=iris.feature_names)
```

接下来，添加一个包含每个样本品种名称的列。下面的代码段中的列表使用target数组中的值来查找target_names数组中的相应品种名称：

```
In [12]: iris_df['species'] = [iris.target_names[i] for i in iris.target]
```

下面用pandas来查看一些样本，再次注意pandas在列标题的右侧显示了一个 “\” ，表示下面会显示更多的列：

```
In [13]: iris_df.head()
Out[13]:
   sepal length (cm)  sepal width (cm)  petal length (cm)  \
0                5.1               3.5                1.4  
1                4.9               3.0                1.4  
2                4.7               3.2                1.3  
3                4.6               3.1                1.5  
4                5.0               3.6                1.4  

   petal width (cm) species
0               0.2  setosa
1               0.2  setosa
2               0.2  setosa
3               0.2  setosa
4               0.2  setosa
```

### 计算一些数值列的描述性统计数据：

```
In [14]: pd.set_option('precision', 2)

In [15]: iris_df.describe()
Out[15]:
              sepal length (cm)  sepal width (cm)  petal length (cm)  \
count             150.00            150.00            150.00  
mean                5.84              3.06              3.76  
std                 0.83              0.44              1.77  
min                 4.30              2.00              1.00  
25%                 5.10              2.80              1.60  
50%                 5.80              3.00              4.35  
75%                 6.40              3.30              5.10  
max                 7.90              4.40              6.90  

         petal width (cm)
count        150.00
mean           1.20
std            0.76
min            0.10
25%            0.30
50%            1.30
75%            1.80
max            2.50
```

在'species'列上调用describe方法可确认它包含了三个值。这里，我们事先知道样本属于三个类别，但在无监督机器学习中并非总是如此。

```
In [16]: iris_df['species'].describe()
Out[16]:
count     150
unique      3
top     setosa
freq       50
Name: species, dtype: object
```

## 14.7.3 使用Seaborn的pairplot可视化数据集

下面来可视化数据集中的特征，了解数据的更多信息的一种方法就是，查看这些特征是如何相互关联的。该数据集有4个特征，我们无法在一幅图中将其中一个与其他三个相对应，但是可以对所有特征两两作图。代码段[20]使用Seaborn的pairplot函数创建了一个图形网格，绘制每个特征相对其自身和其他指定特征的散点图：

```
In [17]: import seaborn as sns
In [18]: sns.set(font_scale=1.1)
In [19]: sns.set_style('whitegrid')
In [20]: grid = sns.pairplot(data=iris_df, vars=iris_df.columns[0:4],
    hue='species')
...:
```

其中，关键字参数为：

- data—包含要绘制的数据的DataFrame。
- vars—包含要绘制的变量的名称的序列。对于DataFrame，这些是要绘制的列的名称。这里使用前4个DataFrame的列，这4个列分别代表萼片长度、萼片宽度、花瓣长度和花瓣宽度。
- hue—用于指定所绘制数据的颜色的DataFrame的列，这里通过鸢尾品种对数据进行着色。

前面对pairplot的调用产生了以下4×4的图形网格，实时运行这个示例，你可以看到pairplot的3个不同数据点颜色。

![](img/bf43b6caad271ba92b343a48c79c2155_696_0.png)

![](img/bf43b6caad271ba92b343a48c79c2155_697_0.png)

沿左上角到右下角的图显示了该列中绘制的特征的分布，图中从左到右为特征值的范围，从顶部到底部为具有这些值的样本数量。观察萼片长度的分布。

![](img/bf43b6caad271ba92b343a48c79c2155_697_1.png)

图中，最高的阴影区域表示山鸢尾的萼片长度范围（沿x轴显示）大约为4～6厘米，并且大多数山鸢尾样本位于该范围的中间（大约5厘米）。类似地，最右边的阴影区域表示维吉尼亚鸢尾的萼片长度范围约为4～8.5厘米，并且大多数维吉尼亚鸢尾样品的萼片长度为6～7厘米。

其他图显示了该特征相对于其他特征的散点图。在第一列中，其他三个图分别绘制了沿y轴的萼片宽度、花瓣长度和花瓣宽度以及沿x轴的萼片长度。

运行此代码时，将在全彩色输出图中看到，为每个鸢尾品种使用单独的颜色会显示出这些品种如何在特征-特征的基础上彼此相关。有趣的是，从所有的散点图中都能很清楚地将表示山鸢尾的蓝色点与表示其他品种的橙色点和绿色点分开，这表明山鸢尾确实属于单一类别。我们也可以看到其他两个品种有时会相互混淆，如某些橙色点和绿色点会重叠。例如，如果看萼片宽度与萼片长度的散点图，会发现变色鸢尾和维吉尼亚鸢尾混合在一起。这表明，如果只有萼片的宽度和长度测量值，将很难区分这两个品种。

### 用单色显示pairplot

如果删除hue关键字参数，那么pairplot函数仅使用一种颜色来绘制所有的数据，因为它不知道如何区分不同的品种：

```
In [21]: grid = sns.pairplot(data=iris_df, vars=iris_df.columns[0:4])
```

正如下面即将看到的结果图，在这种情况下，沿对角线的图是显示该特征的所有值的分布的直方图，不区分品种。在分析每个散点图时，它们看起来可能只有两个不同的集群，尽管我们已经知道这个数据集有三类。如果事先不知道集群的数量，那么可能需要请教熟悉该数据的领域专家，这样的人可能知道数据集中有三种，当我们尝试对数据执行机器学习时，这是很有价值的信息。

pairplot图表适用于少量特征或特征子集，这样你会有少量的行和列，并且对于相对较小的样本，通过它就可以查看数据点。随着特征和样本数量的增加，每个散点很快会变得太小以至于无法读取。对于较大的数据集，可以选择绘制特征的子集，并随机选择样本子集，以了解数据。

![](img/bf43b6caad271ba92b343a48c79c2155_699_0.png)

## 14.7.4 使用KMeans估计器

在本节中，我们将通过scikit-learn的KMeans估计器（来自sklearn.cluster模块）来进行k均值聚类，以将鸢尾花数据集中的每个样本放入一个集群中。与其他估计器一样，KMeans估计器会隐藏算法的复杂数学细节，使其易于使用。

### 创建估计器

### 创建KMeans对象：

```
In [22]: from sklearn.cluster import KMeans
In [23]: kmeans = KMeans(n_clusters=3, random_state=11)
```

关键字参数n_clusters指定k均值聚类算法的超参数k，KMeans估计器需要计算集群并标记每个样本。当训练KMeans估计器时，算法会为每个集群计算一个质心来表示集群的中心数据点。
