代码段[9]使用Seaborn的函数barplot绘制点数的出现次数。执行代码段时，将显示以下窗口（因为前面使用了--matplotlib选项启动IPython）。

![](bbox=[0.306, 0.18, 0.703, 0.42])

Matplotlib模块创建Matplotlib Axes对象，在对象的管理窗口中，交互使用Seaborn模块来显示柱状图。在后台，Seaborn模块使用Matplotlib Figure对象来管理Axes的可视窗口。函数barplot的前两个参数是M维数组对象ndarray，分别包含x轴和y轴值。我们使用可选的palette关键字参数，选择Seaborn的预定义调色板'bright'。在下面网页中可以查看完整的调色板选项：

https://seaborn.pydata.org/tutorial/color_palettes.html

函数barplot返回可配置的Axes对象，我们将其命名为变量axes，可以使用它来配置其他选项，生成最终的绘图。执行相应的代码段后，对柱状图所做的任何更改都会立刻显示出来。

# 设置窗口标题并标记x和y轴

接下来用两个代码段为柱状图添加一些描述性文本：

```
In [10]: axes.set_title(title)
Out[10]: Text(0.5,1,'Rolling a Six-Sided Die 600 Times')

In [11]: axes.set(xlabel='Die Value', ylabel='Frequency')
Out[11]: [Text(92.6667,0.5,'Frequency'), Text(0.5,58.7667,'Die Value')]
```