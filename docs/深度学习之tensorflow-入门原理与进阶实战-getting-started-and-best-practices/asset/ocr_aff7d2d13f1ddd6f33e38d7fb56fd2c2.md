```
```
173
174 pred = preout[:, j, k]
175
176 .label1 = "past" if k == 0 else "_nolegend_"
177 .label2 = "future" if k == 0 else "_nolegend_"
178 .label3 = "Pred" if k == 0 else "_nolegend_"
179 plt.plot(range(len(past)), past, "o--b", label=label1)
180 plt.plot(range(len(past), len(expected) + len(past)),
181     expected, "x--b", label=label2)
182 plt.plot(range(len(past), len(pred) + len(past)),
183     pred, "o--y", label=label3)
184
185 plt.legend(loc='best')
186 plt.title("Predictions vs. future")
187 plt.show()
```
```

为了跟真实的序列值比较，这里将真实的序列值也从 15 个序列开始打印出来，index=14 的值即为预测的第一个值。运行上面的代码，结果如图 9-29 所示。

![](bbox=[0.15, 0.43, 0.82, 0.56])

# a) 序列片段 1

![](bbox=[0.15, 0.6, 0.82, 0.73])

# b) 序列片段 2

![](bbox=[0.15, 0.77, 0.82, 0.9])

# c) 序列片段 3

# 图 9-29 基于 Seg2Seg 实例结果