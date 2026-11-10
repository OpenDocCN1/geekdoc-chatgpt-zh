df.groupby(col1)[col2].mean(): 依据 col1 进行分组后，对 col2 计算平均水平
df.pivot_table(index=col1, values=[col2,col3], aggfunc=max): 以 col1 为索引构建透视表，并分别取 col2 与 col3 的最大值
df.groupby(col1).agg(np.mean): 按 col1 分组后，给全部数值列求均值
data.apply(np.mean): 默认按列方向对 DataFrame 逐列执行 np.mean
data.apply(np.max, axis=1): 指定 axis=1 后，按行方向对 DataFrame 逐行执行 np.max

### 8) 数据拼接
df1.append(df2): 将 df2 的全部行追加到 df1 末尾
pd.concat([df1, df2],axis=1): 在列方向拼接，让 df2 的列接在 df1 右侧
df1.join(df2,on=col1,how='inner'): 以 col1 为键对两个表做内连接，效果等同 SQL JOIN
pd.merge(df1,df2): 执行两表的默认合并操作

### 9) 数据概览
- `df.describe()`: 输出数值列的集中趋势与离散程度汇总
- `df.mean()`: 求出各列的平均值
- `df.corr()`: 生成列与列之间的相关系数矩阵
- `df.count()`: 统计每列中非空条目的数量
- `df.max()`: 拿到各列的最高值
- `df.min()`: 拿到各列的最低值
- `df.median()`: 计算各列的中间位置数值
- `df.std()`: 计算各列的波动标准差

## 3. Matplotlib 可视化
Matplotlib 是 Python 生态里绘制精美图表的核心库，主攻二维图形的呈现。若要做三维绘图，可额外引入 mplot3d 插件进行扩展。

### 1) 数据准备
[PRE0]python
# 一维数据

import numpy as np

x=np.linspace(0, 10, 100)

y=np.cos(x)

z=np.sin(x)

# 二维数据或图像

data=2 * np.random.random((10, 10))

data2=3 * np.random.random((10, 10))

Y, X=np.mgrid[-3:3:100j, -3:3:100j]

U=-1 -X**2 +Y

V=1 +X -Y**2

from matplotlib.cbook import get_sample_data

img=np.load(get_sample_data('axes_grid/bivariate_normal.jpg'))
[PRE1]
[PRE2]
[PRE3]

### 3) 绘图示例
[PRE4]

### 4) 图表定制
[PRE5]
[PRE6]

### 5) 导出图片

### 6）显示与窗口管理
[PRE7]

## 4. Scikit-learn 机器学习
Scikit-learn 是一个开源的 Python 机器学习工具箱，内置大量经典算法，同时提供数据预处理、交叉验证与可视化等配套能力。

### 1）数据加载
[PRE8]

### 2）训练测试划分
[PRE9]

### 3）特征预处理
[PRE10]
[PRE11]

### 4) 模型构建
[PRE12]

### 5) 模型训练
[PRE13]

### 6) 模型预测
[PRE14]

### 7) 模型评估
[PRE15]

## 8) 模型调优
[PRE16]
[PRE17]

## 9) 实战示例