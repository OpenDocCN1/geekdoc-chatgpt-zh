# (1) 导入数据，提取特征

首先，通过导入 pandas 库和 matplotlib.pyplot 库，以及层次聚类算法库 scipy.cluster.hierarchy，实现了必要的功能和可视化操作。接着，通过设置参数解决了中文显示乱码问题，确保中文能够正确显示。然后，使用 pd.read_csv() 函数从名为 “data-9-5-y.csv” 的文件中读取数据，并将其存储在名为 “ourData” 的 DataFrame 中。这一步骤可以用来加载并展示数据。最后，通过使用 .iloc 函数从 “ourData” 数据框中提取特定的列数据，即年收入和消费得分，并将它们存储在名为 “newData” 的数组中，方便后续处理和分析。

# 代码 9-16 导入数据，提取特征：年收入和消费得分

```
# 导入需要的库
import pandas as pd
import matplotlib.pyplot as plt
import scipy.cluster.hierarchy as sch    # 导入层次聚类算法
# 解决中文显示乱码问题
plt.rcParams['font.sans-serif'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False

# 导入数据并显示
ourData = pd.read_csv('data-9-5.csv')
# 从文件"data-9-5-y.csv"中读取数据，并将其存储在名为"ourData"的DataFrame中

# 从数据集中提取后两个特征：
newData = ourData.iloc[:, [3, 4]].values
# 从"ourData"数据框中提取第3列和第4列的数据，并将其存储在名为"newData"的数组中
```

# (2) 绘制树状图

这段代码的作用是绘制树状图（谱系图），找到最佳的聚类数。代码首先使用 sch.linkage() 函数计算数据集 newData 中各个样本的距离，并将其传递给 sch.dendrogram() 函数，生成树状图。树状图可以帮助我们观察样本之间的距离关系和聚类情况。通过调整参数和观察树状图的形状，可以大致确定最佳的聚类数。接下来，代码设置了横坐标的标签为 “用户”，使得树状图在横轴上显示用户的标识。紧接着，代码设置了纵坐标的标签为 “欧氏距离”，说明纵轴上的数值表示样本之间的欧氏距离。最后，使用 plt.show() 函数展示绘制好的树状图。

# 代码 9-17 绘制树状图，找到聚类数

```
dendrogram = sch.dendrogram(sch.linkage(newData, method='ward'))
# 生成树状图
# 'newData' 为输入的数据集，`method='ward'` 表示使用 Ward 方法进行聚类

# plt.title('Dendrogram')  # 设置树状图的标题，此处注释掉了该行代码

plt.xlabel("用户")      # 设置横坐标标签为"用户"

plt.ylabel("欧氏距离")  # 设置纵坐标标签为"欧氏距离"

plt.show()              # 展示绘制的树状图
```