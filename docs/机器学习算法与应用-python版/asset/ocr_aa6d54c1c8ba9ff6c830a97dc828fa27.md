```
y_predict = reg.predict(x_test)
# 第四步: 输出结果
from sklearn.metrics import mean_squared_error
print(mean_squared_error(y_test, y_predict))
print(reg.score(x_train, y_train))
print(reg.score(x_test, y_test))
```

结果输出如下。

```
29.824006898863182
0.7570401119326386
0.6783942923302058
```

代码 5-2 使用线性回归对多元房价数据进行了模型的训练和预测，其中 LinearRegression 同样也不用设置额外的参数就可以进行训练。

# 任务 5-3 使用多项式扩展实现曲线预测

### 任务描述

使用多项式扩展进行曲线预测。

### 任务目标

熟练掌握多项式扩展的作用，及在 Sklearn 中如何使用多项式扩展方法。

# 任务实施

在实际的数据里，有时数据在变量之间具有非线性的关系。在非线性关系中，与自变量的一个单位偏移相关的因变量的变化，因观察空间中的位置而变化。本任务将使用多项式扩展来进行曲线的拟合，多项式扩展可以对现有数据进行转换，将数据转换到更高维度的空间中，使模型拟合性能更强。

【代码 5-3】

```
# 第一步: 数据装载
import numpy as np
X = np.linspace(-10, 10, 400)
Y = np.sin(X) + 0.1*np.random.rand(len(X))
X = X.reshape(-1, 1)
Y = Y.reshape(-1, 1)
# 第二步: 多项式扩展进行模型训练
```