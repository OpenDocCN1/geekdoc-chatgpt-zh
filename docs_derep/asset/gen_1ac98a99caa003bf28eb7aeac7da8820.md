针对一组非平稳序列 \(x_t\)，首要任务是通过差分操作将其转化为平稳序列：

[PRE0]

假设已知原始序列为 \([x_0, x_1, \cdots, x_l]\)，目标是预测 \(x_k (k > l)\)。那么，如何利用对差分序列 \(z_t\) 的预测结果反推原始值？请参考以下公式推导。

[PRE1]

## 4. SARIMA

SARIMA 中的 "S" 代表 Seasonality（季节性）。通常如何剔除季节性影响？以冰淇淋销量序列 \(y_t\) 为例，每年夏季销量激增，呈现以年为周期的季节性波动，如下式所示，但此法会导致首年数据缺失，因为首年之前无历史数据可供参考。

[PRE2]

SARIMA 模型标记为 SARIMA(p, d, q)(P, D, Q)m。其中，前括号 (p, d, q) 对应非季节性部分；后括号 (P, D, Q)m 对应季节性部分；m 表示一个季节周期包含的阶段数。图 2-11 展示了按月统计的冰淇淋销量，其周期为“年”，即每周期含 12 个阶段（月份）。

[PRE3]

图 2-11 季节性

以 SARIMA(1,1,1)(1,1,1)4 为例，借助滞后算子 (Lag Operator) 推导，模型表达式展开如下：

[PRE4]

其中，\((1 - \varphi_1 L)\) 代表非季节部分的 AR 项 (p)；\((1 - L)\) 代表非季节部分的差分项 (d)，即 \(x_t - Lx_t = x_t - x_{t-1}\)；\((1 + \theta_1 L)\) 代表非季节部分的 MA 项 (q)；\((1 - \gamma_1 L^4)\) 对应季节部分的 AR 项 (P)；\((1 - L^4)\) 对应季节部分的差分项 (D)，即 \(x_t - x_{t-4}\)；\((1 + \eta_1 L^4)\) 对应季节部分的 MA 项 (Q)。

再看一个更简化的案例，SARIMA(1,0,0)(0,1,1)4：

[PRE5]

令 \(z_t = x_t - x_{t-4}\)：

[PRE6]

由该公式可清晰拆解 SARIMA 各组成部分：\(z_t\) 体现季节差分项 (D)；\(\varphi_1 z_{t-1}\) 体现非季节自回归项 (p)；\(\eta_1 \varepsilon_{t-4}\) 体现季节移动平均项 (Q)。

## 2.2.3 条件异方差模型

### 1. ARCH

ARCH（自回归条件异方差模型）全称为 Autoregressive Conditional Heteroskedasticity model。在诸多时间序列中，数据常呈现“波动聚类”特征：长期来看方差恒定、序列平稳，但短期内方差剧烈波动，这种现象称为条件异方差。传统 ARMA 类模型难以捕捉此特性。图 2-12 即为典型适合 ARCH 建模的序列形态。

[PRE7]

图 2-12 条件异方差数据形态

ARCH(p) 建模的核心在于对残差序列建模：

[PRE8]

该式含义直观：若当期方差较大，则上一期方差大概率也较大。\(\epsilon_t\) 为白噪声。ARCH(1) 具体形式如下：

[PRE9]

以此类推，ARCH(p) 阶模型表达为：

[PRE10]

在 ARCH(p) 定阶检验中：

[PRE11]

\(x_t^2 = \varepsilon_t^2(\alpha_0 + \alpha_1 x_{t-1}^2) = \varepsilon_t^2 \alpha_0 + \varepsilon_t^2 \alpha_1 x_{t-1}^2\)

由上式可见自回归结构，即误差平方项的自回归过程。据此，可通过偏自相关函数 (PACF) 图谱的滞后截尾点确定 \(p\) 值。

### 2. GARCH

GARCH 是 ARCH 的广义推广形式（Generalized ARCH），能更精准刻画波动聚类，避免波动的突发性跳变。

以 ARCH(1) 为例：

\(x_t = \varepsilon_t \sqrt{\alpha_0 + \alpha_1 x_{t-1}^2} = \varepsilon_t \sigma_t\)

此类模型擅长拟合突发性剧烈波动，而非持续数日或数周的聚类性波动，因此引入 GARCH 机制：

\(x_t = \varepsilon_t \sqrt{\alpha_0 + \alpha_1 x_{t-1}^2 + \beta_1 \sigma_{t-1}^2} = \varepsilon_t \sigma_t\)

对比可见，新增项 \(\beta_1 \sigma_{t-1}^2\) 引入了对历史波动率的记忆。类比 ARMA 对 AR 的改进：当期波动不仅受过去观测值冲击影响，还受过去波动率水平影响，从而更好地描绘持续性聚类波动。

由此，GARCH(\(p,q\)) 通式为：

\(x_t = \varepsilon_t \sqrt{\alpha_0 + \alpha_1 x_{t-1}^2 + \cdots + \alpha_p x_{t-p}^2 + \beta_1 \sigma_{t-1}^2 + \cdots + \beta_q \sigma_{t-q}^2} = \varepsilon_t \sigma_t\)

## 2.3 Croston 模型及其变体

间断性需求的稀疏性（大量零值）让常规预测方法失效，催生了大量针对性算法，且多以学者名字首字母命名。Croston 模型及其变体是该领域的经典代表。Croston 提出组合预测策略：将序列拆解为“需求间隔”与“需求量”两个独立子序列分别预测，最后合成结果。具体逻辑如下：

若 \(X_t > 0\)，则
\(\begin{cases} Z_{t+1} = \alpha X_t + (1-\alpha)Z_t \\ V_{t+1} = \alpha Q_t + (1-\alpha)V_t \\ Y_{t+1} = Z_{t+1}/V_{t+1} \end{cases}\)

若 \(X_t = 0\)，则
\(\begin{cases} Z_{t+1} = Z_t \\ V_{t+1} = V_t \\ Y_{t+1} = Y_t \end{cases}\)

其中，\(Z_t\) 为 \(t\) 期非零需求量估计，\(V_t\) 为非零需求间隔估计，\(X_t\) 为真实需求，\(Q_t\) 为当前零需求持续周期，\(Y_t\) 为考虑零值后的综合需求估计。

原始 Croston 法存在偏差，Syntetos 与 Boylan 提出 SBA（非偏估计），引入平滑系数 \((1 - \alpha/2)\) 修正，即设 \(Z_t=\mu\)、\(V_t = p\)。推导表明新预测值趋近无偏，且经多研究验证有效提升精度。

\(E \left[ (1 - \frac{\alpha}{2}) \left( \frac{Z_t}{V_t} \right) \right] = (1 - \frac{\alpha}{2}) E \left[ \left( \frac{Z_t}{V_t} \right) \right] = (1 - \frac{\alpha}{2}) \left( \frac{\mu}{p} + \frac{1}{2} \frac{\partial^2 (\frac{\mu}{p})}{\partial p^2} Var(p) \right) = (\frac{2 - \alpha}{2}) \left( \frac{\mu}{p} + \frac{\alpha}{2 - \alpha} \mu \frac{p-1}{p^2} \right) = \frac{\mu}{p} \left( \frac{2 - \alpha}{2} + \frac{\alpha p - 1}{2} \frac{1}{p} \right) \approx \frac{\mu}{p}\)

上述方法未考虑产品生命周期终止（下市）场景。Teunter、Syntetos、Babai 等人提出 TSB 法，核心差异在于：不再估计间隔，而是直接估计“非零需求发生概率”，且**每期更新**而非仅在有需求时更新。\(D_t\) 指示 \(t\) 期是否有需求，\(P_t\) 为需求概率估计，\(\alpha, \beta\) 分别为需求量与概率的平滑系数。

若 \(D_t = 1\)，则
\(\begin{cases} P_{t+1} = \beta(1) + (1 - \beta)P_t \\ Z_{t+1} = \alpha X_t + (1 - \alpha)Z_t \\ Y_{t+1} = P_{t+1}Z_{t+1} \end{cases}\)

若 \(D_t = 0\)，则
\(\begin{cases} P_{t+1} = (1 - \beta)P_t \\ Z_{t+1} = Z_t \\ Y_{t+1} = P_{t+1}Z_{t+1} \end{cases}\)

TSB 优势显著：长期零需求下 \(P_t\) 指数衰减趋近 0，而 Croston 间隔估计不变。实战中可设概率阈值判断产品是否继续售卖。Prestwich、Tarim、Rossi 等人提出 HES（双曲指数平滑），融合 Croston 与贝叶斯思想。两者区别：HES 随零需求增加呈双曲线递减，TSB 为指数递减。

标准 Croston 假设需求发生服从伯努利分布；若服从泊松分布，Shale-Boylan-Johnston (SBJ) 与 Syntetos-Babai-Luo 给出了偏差修正系数。SBJ 摒弃简单指数平滑，改用简单移动平均或指数加权移动平均，平滑系数为 \((1 - \alpha/(2 - \alpha))\)。Leven 和 Segerstedt 提出 LAS，估计式为 \(Y_{t+1} = \alpha X_t / Q_t + (1 - \alpha)Y_t\)。Vinh 提出 VINH，与 Croston 相似但加权基于过去两期记录：\(Z_{t+1} = \alpha X_t + (1 - \alpha)Z_t + (1 - \alpha)^2 Z_{t-1}\)、\(V_{t+1} = \alpha Q_t + (1 - \alpha)V_t + (1 - \alpha)^2 V_{t-1}\)。这些变体在实测中均优于原始 Croston。

# 第 3 章 线性回归模型

## 3.1 简单线性回归模型

### 3.1.1 基本概念介绍

简单线性回归旨在建立单一特征变量与目标变量间的线性映射函数，核心在于求解参数向量以实现预测。多元线性回归则扩展为多特征变量联合建模。通过训练获取各特征的回归系数，加权求和得到预测值。多元线性回归基础表达式如下：

[PRE12]

其中，\(\theta_1 \sim \theta_n\) 为回归系数，量化 \(x\) 对 \(y\) 的边际影响；\(\varepsilon\) 为误差项，捕捉线性关系外的随机扰动；\(\theta_0\) 为截距项。线性回归几何示意见图 3-1。

[PRE13]

图 3-1 线性回归的示意图

如何求得最优系数组合？首先设定训练目标。因随机误差 \(\varepsilon\) 服从高斯分布，可通过极大似然估计 (MLE) 推导目标函数，再以最小化该函数为优化目标，利用梯度下降寻找极值点获取最终系数。以下详述推导步骤。

目标函数定义为：

[PRE14]

\(y_i\) 为真实值，\(\hat{y}_i\) 为样本 \(i\) 在参数 \(\theta\) 下的预测值。

该目标函数如何推导而来？核心依据是极大似然估计。因 \(\varepsilon(i)\) 服从高斯分布，可得：

[PRE15]

对每个样本，期望条件概率 \(p(y^{(i)}|x^{(i)}; \theta)\) 越大越好。将 \(n\) 个样本概率相乘构造似然函数，目标转化为最大化该函数：

[PRE16]

乘法运算不便，对两边取对数转化为对数似然函数：

[PRE17]

此时，最大化似然函数等价于最小化下式，即为目标函数的由来。

[PRE18]

### 3.1.2 最优参数求解

基于推导出的目标函数，进行最优参数求解。针对简单线性回归，目标函数为：

[PRE19]

只需对目标函数求导并令导数为零，即可定位最优解：