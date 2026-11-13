```
import pandas as pd

fruits = ["Apple", "Pears", "Bananas", "Orange"]
percentage = [30, 10, 40, 20]

s = pd.Series(percentage, index=fruits, name="Fruits")
print(s)
explode = [0.1, 0.3, 0.1, 0.3]
s.plot(kind="pie",
    figsize=(6, 6),
    explode=explode)
```

上述程序代码中，explode 列表对应各切片的突增值；plot() 函数使用 kind 属性指定 pie 饼图，figsize 属性指定尺寸长宽相同（正圆），explode 属性是突增值。其运行结果如图 10-45 所示。

![](bbox=[0.35, 0.4, 0.7, 0.62])

图 10-45

# 10.4.6 Pandas 包的散布图和箱形图

Pandas 包的 plot() 函数也支持绘制散布图和箱形图（Box plot），只需建立 DataFrame 对象，即可指定字段来绘出散布图和箱形图。

+   1. 散布图: Ch10_4_6.py

将第 10.2.1 节由 sin() 函数绘制的散布图改用 Pandas 包的 plot() 函数来绘制，如下所示：

```
x = np.linspace(0, 2*np.pi, 50)
y = np.sin(x)
df = pd.DataFrame({"x":x, "y":y})
df.plot(kind="scatter", x="x", y="y",
    title="Sin(x)")
```