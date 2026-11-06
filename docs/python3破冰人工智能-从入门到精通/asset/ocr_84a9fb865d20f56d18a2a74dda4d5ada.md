注：你可以把 fig 想象成 Windows 的桌面，桌面可以有多个。这样，axes 就是桌面上的图标，subplot 也是图标，它们的区别在于 axes 是自由摆放的图标，甚至可以相互重叠，而 subplot 是“自动对齐到网格”的图标。但它们本质上都是图标，也就是说 subplot 内部其实也是调用的 axes，只不过规范了各个 axes 的排列罢了。

# 小贴士：

# Matplotlib 中文显示问题

使用 matplotlib 时你会发现，有时图例等设置无法正常显示中文，原因是 matplotlib 库中没有中文字体。

添加如下代码即可实现中文显示：

```
plt.rcParams['font.sans-serif']=['SimHei'] #来显示中文标签
plt.rcParams['axes.unicode_minus']=False #来正常显示负号
```

# 4.2.3 Matplotlib 绘图案例

利用 NumPy 与 Matplotlib，对比两组高斯分布的样本，代码见例 4-11。

# 【例 4-11】对比高斯分布图，程序运行结果如图 4-5 所示

输入：

```
#对比两组高斯分布的样本
import numpy as np
from matplotlib import pyplot as plt

#np.random.normal 构建两组观测数据
samples1 = np.random.normal(0, size=1000)
samples2 = np.random.normal(1, size=1000)

#绘制样本直方图
bins = np.linspace(-4, 4, 30)
histogram1, bins = np.histogram(samples1, bins=bins, normed=True)
histogram2, bins = np.histogram(samples2, bins=bins, normed=True)
#绘制图像，并显示
plt.figure(figsize=(6, 4))
plt.hist(samples1, bins=bins, normed=True, label="Samples 1")
plt.hist(samples2, bins=bins, normed=True, label="Samples 2")
plt.legend(loc='best')
plt.show()
```

输出：

正态分布（Normal distribution）又名高斯分布（Gaussian distribution），是一个在数学、物理及工程等领域都非常重要的概率分布，也是对于数据分布来说最常见的分布（比如，学生的每次考试成绩理论上都上服从高斯分布的），其在统计学的许多方面都有着重大的影响力。例 4-11 中构建了两组高斯分布数据，之后利用 Matplotlib 进行图形绘制。