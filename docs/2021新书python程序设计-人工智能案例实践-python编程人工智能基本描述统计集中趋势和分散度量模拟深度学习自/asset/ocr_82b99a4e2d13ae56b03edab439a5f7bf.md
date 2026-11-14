# # 启动IPython实现交互式Matplotlib开发

IPython内置支持交互式开发Matplotlib图表，后面还需要开发Seaborn图表。我们只需使用以下命令启动IPython：

```
ipython --matplotlib
```

# # 导入库

首先，让我们导入将要用到的库：

```
In [1]: import matplotlib.pyplot as plt
In [2]: import numpy as np
In [3]: import random
In [4]: import seaborn as sns
```

+   1. matplotlib.pyplot模块包含Matplotlib库的图形功能。导入此模块后通常用缩写plt来表示。

+   2. NumPy（Numerical Python）库包含函数unique，用于汇总掷骰子点数。导入此模块后通常用缩写np来表示。

+   3. random模块包含Python的随机数生成函数。

+   4. seaborn模块包含Seaborn库的图形功能。导入此模块后通常用缩写sns来表示。读者可以搜索一下用这种奇特缩写的原因。

# # 掷骰子并统计点数出现频率

接下来，让我们使用列表推导式来创建包含600个随机点数的列表，然后使用NumPy的unique函数来对点数去重（很可能六个面都有）并且统计每个点数出现的次数：

```
In [5]: rolls = [random.randrange(1, 7) for i in range(600)]
In [6]: values, frequencies = np.unique(rolls, return_counts=True)
```