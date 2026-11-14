接下来，我们使用Seaborn的scatterplot函数和Matplotlib的plot函数绘制数据集的散点图。首先，对nyc数据的DataFrame使用scatterplot函数来显示数据点：

```
```python
In [21]: import seaborn as sns
In [22]: axes = sns.scatterplot(data=nyc, x='Date', y='Temperature',
    ...:                            hue='Temperature', palette='winter', legend=False)
    ...:
```
```

其中，关键字参数为：

+   - data，指定包含所要显示数据的DataFrame（nyc）。
+   - x和y，分别指定为nyc的行名和列名，x轴为'Date'，y轴为'Temperature'。行列的对应值构成了用于绘制散点的x-y坐标对。
+   - hue，指定应使用哪一列的数据来确定散点的颜色，这里使用'Temperature'列。在这个例子中，颜色并不是特别重要，但我们想要为图添加一些视觉效果。
+   - palette，指定从Matplotlib颜色表中选择散点的颜色。
+   - legend = False，指定scatterplot函数不应显示图例—它默认为True，但在这个示例中不需要图例。

正如在第10章中所做的，缩放y轴的值的范围，这样一旦显示出回归线，就能更好地看到线性关系：

```
```python
In [23]: axes.set_ylim(10, 70)
Out[23]: (10, 70)
```
```

接下来，显示回归线。首先，在nyc.Date中创建一个包含最小和最大日期值的数组，它们是回归线的起点和终点的x坐标：

```
```python
In [24]: import numpy as np
In [25]: x = np.array([min(nyc.Date.values), max(nyc.Date.values)])
```
```

将数组x传递给代码段 [18] 中的lambda表达式，会产生一个包含相应预测值的数组，其作为y坐标的值：

```
```python
In [26]: y = predict(x)
```
```