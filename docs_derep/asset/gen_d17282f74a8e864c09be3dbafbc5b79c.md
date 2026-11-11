下面动手画一画信号波形。横坐标 *x* 由 `NumPy` 的 `linspace` 生成，纵坐标 *y* 则由 `sin` 计算得到，两个函数的用法如下所示。

| `numpy.linspace(start, stop, num = 50, endpoint = True, retstep = False, dtype = None)` |

| --- |

| 参数 || 说明 |

| `start` || 所生成数列的起始值 |

| `stop` || 所生成数列的结束值 |

| `num` || 所生成数列的元素数量（默认为 50 个元素） |

| `endpoint` || 指定生成的数列中是否包含结束值 |

| `dtype` || 指定所生成数列中元素的数据类型（不指定的话，默认为 float 类型） |

| 返回值 || 包含`num`个元素的等差数列 |

图 1　`linspace`函数的语法格式

| `numpy.sin(x[, out]) = <ufunc 'sin'>` |

| --- |

| 参数 || 说明 |

| `x` || 弧度 |

| 返回值 || 指定弧度的正弦值 |

图 2　`sin`函数的语法格式

另外还要用 `math.pi` 来获取圆周率。把算好的 *x* 与 *y* 交给 `matplotlib` 的 `plot` 绘制曲线，再用 `show` 呈现即可（见图 3）。

[PRE0]

图 3　信号波的显示

[PRE1]

### 标题和轴标签的设定

图表标题可通过 `title` 函数设置，横纵轴名称分别用 `xlabel` 与 `ylabel` 指定，图例则先在 `plot` 的 `label` 参数里命名，再调用 `legend` 显示。

顺带一提，若要正常显示中文，需要调整 `matplotlib` 的配置文件。这里只确认图表效果即可，无需修改配置。

请输入下面的代码以显示图表。

[PRE2]

执行结果如图 4 所示。

[PRE3]

图 4　为图表添加标题和轴标签

### 读者服务：

[PRE4]

微信扫码关注【异步社区】微信公众号，回复“e59085”获取本书配套资源以及异步社区 15 天 VIP 会员卡，近千本电子书免费畅读。

## 第 2 天 scikit-learn

### 第 1 部分　了解 scikit-learn

今天先认识一下 `scikit-learn`。它是一个采用 `BSD` 授权的开源机器学习库，内置了回归、分类、聚类等常用算法。

内部实现上它依赖 Python 的数值计算库 `NumPy` 与 `SciPy`。在使用前建议先浏览官方网站上的说明文档（见图 1）。

[PRE5]

图 1　`scikit-learn`官方网站

下面回顾几种常见的机器学习范式。

### 监督学习

监督学习处理的数据带有真实标签。它基于已知正确答案的样本学习特征与规律，再对无标签数据进行标签预测。

依据标签类型的不同，监督学习可细分为回归 `regression` 与分类 `classification`。

### 无监督学习

无监督学习的对象是没有标签的数据，目标是挖掘数据自身的特征、相似性与模式。因为不需要标注，数据收集成本更低，但也存在“不尝试就不知道能得到什么”的不确定性。

无监督学习主要分为聚类 `clustering` 与降维 `dimensionality reduction`。

### 其他机器学习算法（强化学习等）

还有一批算法难以单纯归入监督或无监督，例如强化学习，它会根据行为结果给予奖励，让模型逐步朝更优行为演进。

### scikit-learn 中的机器学习算法

`scikit-learn` 收录的算法数量庞大，大致可归为 4 类。

● **回归 `regression`**：基于已有数据对连续值进行预测。代表算法有 `SGD` 回归、`LASSO` 回归等。
● **分类 `classification`**：根据带标签样本学习并对新样本进行类别判断。代表算法有核近似法、*k* 近邻算法等。
● **聚类 `clustering`**：把相似样本归为一组以发现结构。代表算法有 *k* 均值算法、谱聚类算法等。
● **降维 `dimensionality reduction`**：压缩特征维度以暴露内在结构，也可作为后续算法的输入。代表算法有主成分分析 `PCA`、核 `PCA` 等。

### 哪种算法最合适

算法太多容易让人迷茫。官方网站提供了一张决策图帮助挑选（见图 2）。

[PRE6]

图 2　算法图

沿着图 2 的流程只需回答“是”或“否”就能定位合适的模型。例如 `START`→学习样本是否 50 个以上？答“否”就会提示“请多准备数据”。是否属于分类预测？答“是”。数据是否有标签？答“是”进入监督学习分支，答“否”则进入无监督分支。

即便选定模型，如何高效使用仍是问题，其中最容易留下印象的应属回归。因此在进入复杂模型前，先用 `scikit-learn` 最简单的回归算法练手。

### 第 2 部分　回归分析

回归分析是用统计方法刻画目标变量与解释变量之间关系的技术。目标变量是待预测的结果，解释变量是影响结果的因素。

`scikit-learn` 采用线性回归模型。线性回归在所有点上拟合一条直线，最小二乘法求解直线方程。

解释变量只有一个时称为一元回归，多个时称为多元回归，二者均被支持。下面先做一元回归。

### 一元回归分析

在 `scikit-learn` 中可通过 `linear_model.LinearRegression` 创建线性回归模型，语法格式如图 1 所示。

| `sklearn.linear_model.LinearRegression(fit_intercept=True, normalize=False,copy_X=True, n_jobs=1)` |

| --- |

| 参数 || 说明 |

| `fit_intercept` || 指定是否包含求截距的计算 |

| `normalize` || 指定是否事先将解释变量归一化 |

| `copy_X` || 为了不在内存中重写，指定是否在复制`X`轴数据后执行 |

| `n_jobs` || 指定用于计算的作业数量。如果设为−1，则使用可用的所有`CPU` |

图 1　`linear_model.LinearRegression`函数的语法格式

代码在 `Jupyter Notebook` 中运行，需提前启动环境。导入 `NumPy`、`matplotlib`、`Pandas`，再从 `sklearn` 导入 `linear_model`。

[PRE7]

为便于理解，先以 `Pandas` 的 `DataFrame` 形式准备 *X* 轴与 *Y* 轴数据。

[PRE8]

最后用 `matplotlib` 画出散点图（见图 2）。

[PRE9]

[PRE10]

图 2　绘制的散点图

线性回归的思路是在数据上画出一条最贴合的直线。先用目测画一条回归直线（见图 3）。

[PRE11]

图 3　用目测法画出的回归直线

随后把 *X*、*Y* 代入公式计算回归系数与截距，这一步通过 `linear_model.LinearRegression` 完成。先生成线性回归模型。

[PRE12]

把 *X*、*Y* 传给模型并调用 `fit` 进行训练，执行后 `Jupyter Notebook` 会输出模型训练时使用的参数。

[PRE13]

至此，模型已学会 *X* 与 *Y* 的关系。

下面用 `NumPy` 的 `arange` 生成从 *X* 最小值到最大值、步长 0.01 的数组。

[PRE14]

用 `NumPy` 的 `newaxis` 把 *X* 坐标转为二维数组。

[PRE15]

再把该 *X* 传入模型的 `predict` 方法得到对应的 *Y* 值。

[PRE16]

同时展示原始散点与拟合直线（见图 4）。

[PRE17]

[PRE18]

图 4　同时显示原始数据和创建好的回归直线

拟合得到的系数保存在 `coef_` 属性，截距保存在 `intercept_` 属性中。

[PRE19]

借助 `linear_model.LinearRegression`，只要明确模型结构，具体计算即可交由库完成，过程相当简便。

### 第 3 部分　机器学习数据集

了解用法后，真正做实验仍需要数据。

网络上可找到大量教学样本，这里使用 `scikit-learn` 自带的两个数据集：`iris` 与手写数字数据集。

## ▶ iris 数据集

`iris` 数据集因长期被用作统计与机器学习的入门示例而知名。用专用的 `load_iris` 函数即可读取。

[PRE20]

依据官方说明，读取得到的鸢尾花数据是 `scikit-learn` 中定义的 `Bunch` 类实例（见图 1）。

数据可通过 `'data'`、`'target'` 等键访问。下面用 `'DESCR'` 键查看数据集说明（见图 2）。

[PRE21]

输出表明共有 150 条记录，单位为厘米，包含 `Iris-Setosa`、`Iris-Versicolour`、`Iris-Virginica` 三种鸢尾花的花萼长度、宽度与花瓣长度、宽度的测量值。

[PRE22]

图 1　`scikit-learn`官方网站上关于`load_iris`函数的说明

[PRE23]

图 2　`iris`数据集的说明信息

检查除 `'DESCR'` 外的其他键。

[PRE24]

除了 `'DESCR'`，还有 `'data'`、`'target'`、`'target_names'`、`'feature_names'` 等，分别尝试查看其内容。

`'data'` 对应鸢尾花的特征矩阵，顺序为花萼长度、花萼宽度、花瓣长度、花瓣宽度。

[PRE25]

显示 `'target'` 得到 150 个数字，0、1、2 各 50 个，表示三种鸢尾花种类。

[PRE26]

`'target_names'` 可确认类别名称，0 为 `setosa`，1 为 `versicolour`，2 为 `virginica`。

[PRE27]

`'feature_names'` 给出各特征的命名，`feature` 即特征之意。

[PRE28]

接下来用 `Pandas` 的 `DataFrame` 将 `iris` 数据展示出来。

[PRE29]

至此已掌握 `iris` 数据集的结构（见图 3）。

[PRE30]