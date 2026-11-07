```
import matplotlib.pyplot as plt
import random
plt.rcParams['font.sans-serif'] = ['KaiTi']  # 用来正常显示中文标签
plt.rcParams['axes.unicode_minus'] = False  # 用来正常显示负号
plt.figure()
month_days = 5
x = [i + 1 for i in range(1, month_days + 1)]
y = [random.randrange(20, 41) for i in range(1, month_days + 1)]
plt.bar(x, y, label='金额', color=['r', 'y', 'b', 'g', 'c'])
plt.xlabel('一周(天)')
plt.ylabel('消费金额(元)')
plt.legend()
plt.xticks(x, ['星期{0}'.format(i + 1) for i in range(0, month_days + 1)])
plt.show()
```

![](bbox=[0.25, 0.22, 0.75, 0.36])

图 3-32

Matplotlib 是 Python 中最受欢迎的数据可视化软件包之一，支持跨平台运行，它是 Python 常用的 2D 绘图库，同时也提供一部分 3D 绘图接口。Matplotlib 通常与 NumPy、Pandas 一起使用，是数据分析中不可或缺的重要工具之一。