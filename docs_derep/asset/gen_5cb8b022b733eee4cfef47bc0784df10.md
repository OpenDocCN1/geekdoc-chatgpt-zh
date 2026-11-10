[PRE0]

输入：

[PRE1]

输出：

[PRE2]

输入：

[PRE3]

输出：

[PRE4]

在数据清洗阶段，当特征数量过多时，通常会剔除贡献度低的弱特征。针对 pandas DataFrame 中重复行的处理，常用的两种手段是 duplicated 与 drop_duplicates，且都支持指定用于判断重复的列。

- duplicated：生成一个与行数等长的布尔序列，用来标记哪些行是重复的。
- drop_duplicates：直接剔除重复行，返回去重后的 DataFrame。

默认情况下，重复组里首次出现的记录被视作唯一，后续出现才被判定为重复。通过 keep 参数可以调整保留策略：

- keep='first' 为默认值，只保留首次出现，后续标记/删除。
- keep='last' 则保留最后一次出现，其余标记/删除。
- keep=False 会把整组的全部记录都标记/删除。

### 【例4-8】标识和删除重复行

输入：

[PRE5]

输出：

[PRE6]

调用 df2.duplicated() 得到一个 Series，其中 d 行和 g 行的内容全为 nan，完全相同，因此被标记为 True，其余行均为 False。

输入：

[PRE7]

输出：

[PRE8]

输入：

[PRE9]

输出：

[PRE10]

输入：

[PRE11]

输出：

[PRE12]

输入：

[PRE13]

输出：

[PRE14]

## 4.2 Matplotlib 可视化工具

### 4.2.1 Matplotlib 快速认识

Matplotlib 是 Python 生态里使用最广泛的绘图包之一，能轻松绘制大量二维图表，也能处理部分三维可视化需求。它最初是为了呈现癫痫患者脑皮层电图信号而开发，因其 API 设计借鉴 Matlab 而得名。在社区推动下，如今已成为科学计算领域的主流绘图方案。库的创始人 John D. Hunter 博士是神经生物学家，2012 年因癌症离世，留下了这一经典项目。

[PRE15]

▲图4-1 John D. Hunter

### 【例4-9】查看 Matplotlib 版本

输入：

[PRE16]

输出：

[PRE17]

在 Python 中，最常见的调用方式是 import matplotlib.pyplot，引入快速绘图子模块 pyplot。因为 Matplotlib 采用面向对象架构，图表的每个组成部分都是对象。面向对象方式在大型项目里更可控，但写法相对繁琐，所以日常更多使用简便的快捷指令。

### 【例 4-10】初识 Matplotlib 操作，程序运行结果如图 4-2 所示

输入：

[PRE18]

① matplotlib 通过 pyplot 模块提供了一套和 Matlab 类似的绘图 API，将众多绘图对象所构成的复杂结构隐藏在这套 API 内部。

输出：

[PRE19]

▲图 4-2 例 4-10 程序运行结果

### 小贴士：

Matplotlib 还附带 pylab 模块，集合了 NumPy 与 pyplot 的常用函数，便于在 IPython 交互环境中快速计算和画图。常用别名为 pl，导入写法为 import pylab as pl。

### 4.2.2 Matplotlib 基础操作

在 Matplotlib 里，一张完整的图对应一个 Figure 对象，一个 Figure 可包含一个或多个 Axes 对象，每个 Axes 拥有独立的坐标系，具体层级如图 4-3。

[PRE20]

▲图 4-3 Matplotlib 对象

如图 4-3 所示，Figure 是整张图像的容器，本例只包含一个坐标区 Axes。每个 ax 都是可独立绘图的区域，其内部结构如图 4-4。

[PRE21]

▲图 4-4 Axes 对象

以折线图为例，拆解出图像的各个部件，见表 4-9。

### 表 4-9 图像内部各个组件内容

| 组件 | 说明 |
|---|---|
| Figure | 整个图像 |
| Axes | 轴，灵活的子图 |
| Grid | 网格 |
| Title | 标题 |
| Legend | 图例 |
| line | 图像中的线 |
| markers | 图像中的点 |
| Spines | 连接轴刻度标记的线，而且标明了数据区域的边界 |
| Major tick | 主刻度 |
| Minor tick | 分刻度 |
| Major tick label | 主刻度标签 |
| Y axis label | y 轴标签 |
| Minor tick label | 分刻度标签 |
| X axis label | x 轴标签 |

注：可以把 fig 视作 Windows 桌面，可存在多个。axes 相当于桌面上的图标，subplot 也是图标，区别在于 axes 可自由摆放甚至重叠，subplot 则按网格自动对齐。本质上 subplot 也是 axes，只是对排列做了规范。

### 小贴士：

### Matplotlib 中文显示问题

使用 matplotlib 时你会发现，有时图例等设置无法正常显示中文，原因是 matplotlib 库中没有中文字体。

添加如下代码即可实现中文显示：

[PRE22]

### 4.2.3 Matplotlib 绘图案例

借助 NumPy 与 Matplotlib，绘制两组高斯分布样本进行对比，代码见例 4-11。

### 【例 4-11】对比高斯分布图，程序运行结果如图 4-5 所示

输入：

[PRE23]

输出：

正态分布也叫高斯分布，在数学、物理、工程等领域应用极广，是数据中最常见的分布形态之一，例如学生的考试成绩在理论上常被假设为高斯分布。它对统计学产生深远影响。例 4-11 先生成两组高斯数据，再用 Matplotlib 绘图。

[PRE24]

▲图 4-5 例4-11 程序运行结果

接下来做一个饼图，假设有四种动物 Duck、Dog、Cow、Pig，占比分别为 15%、30%、45%、10%。数据分析时光看表格不够，直观的可视化更有帮助，例 4-12 用饼图展示。

### 【例 4-12】绘制饼形图案例，程序运行结果如图 4-6 所示

输入：

[PRE25]

输出：

[PRE26]

▲图 4-6 例 4-12 的运行结果

除了基础饼图外，这里还演示了标签显示、百分比自动标注、爆炸式分离、阴影下拉以及起始角度自定义等可选效果。

## 4.3 SciPy 科学计算库

### 4.3.1 初识 SciPy

SciPy 在 NumPy 基础上扩展了大量数学、科学和工程计算常用函数，紧密依赖 NumPy 提供快速 n 维数组运算。它与 NumPy 数组协同工作，提供高效易用的处理接口，涵盖统计、优化、积分、线性代数、傅里叶变换、信号与图像处理、常微分方程求解等，功能非常全面。

### 【例 4-13】查看 SciPy 版本

输入：

[PRE27]

输出：

[PRE28]

SciPy 按功能划分为多个子包，具体见表 4-10。

表 4-10 SciPy 模块功能表

| 模块 | 功能 |
|---|---|
| scipy.cluster | 矢量量化/Kmeans |
| scipy.constants | 物理和数学常数 |
| scipy.fftpack | 傅里叶变换 |
| scipy.integrate | 积分 |
| scipy.interpolate | 插值 |