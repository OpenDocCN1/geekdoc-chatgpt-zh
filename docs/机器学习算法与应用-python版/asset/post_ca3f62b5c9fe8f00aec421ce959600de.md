
### (2) 隐含层到输入层参数更新。

$$
\frac{\partial L}{\partial w_{11}} = \frac{\partial L}{\partial ha_{out}} \cdot \frac{\partial ha_{out}}{\partial ha_{in}} \cdot \frac{\partial ha_{in}}{\partial w_{11}}
$$

计算 $\frac{\partial L}{\partial ha_{out}}$：

$$
\frac{\partial L}{\partial ha_{out}} = \frac{\partial L}{\partial y_{in}} \cdot \frac{\partial y_{in}}{\partial ha_{out}}
$$

已知 $\frac{\partial L}{\partial y_{in}} = 0.1253 \times 0.2343$

$$
y_{in} = w_{21} \cdot ha_{out} + w_{22} \cdot hb_{out}
$$

$$
\frac{\partial y_{in}}{\partial ha_{out}} = w_{21}
$$

$$
\frac{\partial L}{\partial ha_{out}} = \frac{\partial L}{\partial y_{in}} \cdot \frac{\partial y_{in}}{\partial ha_{out}} = 0.1253 \times 0.2343 \times 0.6 = 0.0176
$$

计算 $\frac{\partial ha_{out}}{\partial ha_{in}}$：

$$
ha_{out} = \frac{1}{1 + \exp(-ha_{in})}
$$

$$
\frac{\partial ha_{out}}{\partial ha_{in}} = ha_{out} \cdot (1 - ha_{out}) = 0.5150 \times (1 - 0.5150) = 0.2498
$$

计算 $\frac{\partial ha_{in}}{\partial w_{11}}$：

$$
ha_{in} = w_{11} \cdot x = 0.6 \times 0.1 = 0.06
$$

$$
\frac{\partial ha_{in}}{\partial w_{11}} = x = 0.1
$$

综上所述，

$$
\frac{\partial L}{\partial w_{11}} = \frac{\partial L}{\partial ha_{out}} \cdot \frac{\partial ha_{out}}{\partial ha_{in}} \cdot \frac{\partial ha_{in}}{\partial w_{11}} = 0.0176 \times 0.2498 \times 0.1 = 0.0004
$$

更新 \( w_{11} \) 的值

$$
w_{11} = w_{11} - \eta \cdot \frac{\partial L}{\partial w_{11}} = 0.6 - 0.1 \times (0.0004) = 0.6000
$$

\( w_{12} \) 的更新办法和 \( w_{11} \) 相同，更新完 4 个超参数之后，就完成了误差反向传播；接着使用新的权重重新进行前向传播，继续迭代训练，最终获得训练好的模型并保存。

## 任务 9-2 使用人工神经网络算法拟合函数

### 任务描述

根据前面所理解的人工神经网络的基础知识，使用人工神经网络算法拟合函数。

### 任务目标

掌握人工神经网络拟合函数的要点。

### 任务实施

首先拟合第一个函数——抛物线，方程式为 $y = x^2$，函数图形如图 9-4 所示。

![](img/cba85b47e7b62410be6bbcef421bf9c1_125_0.png)

图 9-4 要拟合的抛物线图形

### 【代码 9-1】

```python
import numpy as np
X = np.linspace(-10, 10, 400)
X_new = X.reshape(-1,1)
y = X*X + 1*np.random.rand(len(X))
# 初始化模型对象
from sklearn.neural_network import MLPRegressor
MLP = MLPRegressor(alpha=1e-6,hidden_layer_sizes=(10, 20), random_state=10,
max_iter=100000,activation='relu')
MLP.fit(X_new,y)
y_new = MLP.predict(X_new)
# 绘制抛物线
import matplotlib.pyplot as plt
plt.scatter(X,y,c="grey")
plt.plot(X_new,y_new,c="black")
```

```python
# 计算均方误差
from sklearn.metrics import mean_squared_error
print(mean_squared_error(y,y_new))
```

代码 9-1 输出的图形如图 9-5 所示。其中灰线为原始目标数据，黑线为预测数据。均方误差为 0.5534671907156201，该模型可以有效地进行抛物线逼近拟合。

![](img/cba85b47e7b62410be6bbcef421bf9c1_126_0.png)

图 9-5 拟合抛物线结果

拟合第二个函数，方程式为 $y=\cos x$，神经网络参数配置如下。

```python
y = np.sin(X) + 0.3*np.random.rand(len(X))
MLP = MLPRegressor(
    alpha=1e-6,
    hidden_layer_sizes=(10, 20),
    random_state=10,
    max_iter=100000,
    activation='relu'
)
```

修改代码 9-1，运行结果如图 9-6 所示。对不同的函数，需要的模型拟合能力是不同的。

![](img/cba85b47e7b62410be6bbcef421bf9c1_126_1.png)

图 9-6 系统函数拟合结果

对于抛物线的拟合，将隐含层大小设置为 hidden_layer_sizes (2, 2)，对应的程序修改如下。

```python
MLP = MLPRegressor(
    alpha=1e-6,
    hidden_layer_sizes=(2, 2),
    random_state=10,
    max_iter=100000,
    activation='relu'
)
```

重新运行代码 9-1，结果如图 9-7 所示。明显可以发现，减小隐含层大小后的拟合效果变差了，均方误差为 11.320189326103405。从图 9-7 中可以看出，拟合的结果并没有之前平滑，由此说明隐含层越少，拟合的效果越差。

![](img/cba85b47e7b62410be6bbcef421bf9c1_127_0.png)

图 9-7 调整参数之后的抛物线拟合结果

输出训练后得到每层的权重参数，第一层为 2 个参数，第二层为 2 x 2=4 个参数，最后一层为 2 个参数。

```python
layer = 0
for w in MLP.coefs_:
    cengindex += 1
    print(layer, w.shape, w)
```

输出结果如下。

```
0 (1, 2) [[ 1.99537275  -1.84565219]]
0 (2, 2) [[-2.27061811e-06  1.63467648e+00]
          [-7.48914833e-04  1.76601096e+00]]
0 (2, 1) [[4.61343773e-04]
          [4.08913520e+00]]
```

## 任务 9-3 使用人工神经网络算法实现鸢尾花分类

### 任务描述

前面的项目中对鸢尾花数据集作了详细的介绍，可以使用多种不同的算法对其分类。本任务使用人工神经网络算法解决鸢尾花分类问题。

### 任务目标

掌握用人工神经网络算法实现鸢尾花分类的基本步骤。

### 任务实施

根据前面讲解的人工神经网络的基础知识，使用人工神经网络实现鸢尾花分类。

> 【代码 9-2】

```python
from sklearn import datasets
iris = datasets.load_iris()
X = iris.data
y = iris.target
# 数据集划分
from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.25)
# 数据归一化
from sklearn.preprocessing import StandardScaler
scaler = StandardScaler()
X_train = scaler.fit_transform(X_train)
X_test = scaler.transform(X_test)
# 训练模型
from sklearn.neural_network import MLPClassifier
MLP = MLPClassifier(hidden_layer_sizes=(20, 8), max_iter=100000)
MLP.fit(X_train,y_train)
y_predict = MLP.predict(X_test)
# 输出分类结果
from sklearn.metrics import roc_curve, roc_auc_score, classification_report,
accuracy_score, confusion_matrix
train_accuracy = accuracy_score(y_test, y_predict)*100
print('混淆矩阵:\n', confusion_matrix(y_test, y_predict))
print(train_accuracy)
from sklearn.metrics import classification_report
print(classification_report(y_test, y_predict))
```

输出结果如下。

混淆矩阵：
[[19 0 0]
 [ 0 10 0]
 [ 0 0 9]]
100.0

| | precision | recall | f1-score | support |
|---|---|---|---|---|
| 0 | 1.00 | 1.00 | 1.00 | 19 |
| 1 | 1.00 | 1.00 | 1.00 | 10 |
| 2 | 1.00 | 1.00 | 1.00 | 9 |

| accuracy | | | 1.00 | 38 |
| macro avg | 1.00 | 1.00 | 1.00 | 38 |
| weighted avg | 1.00 | 1.00 | 1.00 | 38 |

上述代码主要经过数据集划分、数据归一化、训练模型和输出结果这几个主要流程。继续更改隐含层数量，来观察一下对结果产生的影响，隐含层大小设置为 (20, 3)。

```python
MLP = MLPClassifier(
    hidden_layer_sizes=(20,3),
    max_iter=100000
)
```

输出结果如下。

混淆矩阵：
[[10 0 0]
 [ 0 12 1]
 [ 0 1 14]]
94.73684210526315

| | precision | recall | f1-score | support |
|---|---|---|---|---|
| 0 | 1.00 | 1.00 | 1.00 | 10 |
| 1 | 0.92 | 0.92 | 0.92 | 13 |
| 2 | 0.93 | 0.93 | 0.93 | 15 |

| accuracy | | | 0.95 | 38 |
| macro avg | 0.95 | 0.95 | 0.95 | 38 |
| weighted avg | 0.95 | 0.95 | 0.95 | 38 |

设置隐含层大小为 (10, 3)。

```python
MLP = MLPClassifier(
    hidden_layer_sizes=(10,3),
    max_iter=100000
)
```

输出结果如下。

混淆矩阵：
[[16 0 0]
 [0 10 1]
 [0 0 11]]
97.36842105263158

| | precision | recall | f1-score | support |
|---|---|---|---|---|
| 0 | 1.00 | 1.00 | 1.00 | 16 |
| 1 | 1.00 | 0.91 | 0.95 | 11 |
| 2 | 0.92 | 1.00 | 0.96 | 11 |
| accuracy | | | 0.97 | 38 |
| macro avg | 0.97 | 0.97 | 0.97 | 38 |
| weighted avg | 0.98 | 0.97 | 0.97 | 38 |

由如上结果可知，设置不同的隐含层大小获得的结果也不尽相同。

## ◆ 项目小结 ◆

本项目首先介绍了人工神经网络基本原理，前向传播与反向传播的具体计算；其次通过人工神经网络来拟合曲线，并更改网络参数来观察对拟合曲线的影响；最后将其用于鸢尾花的分类案例中。

## ◆ 练习题 ◆

### 一、选择题

- 1. 以下说法中正确的是（ ）。
- A. 一个神经元可以有一个输入端和一个输出
- B. 一个神经元可以有多个输入端和一个输出
- C. 一个神经元可以有一个输入端和多个输出
- D. 一个神经元可以有多个输入端和多个输出
- E. 上述都正确

- 2. 在神经网络中，求解每一个神经元的权重和偏差是最重要的一步，以下（ ）可以获得神经元的权重和偏移。
- A. 穷举法，直到得到最佳值
- B. 赋予一个初始值，然后根据误差使用反向传播方法不断迭代调整权重
- C. 随机赋值
- D. 以上都不正确

- 3. 现在需要计算 3 个矩阵 A、B、C 的乘积 ABC，假设三个矩阵的维度分别为 m x n, n x p, p x q，且 m < n < p < q，以下计算顺序效率最高的是（ ）。
- A. (AB)C
- B. AC(B)
- C. A(BC)
- D. 所有效率都相同

- 4. 为了避免神经网络陷入局部最小值里，可以采取（ ）策略。
- A. 动态调整学习率
- B. 一开始将学习率降至原来的 1/10，然后用动量项（momentum）
- C. 增加参数数目
- D. 以上都不对

- 5. Sklearn 库中用于神经网络回归和分类的函数分别是（ ）和（ ）。
- A. MLPClassifier
- B. MLPRegressor
- C. DecisionTreeClassifier
- D. DecisionTreeRegressor

### 二、简答题

- 1. 简述人工神经网络的使用场景。

- 2. 简述人工神经网络权重调整训练的过程。

- 3. 写出 Sigmoid 函数，并对其进行求导。

- 4. 写出 3 个常见的神经网络激活函数。

## 基于 AdaBoost 算法的应用实践

### 项目导读

AdaBoost（Adaptive Boosting，自适应提升）是一种非常典型的提升（Boosting）算法，属于迭代增强算法。AdaBoost 的基本思想是在同一个数据集下训练出多个弱学习器，将多个弱学习器集成起来组合成一个强分类器，即充分利用多个弱学习器的优势组合成一个强学习器。一般情况下，在训练过程中需要进行多次迭代，每一次迭代都会改变样本的概率分布，通过突出样本的权值来更好地进行下一次迭代训练。

### 学习目标

- 掌握 AdaBoost 算法的基本原理。
- 掌握 AdaBoost 算法的分类实现步骤。
- 掌握 AdaBoost 算法的回归实现步骤。

### 知识导图

基于 AdaBoost 算法的应用实践
├── 使用AdaBoost算法原理知识进行分类器计算
│   ├── 了解AdaBoost算法的步骤
│   ├── 了解AdaBoost算法的特点
│   └── 使用AdaBoost算法实现分类器计算
├── 使用AdaBoost算法实现鸢尾花分类问题
│   ├── 使用决策树分类器实现鸢尾花分类
│   └── 使用SVM分类器实现鸢尾花分类
├── 使用AdaBoost算法实现人脸识别
└── 使用AdaBoost算法实现曲线预测

## 任务 10-1 使用 AdaBoost 算法原理知识进行分类器计算

### 任务描述

首先了解 AdaBoost 算法的基本原理，其次通过 AdaBoost 算法实现分类器计算。

### 任务目标

掌握分类器计算的基本实现步骤。

### 知识准备

### 认识 AdaBoost 算法

AdaBoost 是一种集成学习算法，可用于分类或回归。虽然 AdaBoost 比多数机器学习算法更能防止过拟合，但它对噪声很敏感。

AdaBoost 之所以称为自适应，是因为它使用多次迭代来生成单个复合强学习器。AdaBoost 通过迭代添加弱分类器（与真实分类器仅略微相关的分类器）来创建强学习器（与真实分类器相关性良好的分类器）。在每一轮训练中，一个新的弱学习器被添加到集合中，并调整一个权重向量以关注在前几轮被错误分类的例子。最终结果比弱学习器具有更高的准确度。

AdaBoost 算法的基本思想如图 10-1 所示。

集成学习结合了多种出色算法，从而形成一种优化的预测算法。集成方法不是希望在一个决策树上做出正确的调用，而是采用几种不同的树并将它们聚合成一个最终的、强大的预测器。

![](img/cba85b47e7b62410be6bbcef421bf9c1_133_0.png)

![](img/cba85b47e7b62410be6bbcef421bf9c1_133_1.png)

图 10-1 AdaBoost 算法的基本思想

### 任务实施

本节主要围绕 AdaBoost 算法的基本原理进行阐述，主要包括 AdaBoost 算法的基本概述、AdaBoost 算法的实现步骤、AdaBoost 算法的特点，并通过具体的例子来解释 AdaBoost 算法的原理。
