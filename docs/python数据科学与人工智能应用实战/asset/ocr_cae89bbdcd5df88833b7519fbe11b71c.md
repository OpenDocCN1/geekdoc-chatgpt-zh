图 15-15 中，红色点是测试数据集，蓝色点是训练数据集。数据偏离中间水平线很远的点，如下方 y=-20 上下附近的红色点和蓝色点，就是异常值。

# 15.4 Logistic 回归

Logistic 回归（Logistic Regression，也称逻辑回归）也属于回归分析大家族的一员，不同于线性回归是解决连续数值的评估和预测，Logistic 回归用于解决分类问题。

# 15.4.1 认识 Logistic 回归

Logistic 回归和线性回归虽使用相同的观念，不过其主要应用的是二元性数据，如男或女、成功或失败、真或假等。所以，Logistic 回归和线性回归的不同是它在解决分类问题。

实际上，Logistic 回归的做法和线性回归相同，只不过其结果需要使用 logistic 函数或称 sigmoid 函数（S 函数）转换成 0~1 的概率，其公式如下所示：

\( S(t) = \frac{1}{(1+e^{-t})} \)

上述 sigmoid 函数可以使用 Matplotlib 包绘制图形（Python 程序：Ch15_4_1.py），如下所示：

```
import numpy as np
import matplotlib.pyplot as plt

t = np.arange(-6, 6, 0.1)
S = 1/(1+(np.e**(-t)))

plt.plot(t, S)
plt.title("sigmoid function")
plt.show()
```

上述程序代码运用之前公式，从其运行结果中可以看到 sigmoid 函数的图形，如图 15-16 所示。

![](bbox=[0.3, 0.73, 0.65, 0.91])

图 15-16