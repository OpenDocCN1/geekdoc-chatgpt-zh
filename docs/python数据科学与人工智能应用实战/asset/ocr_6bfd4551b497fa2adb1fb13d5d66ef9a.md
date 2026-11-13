```
ax2.set_ylabel("Sinh(x)", color="blue")
plt.xticks(range(0, 11))
ax.set_yticks(np.linspace(-1, 1, 10))
ax2.set_yticks(np.linspace(0, 12000, 10))
plt.show()
```

上述程序代码只更改了 2 个 y 轴的自定义刻度，所以调用 2 次 set_yticks() 函数，参数值是新刻度。其运行结果如图 10-34 所示。

![](bbox=[0.27, 0.25, 0.75, 0.46])

图 10-34

# 10.4 Pandas 包的数据可视化

数据可视化是使用多种图表来呈现数据。因为一张图形胜过千言万语，可以让我们更有效率地与其他人进行沟通，即数据可视化可以让复杂数据更容易呈现想要表达的信息，也更容易让我们了解这些数据代表的意义。

实际上，除了使用 Matplotlib 包绘制各种图表外，第 9 章介绍的 Pandas 包本身也支持函数来绘制图表，一样可以将数据可视化呈现。

# 10.4.1 Series 对象的数据可视化

Pandas 包的 Series 和 DataFrame 对象都支持 plot() 函数的绘制图表功能，本小节先介绍 Series 对象，下一小节再介绍 DataFrame 对象。

+   1. 使用 Series 对象绘制折线图：Ch10_4_1.py

只需使用数值列表建立 Series 对象，即可调用 plot() 函数绘制折线图，如下所示：

```
import pandas as pd

data = [100, 110, 150, 170, 190, 200, 220]
```