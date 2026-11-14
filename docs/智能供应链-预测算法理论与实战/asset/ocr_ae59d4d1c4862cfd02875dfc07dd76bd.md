月 1 日的数据作为模型训练数据，预测 2022 年 1 月 1 日至 2022 年 12 月 1 日每个月的销量情况，并在完成预测后将真实销量与预测销量进行比较，计算预测准确率。选择合适的准确率指标对于需求预测非常重要，常用的准确率指标有 MAPE（Mean Absolute Percentage Error）、SMAPE（Symmetric Mean Absolute Percentage Error）等。在本节的案例分析中，我们使用 MAPE 作为准确率指标，其表达式如式（1-1）所示。通过 MAPE 可以评价预测结果，MAPE 衡量的是预测值与真实值的偏离程度，MAPE 值越小越好。完成准确率指标的选择后，接下来开始建模预测。

```
MAPE=\frac{1}{n}\sum_{t=1}^{n} \left| \frac{A_t - F_t}{A_t} \right|  (1-1)
```

式（1-1）中，\(A_t\) 为 \(t\) 时期销量的真实值，\(F_t\) 为 \(t\) 时期销量的预测值。

# # 1. 指数平滑模型

指数平滑模型的原理是将过去一段时间的销量加权平均后作为未来预测值。该模型相对简单，因此也具有较强的健壮性，是业界人工预测常用的一种方法。指数平滑的思想认为，越远的信息权重越低，并且权重是随着时间呈指数级衰减的。另外，指数平滑还有一些拓展模型，例如三次指数平滑（Holt-Winters），可以进一步对销量数据的趋势性和季节性进行拓展，从而实现更加有效的预测。具体的模型原理将在第 2 章中详细介绍，本节我们先快速实现其应用。通过 Python 的 statsmodels 包中的 ExponentialSmoothing 模块，我们可以实现模型拟合和销量预测。模型相关参数如表 1-11 所示。

表 1-11 ExponentialSmoothing 的参数列表

| 参数 | 数据类型 | 备注 |
|---|---|---|
| endog | array-like | 时间序列，即销量数据 |
| trend | {"add", "mul", "additive", "multiplicative", None} | 趋势类型，需判断模型是乘性还是加性 |
| demped | bool | 趋势分量是否要被抑制，默认为否 |
| seasonal | {"add", "mul", "additive", "multiplicative", None} | 季节类型，需判断模型是乘性还是加性 |
| seasonal_periods | int | 季节的周期长度 |

通过之前的探索性分析，我们已经对这些模型参数有了基本的了解。在本案