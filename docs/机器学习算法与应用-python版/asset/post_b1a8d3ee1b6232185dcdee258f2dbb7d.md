
import numpy as np
data=pd.DataFrame({
    ' 学号 ':[1,2,3,4,5,6,7,7,8],
    ' 身高 ':[172.0,162.0,175.0,170.0,17.0,160.0,164.0,
        164.0,160.0],
    ' 体重 ':[70,62,75,68,67,58,64,64,53]
})
print(" 是否存在低于正常身高范围的值: ",any(data[' 身高 ']<50))

输出结果如下。

> 是否存在低于正常身高范围的值: True

对于身高低于 50cm 的值，用其他身高中的最小值来填充，代码如下。

```
renew_value=data[' 身高 '][data[' 身高 ']>50].min()
data.loc[data[' 身高 ']<50," 身高 "]=renew_value
print(data)
```

输出结果如下。

|  | 学号 | 身高 | 体重 |
|---|---|---|---|
| 0 | 1 | 172.0 | 70 |
| 1 | 2 | 162.0 | 62 |
| 2 | 3 | 175.0 | 75 |
| 3 | 4 | 170.0 | 68 |
| 4 | 5 | 160.0 | 67 |
| 5 | 6 | 160.0 | 58 |
| 6 | 7 | 164.0 | 64 |
| 7 | 7 | 164.0 | 64 |
| 8 | 8 | 160.0 | 53 |

由如上结果可以看出，第五条数据中的身高值已经被替换为 160cm。

## 任务 2-3 实现 PCA 图像去噪

### 任务描述
本任务主要学习主成分分析方法的概念、主成分分析的计算步骤，基于 Sklearn 库构建 PCA，并使用 PCA 进行图像去噪处理。

### 任务目标
掌握主成分分析方法的计算步骤，并应用到图像去噪中。

### 知识准备

PCA 是一种无监督算法，也是一种“降维”方法。它将相互关联的变量数量减少，而不会失去这些变量的本质。它概述了输入和变量之间的线性关系。PCA 还可以作为一种工具来更好地对高维数据进行数据可视化。

### 任务实施

在应用机器学习技术时，要处理成百上千个变量。大多数变量是相互关联的。在这种情况下，将模型拟合到数据集会导致模型的准确性较差，因此需要消除数据中的冗余信息。此任务将介绍如何使用 PCA 进行数据的冗余消除、特征提取并将 PCA 应用到图像去噪中。

### 步骤 1 了解 PCA 计算步骤

主成分分析的主要步骤如下。

- (1) 对所有的样本进行中心化：样本的每个特征减去该样本的特征均值。
- (2) 计算样本的协方差矩阵。
- (3) 对协方差矩阵进行特征值分解。
- (4) 取出最大的 n 个特征值对应的特征向量，将所有的特征向量标准化，组成特征向量矩阵。
- (5) 用特征向量矩阵乘以样本集中的每一个样本，转化为新的样本，即为降维后的输出样本。

### 步骤 2 了解 PCA 函数的主要参数

主成分分析函数 sklearn.decomposition.PCA 的主要参数如下。

- n_components：经过 PCA 降维后的目标特征维度数目。
- copy：在运行算法时，根据 copy 的值决定是否将原始数据复制一份。默认为 True，在运行 PCA 降维后，不改变原始数据；否则，在原始数据上直接进行降维处理。
- whiten：表示是否对降维后的数据的每个特征进行标准化。默认为 False，令每个特征的方差都为 1。
- svd_solver：指定奇异值分解（Singular Value Decomposition，SVD）的方法。①当 svd_solver 值为 'randomized' 时，采用一些随机算法加快 SVD 求解，这种方法适合处理大数据量、维度多的数据；②当 svd_solver 值为'full' 时，则采用传统意义上的 SVD 进行求解。

### 步骤 3 使用 Sklearn 构建 PCA

通过 Sklearn 库中的 PCA 方法进行数据的主要特征提取，主要步骤如下：首先装载输入，接下来引入 PCA、设置 PCA 的参数，然后提取数据特征，最后是数据还原等。下面通过代码 2-9 来实现数据的主要特征提取。

### 【代码 2-9】

```
# 第一步：装载数据
import numpy as np
X=np.array([
 [1,2,1,2],
 [7,2,2,4],
 [3,7,3,6],
 [2,5,2,3],
 [3,2,2,9],
 [5,0,3,5]])
# 第二步：选择 PCA 算法并设置参数
from sklearn.decomposition import PCA
pca=PCA(n_components=3)
# 第三步：进行数据降维
X_pca=pca.fit_transform(X)
# 第四步：将降维后的数据进行还原
X_new=pca.inverse_transform(X_pca)
# 第五步：输出 PCA 模型系数
print(pca.explained_variance_ratio_)
print(X_new)
```

输出结果如下。

```
[[ 0.44274388  0.35663085  0.18459621]
 [ 0.95722833  1.97159761  1.22028273  1.9753309 ]
 [ 6.88643498  1.92458751  2.58488274  3.93449993]
 [ 3.05169086  7.03432515  2.73378166  6.02981336]
 [ 2.01310818  5.00870444  1.93249024  3.00756031]
 [ 2.92587872  1.95078   2.38173953  8.95724961]
 [ 5.16565893  0.11000528  2.14682309  5.09554589]]
```

代码 2-9 采用 PCA 方法对数据进行降维，并对降维后的数据进行恢复，PCA（n_components=n）函数中的参数 n_components 表示降维后的目标维度，一般小于数据的特征数，此案例中的数据特征数为 4，目标维度设置为 3。可以看到恢复后的数据与原始数据比较接近，例如，原始数据为 5，恢复后数据为 5.16。

### 步骤 4 实施 PCA 图像去噪

PCA 还可以用于图像去噪（即减少数字图像中含有的噪声信息）。PCA 认为图像的噪声是异常值，不属于图像的主要部分，它可以提取信息的主要成分，因此可以使用 PCA 对图像进行去噪。本任务使用的图片如图 2-2 所示。代码 2-10 先将彩色图片转换成灰度图片，再对灰度图片进行去噪处理。

![](img/cba85b47e7b62410be6bbcef421bf9c1_34_0.png)

图 2-2 未加噪声的原始图像

### 【代码 2-10】

```
# 第一步: 读取并显示图片
from sklearn.datasets import load_sample_image
import matplotlib.pyplot as plt
import numpy as np
img1=load_sample_image("china.jpg")
img1=np.array(img1,dtype=np.float64)/255
plt.imshow(img1)
plt.show()
# 第二步: 图像灰度化
from skimage import color,filters
img1=color.rgb2gray(img1)
img2=np.random.normal(img1.data,0.1)
plt.imshow(img2,cmap='gray')
plt.show()
# 第三步: PCA 降维去噪
from sklearn.decomposition import PCA
pca=PCA(0.8)
img2_pca=pca.fit_transform(img2)
img3=pca.inverse_transform(img2_pca)
# 第四步: 显示去噪后的图像
plt.imshow(img3,cmap='gray')
plt.show()
```

输出结果如图 2-3 和图 2-4 所示。

![](img/cba85b47e7b62410be6bbcef421bf9c1_35_0.png)

![](img/cba85b47e7b62410be6bbcef421bf9c1_35_1.png)

代码 2-10 使用了 PCA 算法对图像进行去噪，其中 PCA 的参数设置为 0.8，表示保留图像 80% 的主要特征。图 2-3 为含噪图像，图 2-4 为去噪后的图像。从图 2-3 和图 2-4 可以看出，图 2-4 比图 2-3 平滑，说明去除了一些噪声。

## 项目小结

本项目介绍了机器学习数据集的划分、数据的预处理和数据的降维。本项目的难点是 Sklearn 库中使用函数完成数据集划分、数据预处理和数据降维。数据预处理是机器学习的基础部分，接下来的机器学习算法应用，都需要使用本项目中的知识。

## 练习题

### 一、选择题

- 1. 以下（ ）函数是 Sklearn 库中用于划分数据集的。
- A. MinMaxScaler   B. train_test_split   C. StandardScaler

- 2. 以下（ ）函数是 Sklearn 库中用于均值方差归一化数据集的。
- A. MinMaxScaler   B. train_test_split   C. StandardScaler

- 3. Sklearn 库中用于划分数据集函数的（ ）参数如果设置为一样，那么得到的划分结果也一样。
- A. MinMaxScaler   B. stratify   C. test_size   D. random_state

- 4. 以下（ ）函数是用来删除数据中包含空值数据的。
- A. fillna   B. dropna   C. loc

- 5. Sklearn 库中的主成分分析函数的（ ）参数用来表示经过 PCA 降维后的目标特征维度数目。
- A. n_components   B. copy   C. whiten   D. svd_solver

### 二、简答题

- 1. 简述机器学习中测试集和验证集的作用。

- 2. 简述机器学习中原始数据常见的一些问题。

- 3. 简述 PCA 的原理、特点及应用场景。

- 4. 设置 train_test_split 函数中参数 random_state 为 None，观察数据集划分结果是否相同。

## 项目3

### 基于 K-Means 算法的应用实践

### 项目导读

在机器学习中，经常需要确定目标所属的类别。例如，要判定一个动物是狗、猫还是其他动物。解决这类问题的办法是先提供一些含有各种动物的数据集，让算法得到充分的训练学习，然后根据学习得到的先验信息对一个动物的类型做出判断。这种做法称为有监督学习，它分为训练和预测两个阶段。在训练阶段，使用大量的样本进行学习，得到一个判定动物类型的模型。在预测阶段，给出一张含有动物的图像，就可以使用这个模型预测出它的类别。

### 学习目标

- 掌握图像聚类算法 K-Means 的含义。
- 理解身高、体重聚类，确定 K 值。
- 掌握 K-Means 算法压缩图像的方法。

### 知识导图

![](img/cba85b47e7b62410be6bbcef421bf9c1_37_0.png)

## 任务 3-1 使用 K-Means 算法实现聚类手写图像

### 任务描述

本任务学习聚类算法的基本原理、基本步骤，并使用 Sklearn 中的 K-Means 算法实现聚类手写数字图像。

### 任务目标

熟练使用 Sklearn 中的 K-Means 算法来完成一些图像聚类的操作。

### 知识准备

#### 1. 聚类算法

与分类问题相似，聚类算法也需要确定一个物体的类别，不同的是，它没有事先定义类别，需要用户想办法把一批样本分开，形成多个类别。聚类算法需要保证每一个类中的样本之间是相似的，而不同类的样本之间是不同的。在这里，类型被称为“簇”（ cluster ）。例如有一群动物，事先没有说明有哪些动物，也没有一个训练好的判定各种动物的模型，聚类算法要自动将这群动物进行归类。这里没有统一的、确定的划分标准，可能有人将颜色相似的动物归在一起，有人将形状相似的动物归在一起。聚类算法是一种无监督学习，没有训练过程，这是和分类算法最本质的区别。算法要根据自己定义的规则，将相似的样本划分在一起，不相似的样本分成不同的类。

聚类就是按照某个特定标准把一个数据集分割成不同的类或簇，如图 3-1 所示，使同一个簇内的数据对象的相似性尽可能大，同时不同簇间的数据对象的差异性尽可能地大，本质上是集合划分问题。聚类算法的核心是如何定义簇，要求簇内的样本尽可能相似。簇的划分通常是根据簇内样本之间的距离或样本点在数据空间中的密度来确定。

在生物学中，聚类可以用来发现具有类似表达模式的基因群组。在日常的网上购物中，聚类可以用来定位一个用户可能感兴趣的产品。在市场营销中，聚类可以用来发现相似用户的分组。

![](img/cba85b47e7b62410be6bbcef421bf9c1_38_0.png)

图 3-1 聚类划分结果

#### 2. K-Means 算法

K-Means 算法是机器学习中常用的无监督的聚类算法。对于给定的数据集，可以通过算法划分成 K 个不同的簇，且每个簇的中心（质心）可以通过属于该簇所有点坐标的平均或者加权平均计算而成。簇个数 K 是用户指定的，每一个簇通过其质心来描述。以距离作为数据对象间相似性度量的标准，即数据对象间距离越小，它们的相似性越高，越有可能在同一个簇中。K-Means 算法通常采用欧式距离来计算数据对象间的距离。

K-Means 算法在没有监督的情况会从经验中学习，性能可以通过一定的指标衡量。该算法的原理相对简单，可解释性好，收敛速度快，调参也只需要改变簇个数 K 一个参数。初始值的确定对于 K-Means 算法的结果影响较大，可能每次聚类的结果并不完全一致，也可能只是局部最优而不是全局最优。尤其是在两个簇距离过近时，影响较大。

### 任务实施

本任务将介绍 K-Means 的定义算法实现的步骤、质心的计算，并使用 K-Means 算法来实现二维图像聚类。

### 步骤 1 了解算法步骤

K-Means 算法的步骤如下。

- (1) 适当选择 K 个簇的初始质心。
- (2) 在第 k 次迭代中，对任意一个样本，求其到 K 个质心的距离，将该样本归到距离最短的质心所在的簇。
- (3) 利用均值等方法更新该类的质心值。
- (4) 对于所有的 K 个簇质心，如果利用（2）和（3）的迭代更新后值保持不变，则迭代结束，否则继续迭代。

### 步骤 2 计算点与质心的距离计算方法

K-Means 算法是基于质心或基于距离的算法，根据每个点到质心的距离分别计算出属于哪个簇。K-Means 算法的主要目标是计算出各个点到各自质心距离之和的最小值。计算两点之间的距离有很多公式，本项目以欧氏距离计算公式为例，如式（3-1）所示。

\(d=\sqrt{(x_1-x_2)^2+(y_1-y_2)^2}\) (3-1)

### 步骤 3 实现图像聚类

图像聚类可以用于划分图像数据集，并且发现同类图像的特征。接下来我们使用 Sklearn 中的 K-Means 算法来聚类手写数字图像，使用的数据集是 Sklearn 自带的手写数据库，其中包含了 0～9 的手写数字，一共 10 个类 1797 张图片，每张图片的分辨率是 8 像素 x 8 像素。图 3-2 表示数字 0 的手写数字图像，图 3-3 表示数字 9 的手写数字图像。

![](img/cba85b47e7b62410be6bbcef421bf9c1_40_0.png)

![](img/cba85b47e7b62410be6bbcef421bf9c1_40_1.png)

### 【代码 3-1】

```
# 第一步：装载数据
from sklearn.datasets import load_digits
digits = load_digits()
X = digits.data;
y = digits.target
# 第二步：引入时间计时
import time
start = time.process_time()
# 第三步：进行 K-Means 聚类
from sklearn.cluster import KMeans
KM = KMeans(n_clusters=10)
c = KM.fit_predict(X)
end = time.process_time()
print('Time is %.3f' % (end - start))
# 第四步：计算聚类结果
import numpy as np
y_predict = np.zeros_like(c)
from scipy.stats import mode
for i in range(10) :
    mask = (c == i)
    y_predict[mask] = mode(y[mask])[0]
KM.cluster_centers_.shape
# 第五步：显示聚类中心
```

```
import matplotlib.pyplot as plt
plt.rcParams['font.sans-serif'] = [u'SimHei']
plt.rcParams['axes.unicode_minus'] = False
fig, ax = plt.subplots(2, 5, figsize = (8, 3))
centers = KM.cluster_centers_.reshape(10, 8, 8)
for axi, center in zip(ax.flat, centers):
    axi.set(xticks = [], yticks = [])
    axi.imshow(center, cmap = plt.cm.binary)
# 第六步：输出聚类准确率
from sklearn.metrics import accuracy_score
print('聚类准确率为：%.4f %%' % accuracy_score(y, y_predict))
```

输出结果如下，显示的聚类中心如图 3-4 所示。

```
Time is 0.406
聚类准确率为: 0.7919 %
```

![](img/cba85b47e7b62410be6bbcef421bf9c1_41_0.png)

图 3-4 聚类中心

代码 3-1 采用 K-Means 算法对手写数字进行识别，其中，因为我们已经知道这些数字图片一共有 10 个类，所以 K-Means 函数中的 n_clusters 设置为 10。图 3-4 为聚类中心，一共 10 个，可以看见数字 0～9。
项目 2 中学习的 PCA 可以用于数据降维，提出数据中的主要信息，我们通过 PCA 对手写字符数据进行降维处理，观察降维后的聚类结果，使用的程序如下。

### 【代码 3-2】

```
# 第一步：装载数据
from sklearn.datasets import load_digits
digits = load_digits()
X = digits.data;
y = digits.target
# 第二步：引入时间计时
import time
from sklearn.decomposition import PCA
start = time.process_time()
# 第三步：PCA 降维
pca = PCA(n_components=10)
```
