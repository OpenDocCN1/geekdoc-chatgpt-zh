

# 高斯混合模型分类器

## 提取倾斜角度

```
# Extract the angle of tilt
angle = np.arctan2(norm_vec[1], norm_vec[0])
angle = 180 * angle / np.pi
```

## 放大椭圆以进行可视化

放大椭圆以进行可视化。特征值控制椭圆的大小：

```
# Scaling factor to magnify the ellipses
# (random value chosen to suit our needs)
scaling_factor = 8
eigenvalues *= scaling_factor
```

## 绘制椭圆

绘制椭圆：

```
# Draw the ellipse
ellipse = patches.Ellipse(classifier.means_[i, :2],
        eigenvalues[0], eigenvalues[1], 180 + angle,
        color=color)
axis_handle = plt.subplot(1, 1, 1)
ellipse.set_clip_box(axis_handle.bbox)
ellipse.set_alpha(0.6)
axis_handle.add_artist(ellipse)
```

## 在图上叠加输入数据

在图上叠加输入数据：

```
# Plot the data
colors = 'bgr'
for i, color in enumerate(colors):
    cur_data = iris.data[iris.target == i]
    plt.scatter(cur_data[:,0], cur_data[:,1], marker='o',
            facecolors='none', edgecolors='black', s=40,
            label=iris.target_names[i])
```

## 在此图上叠加测试数据

在此图上叠加测试数据：

```
test_data = X_test[y_test == i]
plt.scatter(test_data[:,0], test_data[:,1], marker='s',
        facecolors='black', edgecolors='black', s=40 ,
        label=iris.target_names[i])
```

## 计算训练和测试数据的预测输出

计算训练和测试数据的预测输出：

```
# Compute predictions for training and testing data
y_train_pred = classifier.predict(X_train)
accuracy_training = np.mean(y_train_pred.ravel() == y_train.ravel()) * 100
print('Accuracy on training data =', accuracy_training)
```

```
y_test_pred = classifier.predict(X_test)
accuracy_testing = np.mean(y_test_pred.ravel() == y_test.ravel()) * 100
print('Accuracy on testing data =', accuracy_testing)
```

```
plt.title('GMM classifier')
plt.xticks(())
plt.yticks(())
```

```
plt.show()
```

完整代码在文件`gmm_classifier.py`中给出。运行代码后，您将看到以下输出：

![](Images/1d669107052e7cccd1d9be63d5f96025.png)

图 8：高斯混合模型分类器图

输入的数据由三个分布组成。不同大小和角度的三个椭圆表示输入数据中的基础分布。您将看到以下输出：

```
Accuracy on training data = 87.5
Accuracy on testing data = 86.6666666667
```

在本节中，我们学习了高斯混合模型，并使用 Python 开发了一个示例。在下一节中，我们将学习另一种无监督学习技术，即相似性传播模型，用于对数据进行分类，并将其用于，以便在股市数据中查找子组。

# 使用相似性传播模型在股票市场中寻找子群体

**相似性传播**是一种聚类算法，不需要事先指定多个聚类。由于其通用性和实现的简便性，它已在许多领域中找到了许多应用。它使用一种称为消息传递的技术找出代表性的群集，称为样本。它从指定需要考虑的相似性度量开始。同时将所有训练数据点视为潜在的范例。然后，它在数据点之间传递消息，直到找到一组示例为止。

消息传递发生在两个备用步骤中，分别称为**责任**和**可用性**。责任是指从群集成员发送到候选示例的消息，指示该数据点作为该示例群集的成员的适合程度。可用性是指从候选示例发送到集群的潜在成员的消息，表明它作为示例的适用性。一直执行此操作，直到算法收敛到最佳样本集为止。

还有一个称为首选项的参数，该参数控制将发现的示例数量。如果选择较高的值，则将导致算法找到太多的聚类。如果选择一个较低的值，则将导致少数群集。最佳值是点之间的中间相似度。

让我们使用“相似性传播”模型来查找股票市场中的子组。我们将使用开盘价和收盘价之间的股票报价变化作为控制特征。创建一个新的 Python 文件并导入以下包：

```
import datetime
import json
import numpy as np
import matplotlib.pyplot as plt
from sklearn import covariance, cluster
import yfinance as yf
```

`matplotlib` 中可用的股市数据将用作输入。公司符号在文件`company_symbol_mapping.json`中映射到其全名：

```
# Input file containing company symbols
input_file = 'company_symbol_mapping.json'
```

从文件中加载公司符号图：

```
# Load the company symbol map
with open(input_file, 'r') as f:
    company_symbols_map = json.loads(f.read())
```

```
symbols, names = np.array(list(company_symbols_map.items())).T
```

从 `matplotlib` 加载股票报价：

```
# Load the historical stock quotes
start_date = datetime.datetime(2019, 1, 1)
end_date = datetime.datetime(2019, 1, 31)
quotes = [yf.Ticker(symbol).history(start=start_date, end=end_date)
                for symbol in symbols]
```

计算开始和结束报价之间的差异：

```
# Extract opening and closing quotes
opening_quotes = np.array([quote.Open for quote in quotes]).astype(np.float)
closing_quotes = np.array([quote.Close for quote in quotes]).astype(np.float)
```

```
# Compute differences between opening and closing quotes
quotes_diff = closing_quotes - opening_quotes
```

规范化数据：

```
# Normalize the data
X = quotes_diff.copy().T
X /= X.std(axis=0)
```

创建一个图模型：

```
# Create a graph model
edge_model = covariance.GraphLassoCV()
```

训练模型：

```
# Train the model
with np.errstate(invalid='ignore'):
    edge_model.fit(X)
```

使用我们刚刚训练的边缘模型构建相似性传播聚类模型：

```
# Build clustering model using Affinity Propagation model
_, labels = cluster.affinity_propagation(edge_model.covariance_)
num_labels = labels.max()
```

打印输出：

```
# Print the results of clustering
print('\nClustering of stocks based on difference in opening and closing quotes:\n')
for i in range(num_labels + 1):
    print("Cluster", i+1, "==>", ', '.join(names[labels == i]))
```

完整代码在文件`stocks.py`中给出。运行代码时，您将看到以下输出：

![](Images/53210b9d08af9f241e5193ef8ee9d92e.png)

图 9：基于开盘价和收盘价差异的股票聚类

此输出代表该时间段内股票市场中的各个子组。请注意，运行代码时群集可能会以不同的顺序出现。

既然我们已经了解了“相似性传播”模型并学习了一些新概念，我们将继续本章的最后部分，在此部分中，我们将使用无监督学习技术，根据客户的购物习惯来细分市场数据。

## 根据购物模式细分市场

让我们看看如何运用无监督学习技术根据客户的购物习惯来细分市场。为您提供了一个名为`sales.csv`的文件。该文件包含来自多家零售服装店的各种上衣的销售详细信息。目标是确定模式并根据这些商店中售出的商品数量来细分市场。

创建一个新的 Python 文件并导入以下包：

```
import csv
```

```
import numpy as np
import matplotlib.pyplot as plt
from sklearn.cluster import MeanShift, estimate_bandwidth
```

从输入文件加载数据。由于它是 CSV 文件，因此我们可以在 Python 中使用 `csv` 阅读器从该文件中读取数据并将其转换为`NumPy`数组：

```
# Load data from input file
input_file = 'sales.csv'
file_reader = csv.reader(open(input_file, 'r'), delimiter=',')
X = []
for count, row in enumerate(file_reader):
    if not count:
        names = row[1:]
        continue
```

```
X.append([float(x) for x in row[1:]])
```

```
# Convert to numpy array
X = np.array(X)
```

让我们估计输入数据的带宽：

```
# Estimating the bandwidth of input data
bandwidth = estimate_bandwidth(X, quantile=0.8, n_samples=len(X))
```

根据估计的带宽训练平均漂移模型：

```
# Compute clustering with MeanShift
meanshift_model = MeanShift(bandwidth=bandwidth, bin_seeding=True)
meanshift_model.fit(X)
```

提取标签和每个群集的中心：

```
labels = meanshift_model.labels_
cluster_centers = meanshift_model.cluster_centers_
num_clusters = len(np.unique(labels))
```

打印集群数和集群中心：

```
print("\nNumber of clusters in input data =", num_clusters)
```

```
print("\nCenters of clusters:")
print('\t'.join([name[:3] for name in names]))
for cluster_center in cluster_centers:
    print('\t'.join([str(int(x)) for x in cluster_center]))
```

我们正在处理六维数据。为了使数据可视化，让我们使用由第二维和第三维构成的二维数据：

```
# Extract two features for visualization
cluster_centers_2d = cluster_centers[:, 1:3]
```

绘制群集的中心：

```
# Plot the cluster centers
plt.figure()
plt.scatter(cluster_centers_2d[:,0], cluster_centers_2d[:,1],
        s=120, edgecolors='black', facecolors='none')
```

```
offset = 0.25
plt.xlim(cluster_centers_2d[:,0].min() - offset * cluster_centers_2d[:,0].ptp(),
        cluster_centers_2d[:,0].max() + offset * cluster_centers_2d[:,0].ptp(),)
plt.ylim(cluster_centers_2d[:,1].min() - offset * cluster_centers_2d[:,1].ptp(),
        cluster_centers_2d[:,1].max() + offset * cluster_centers_2d[:,1].ptp())
plt.title('Centers of 2D clusters')
```

```
plt.show()
```

文件`market_segmentation.py`中提供了完整代码。运行代码时，您将看到以下输出：

![](Images/62eb5502dfa9b339ff4b41a3b3ea03e5.png)

图 10：2D 群集的中心图

在本章的最后一部分中，我们应用了在本章前面了解的均值漂移算法，并将其用于分析和细分客户的使用习惯。

您还将看到以下输出：

![](Images/164942a76daf8c5906ef6e48f751546e.png)

图 11：集群中心输出

## 总结

在本章中，我们首先讨论无监督学习及其应用。然后，我们学习了聚类以及如何使用 K 均值算法聚类数据。我们讨论了如何使用均值漂移算法估计聚类数。我们讨论了轮廓分数以及如何估计聚类的质量。我们了解了高斯混合模型，以及如何基于它们建立分类器。我们还讨论了“亲和力传播”模型，并使用它在股票市场中找到了子组。然后，我们应用均值漂移算法根据购物模式细分市场。

在下一章中，我们将学习如何构建推荐引擎。

# 8 构建推荐系统

> 原文：[https://gitee.com/apachecn/apachecn-dl-zh/blob/master/docs/ai-py/08.md](https://gitee.com/apachecn/apachecn-dl-zh/blob/master/docs/ai-py/08.md)

在本章中，我们将学习如何建立一个推荐系统来推荐人们可能喜欢看的电影。我们将了解 K 最近邻分类器，并了解如何实现它。我们使用这些概念来讨论协同过滤，然后使用它来构建推荐系统。

到本章末，您将了解以下内容：

*   提取最近邻
*   建立 K 最近邻分类器
*   计算相似度分数
*   使用协同过滤查找相似用户
*   建立电影推荐系统

# 提取最近邻

推荐人系统采用最近邻的概念来找到好的建议。名称*最近邻*是指从给定数据集中查找到输入点最近的数据点的过程。这通常用于构建分类系统，该分类系统根据输入数据点与各种类别的接近程度对数据点进行分类。让我们看看如何找到给定数据点的最近邻。

首先，创建一个新的 Python 文件并导入以下包：

```
import numpy as np
import matplotlib.pyplot as plt
from sklearn.neighbors import NearestNeighbors
```

定义样本 2D 数据点：

```
# Input data
X = np.array([[2.1, 1.3], [1.3, 3.2], [2.9, 2.5], [2.7, 5.4], [3.8, 0.9],
        [7.3, 2.1], [4.2, 6.5], [3.8, 3.7], [2.5, 4.1], [3.4, 1.9],
        [5.7, 3.5], [6.1, 4.3], [5.1, 2.2], [6.2, 1.1]])
```

定义要提取的最近邻的数量：
