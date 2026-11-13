接着建立反应变量 y 的 DataFrame 对象，如下所示：

```
```python
target = pd.DataFrame(boston.target, columns=["MEDV"])
print(target.head())
```
```

上述程序代码使用 target 键的房价建立 DataFrame 对象，字段名是 MEDV，这是数据集完整描述的最后一个属性。其运行结果如图 15-9 所示。

| | MEDV |
|---|---|
| 0 | 24.0 |
| 1 | 21.6 |
| 2 | 34.7 |
| 3 | 33.4 |
| 4 | 36.2 |

# 3. 训练预测模型

现在，可以使用波士顿数据集来训练线性复回归的预测模型（Python 程序：Ch15_3_2b.py），如下所示：

```
```python
boston = datasets.load_boston()

X = pd.DataFrame(boston.data, columns=boston.feature_names)
target = pd.DataFrame(boston.target, columns=["MEDV"])
y = target["MEDV"]

lm = LinearRegression()
lm.fit(X, y)
print("回归系数：", lm.coef_ )
print("截距：", lm.intercept_ )
```
```

上述程序代码调用 fit() 函数训练模型后，输出回归系数和截距。其运行结果如下所示：

```
```
回归系数： [-1.07170557e-01  4.63952195e-02  2.08602395e-02  2.68856140e+00
-1.77957587e+01  3.80475246e+00  7.51061703e-04 -1.47575880e+00
 3.05655038e-01 -1.23293463e-02 -9.53463555e-01  9.39251272e-03
-5.25466633e-01]
截距： 36.49110328036238
```
```

上述回归系数因为有 13 个解释变量，所以有 13 个系数。可以建立 DataFrame 对象来输出每一个特征的系数，如下所示：

```
```python
coef = pd.DataFrame(boston.feature_names, columns=["features"])
coef["estimatedCoefficients"] = lm.coef_
print(coef)
```
```

上述程序代码建立 DataFrame 对象（包括 feature_names 字段）后，新增系数的字段。其运行结果如图 15-10 所示。

从图 15-10 中可以看出 RM 特征的系数最大，表示 RM 与房价高度相关。所以，接下来绘制这 2 个数据的散布图，如下所示：