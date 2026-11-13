| | f tracking | happiness |
|---|---|---|
| 0 | 110 | 0.3 |
| 1 | 1018 | 0.8 |
| 2 | 1130 | 0.5 |
| 3 | 417 | 0.4 |
| 4 | 626 | 0.6 |

# 图 13-6

| | f tracking s | happiness s |
|---|---|---|
| 0 | -1.636807 | -0.870370 |
| 1 | 0.541891 | 1.444444 |
| 2 | 0.810629 | 0.055556 |
| 3 | -0.900176 | -0.407407 |
| 4 | -0.398692 | 0.518519 |

# 图 13-7

现在，因为数据已经标准化，所以可以绘制散布图，如下所示：

```
df_scaled.plot(kind="scatter", x="f_tracking_s", y="happiness_s")
```

上述程序代码绘制数据标准化后 DataFrame 对象的散布图，如图 13-8 所示。

![](bbox=[0.335, 0.306, 0.706, 0.497])

# 图 13-8

Python 程序：Ch13_2_1a.py 是使用 StandardScaler 对象来进行数据标准化，如下所示：

```
scaler = preprocessing.StandardScaler()
np_std = scaler.fit_transform(df)
df_std = pd.DataFrame(np_std, columns=["f_tracking_s", "happiness_s"])
print(df_std.head())
```

上述程序代码首先建立 StandardScaler 对象，然后调用 fit_transform() 函数进行数据转换，即标准化 DataFrame 对象的数据。

# 13.2.2 最小-最大值缩放

最小-最大值缩放（Min-Max Scaling）是另一种常用的特征缩放方法，也称为正规化（Normalization），可以将数值数据转换成 0~1 区间，可以使用在第 15 章的线性回归和第 16 章的 K 近邻算法（K Nearest Neighbor Algorithm，KNN）。

数据正规化是使用以下公式进行最小-最大值缩放，如下所示：

\( X_{norm} = \frac{X - X_{min}}{X_{max} - X_{min}} \)