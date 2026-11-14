动的统计量标准差占平均值的比例；也可以选择一些简单的方法，例如以移动平均法等作为参照基准对比预测结果的准确率。因为 MAPE 值大多数时候位于 0 ~ 1 之间，因此在实践中为了更直观，可以使用 1—MAPE 作为最终展示结果。1—MAPE 越靠近 1 代表预测结果越好。从下面的代码中，我们看到本次预测的准确率指标 1—MAPE 为 0.776。

```
1. # 准确率指标计算
2. def mape(y,y_hat):
3.     ...
4.     :param y: 实际销量数据，float
5.     :param y_hat: 预测销量数据，float
6.     :return: 是否为趋势的结果，str
7.     ...
8.     if y == 0 and y_hat ==0:
9.         return 0
10.    elif y==0 or y_hat ==0:
11.        return 1
12.    return np.abs(y-y_hat)/np.abs(y)
13.
14. # 抽取测试集，和预测结果拼接
15. test_df = order_df[['order_date','y']][-12:]
16. test_df['y_hat'] = ets_y_hat.values
17. # 计算准确率
18. print(1-np.mean(test_df[['y','y_hat']].apply(\n19.         lambda x:mape(x[0],x[1]),axis=1))) # 0.7768992944955736
```

另一种方式是通过折线图查看预测结果。我们可以使用不同的颜色在同一张图上绘制预测结果和实际销量情况，直观地比较预测结果和实际情况的差异，如图 1-8 所示。

```
1. # 通过绘图查看预测情况
2. ax = y_data.plot(figsize=(10,6),\n3.     marker='o', color='black', title="Forecasts result" , legend=True)
4. ax.set_ylabel("quantity")
5. ax.set_xlabel("date")
6.
7.
8. es_fit.forecast(12).rename('EMS Forecast').plot("
```