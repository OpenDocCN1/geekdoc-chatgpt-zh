如下所示。

+   • MSE（Mean Squared Error）：MSE 可以告诉我们数据集的点如何接近回归线，即测量各点至回归线的距离（这些距离称为误差）的平方和后，计算出平均值。因为是误差，所以值越小，模型越好。

+   • R-squared（R²）：R-squared 也称为决定系数（Coefficient of Determination），可以告诉我们数据集如何符合回归线，R-squared 的值是 0～1，即反应变量的变异比例。可以使用 Scikit-learn 的 score() 函数计算 R-squared，其值越大，模型就越好。

现在，可以计算 MSE 和 R-squared 来输出预测模型的绩效（Python 程序：Ch15_3_3a.py），如下所示：

```
...
lm = LinearRegression()
lm.fit(XTrain, yTrain)
pred_train = lm.predict(XTrain)
pred_test = lm.predict(XTest)
MSE_train = np.mean((yTrain-pred_train)**2)
MSE_test = np.mean((yTest-pred_test)**2)
print("训练数据的 MSE:", MSE_train)
print("测试数据的 MSE:", MSE_test)
```

上述程序代码分别计算训练数据集和测试数据集的 MSE，yTrain 和 yTest 是房价，在减去 pred_train 和 pred_test 且平方后，调用 mean() 函数计算算术平均值。然后计算 R-squared，如下所示：

```
print("训练数据的 R-squared:", lm.score(XTrain, yTrain))
print("测试数据的 R-squared:", lm.score(XTest, yTest))
```

上述程序代码调用 score() 函数计算训练数据集和测试数据集的 R-squared，其运行结果如下所示：

```
训练数据的 MSE: 19.54675847353467
测试数据的 MSE: 28.541367275619667
训练数据的 R-squared: 0.7551332741779998
测试数据的 R-squared: 0.6955388005506267
```

Python 程序：Ch15_3_3b.py 是修改第 15.3.2 节的 Ch15_3_2c.py 程序，新增计算 MSE 和 R-squared 的程序代码，如下所示：

```
MSE = np.mean((y-predicted_price)**2)
print("MSE:", MSE)
```