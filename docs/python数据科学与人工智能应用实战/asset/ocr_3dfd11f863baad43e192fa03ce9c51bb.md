上述程序代码指定 loc 参数值 1 的位置值，参数值也可以使用位置字符串 upper right（右上角），如下所示：

```
plt.legend(loc="upper right")
```

loc 参数值的位置字符串和整数值及说明如表 10-4 所示。

# 表 10-4 loc 参数值的位置字符串和整数值及说明

| 字符串值 | 整数值 | 说明 |
|---|---|---|
| "best" | 0 | 最佳位置 |
| 'upper right' | 1 | 右上角 |
| 'upper left' | 2 | 左上角 |
| 'lower left' | 3 | 左下角 |
| 'lower right' | 4 | 右下角 |
| 'right' | 5 | 右边 |
| 'center left' | 6 | 左边中间 |
| 'center right' | 7 | 右边中间 |
| 'lower center' | 8 | 下方中间 |
| 'upper center' | 9 | 上方中间 |
| 'center' | 10 | 中间 |

# 10.1.5 指定轴的范围

Matplotlib 包默认自行使用数据判断 x 轴和 y 轴的范围，以便输出 x 轴和 y 轴标尺的刻度。当然，也可以自行指定 x 轴和 y 轴的范围。

# 1. 输出轴的范围： Ch10_1_5.py

调用 axis() 函数输出 Matplotlib 自动计算出的轴范围，如下所示：

```
days = range(0, 22, 3)
celsius = [25.6, 23.2, 18.5, 28.3, 26.5, 30.5, 32.6, 33.1]
plt.plot(days, celsius)
print("轴范围: ", plt.axis())
plt.show()
```

上述程序代码在输出轴范围后，才会输出绘制的图表。其运行结果如图 10-10 所示。
图 10-10 上方是轴范围，依次是 x 轴最小值、x 轴最大值、y 轴最小值和 y 轴最大值。

# 2. 指定轴的自定义范围： Ch10_1_5a.py

如果觉得自动计算出的轴范围并不符合预期，则可以调用 axis() 函数自行指定 x 轴和 y