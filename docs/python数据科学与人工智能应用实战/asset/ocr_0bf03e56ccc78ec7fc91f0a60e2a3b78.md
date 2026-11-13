```
plt.ylabel("Predicted Price")
plt.title("Price vs Predicted Price")
plt.show()
```

上述程序代码可以绘制原来房价和预测房价的散布图，如图 15-12 所示。

![](bbox=[0.27, 0.185, 0.67, 0.43])

从图 15-12 中可以看出，当房价增加时，有一些预测房价是错误数据。例如，在 Price 约 50 时，其预测房价垂直排成一条线，这时就可以使用第 15.3.4 节的残差图（Residual Plots）来找出导致这些异常值（Outlier）的错误数据。

# 15.3.3 训练数据集和测试数据集

实际上，对于取得的数据集，我们并不会使用整个数据集来训练预测模型，通常会使用随机方式先将其切割成训练数据集（Training Dataset）和测试数据集（Test Dataset），使用训练数据集训练预测模型后，再使用测试数据集来验证模型的绩效。

+   1. 调用 train_test_split() 函数随机切割数据集

Scikit-learn 包中的 train_test_split() 函数可以帮助指定比例来随机切割数据集（Python 程序：Ch15_3_3.py），如下所示：

```
import pandas as pd
from sklearn import datasets
from sklearn.linear_model import LinearRegression
from sklearn.model_selection import train_test_split
import matplotlib.pyplot as plt

boston = datasets.load_boston()
X = pd.DataFrame(boston.data, columns=boston.feature_names)
```