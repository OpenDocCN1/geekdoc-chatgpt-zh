

基于蒙特卡罗模拟的投资组合权重 ![\\boldsymbol{\\phi}](Images/image00474.gif)可以在波动率−收益率空间中可视化投资机会集，如图 3-1 所示。

![{%}](Images/image00475.jpeg)

**图 3-1：模拟投资组合预期波动率和预期收益率（单一风险资产）**

因为只有一种风险资产和一种无风险资产，所以机会集是一条直线。

```
In [48]: from pylab import plt, mpl
             plt.style.use('seaborn')
             mpl.rcParams['savefig.dpi'] = 300
             mpl.rcParams['font.family'] = 'serif'
    
In [49]: phi_mcs = np.random.random((2, 200))  ➊
    
In [50]: phi_mcs = (phi_mcs / phi_mcs.sum(axis=0)).T  ➊
    
In [51]: mcs = np.array([(sigma_phi(phi), mu_phi(phi))
                             for phi in phi_mcs])  ➋
    
In [52]: plt.figure(figsize=(10, 6))
             plt.plot(mcs[:, 0], mcs[:, 1], 'ro')
             plt.xlabel('expected volatility')
             plt.ylabel('expected return');
```

➊ 随机投资组合，标准化为 1。
➋ 随机组合的预期投资组合波动率与收益率。

现在设想一种有 3 种状态的静态经济模式 ![\\mathcal{M}^3](Images/image00476.gif)，其中 ![\\mathit{\\Omega}={u,m,d\\}](Images/image00477.gif)。这 3 种状态的可能性相等，即 ![P=\\left{\\dfrac{1}{3},\\dfrac{1}{3},\\dfrac{1}{3}\\right\\}](Images/image00478.gif)。可交易资产集合是包括两个固定价格为 ![S\_0=T\_0=10](Images/image00479.gif)的风险资产 ![S](Images/image00354.gif)和 ![T](Images/image00480.gif)的组合，不确定回报分别为以下各项：

![S\_1=\\begin{bmatrix}20\\\\10\\\\5\\end{bmatrix}](Images/image00481.gif)

和

![T\_1=\\begin{bmatrix}1\\\\12\\\\13\\end{bmatrix}](Images/image00482.gif)

基于这些假设，下面的 Python 代码重复了蒙特卡罗模拟并可视化了图 3-2 中的结果。使用两种风险资产，著名的 MVP“子弹”将可见。

```
In [53]: P = np.ones(3) / 3  ➊
             P  ➊
Out[53]: array([0.33333333, 0.33333333, 0.33333333])
    
In [54]: S1 = np.array((20, 10, 5))
    
In [55]: T0 = 10
             T1 = np.array((1, 12, 13))
    
In [56]: M0 = np.array((S0, T0))
             M0
Out[56]: array([10, 10])
    
In [57]: M1 = np.array((S1, T1)).T
             M1
Out[57]: array([[20,  1],
                    [10, 12],
                    [ 5, 13]])
    
In [58]: rM = M1 / M0 - 1
             rM
Out[58]: array([[ 1. , -0.9],
                    [ 0. ,  0.2],
                    [-0.5,  0.3]])
    
In [59]: mcs = np.array([(sigma_phi(phi), mu_phi(phi))
                             for phi in phi_mcs])
    
In [60]: plt.figure(figsize=(10, 6))
             plt.plot(mcs[:, 0], mcs[:, 1], 'ro')
             plt.xlabel('expected volatility')
             plt.ylabel('expected return');
```

➊ 3 种状态的新概率测度。

![{%}](Images/image00483.jpeg)

**图 3-2：模拟投资组合预期波动率和预期收益率（两种风险资产）**

### 3. 最小波动率和最大夏普比率

接下来，推导**最小波动率**（最小方差）和**最大夏普比率**投资组合。图 3-3 显示了两个投资组合在风险−收益空间中的位置。

![{%}](Images/image00484.jpeg)

**图 3-3：最小波动率和最大夏普比率投资组合**

尽管风险资产 ![T](Images/image00480.gif)具有负的预期收益率，但它在最大夏普比率投资组合中具有重要的权重。这是由于多样化效应降低了投资组合的风险，而不是降低了投资组合的预期收益率。

```
In [61]: cons = {'type': 'eq', 'fun': lambda phi: np.sum(phi) - 1}
    
In [62]: bnds = ((0, 1), (0, 1))
    
In [63]: min_var = minimize(sigma_phi, (0.5, 0.5),
                                constraints=cons, bounds=bnds)  ➊
    
In [64]: min_var
Out[64]:      fun: 0.07481322946910632
                  jac: array([0.07426564, 0.07528945])
              message: 'Optimization terminated successfully.'
                 nfev: 17
                  nit: 4
                 njev: 4
               status: 0
              success: True
                    x: array([0.46511697, 0.53488303])
    
In [65]: def sharpe(phi):
                 return mu_phi(phi) / sigma_phi(phi)  ➋
    
In [66]: max_sharpe = minimize(lambda phi: -sharpe(phi), (0.5, 0.5),
                            constraints=cons, bounds=bnds)  ➌
    
In [67]: max_sharpe
Out[67]:      fun: -0.2721654098971811
                  jac: array([ 0.00012054, -0.00024174])
              message: 'Optimization terminated successfully.'
                 nfev: 38
                  nit: 9
                 njev: 9
               status: 0
              success: True
                    x: array([0.66731116, 0.33268884])
    
In [68]: plt.figure(figsize=(10, 6))
             plt.plot(mcs[:, 0], mcs[:, 1], 'ro', ms=5)
             plt.plot(sigma_phi(min_var['x']), mu_phi(min_var['x']),
                      '^', ms=12.5, label='minimum volatility')
             plt.plot(sigma_phi(max_sharpe['x']), mu_phi(max_sharpe['x']),
                      'v', ms=12.5, label='maximum Sharpe ratio')
             plt.xlabel('expected volatility')
             plt.ylabel('expected return')
             plt.legend();
```

➊ 最小化投资组合预期波动率。
➋ 定义夏普比率函数，假设短期利率为 `0`。
➌ 通过最小化夏普比率的负值来最大化夏普比率。

### 4. 有效边界

**有效投资组合**是在给定预期风险（收益率）下具有最大预期收益率（风险）的投资组合。在图 3-3 中，所有预期收益率低于最小风险组合的投资组合都是**低效**的。下面的代码从风险−收益空间中得到有效投资组合，并将其绘制在图 3-4 中。所有有效投资组合的集合称为**有效边界**，经济主体只会选择位于有效边界上的投资组合。

```
In [69]: cons = [{'type': 'eq', 'fun': lambda phi: np.sum(phi) - 1},
                    {'type': 'eq', 'fun': lambda phi: mu_phi(phi) - target}]  ➊
    
In [70]: bnds = ((0, 1), (0, 1))
    
In [71]: targets = np.linspace(mu_phi(min_var['x']), 0.16)  ➋
    
In [72]: frontier = []
             for target in targets:
                 phi_eff = minimize(sigma_phi, (0.5, 0.5),
                                    constraints=cons, bounds=bnds)['x']  ➌
                 frontier.append((sigma_phi(phi_eff), mu_phi(phi_eff)))
             frontier = np.array(frontier)
    
In [73]: plt.figure(figsize=(10, 6))
             plt.plot(frontier[:, 0], frontier[:, 1], 'mo', ms=5,
                      label='efficient frontier')
             plt.plot(sigma_phi(min_var['x']), mu_phi(min_var['x']),
                      '^', ms=12.5, label='minimum volatility')
             plt.plot(sigma_phi(max_sharpe['x']), mu_phi(max_sharpe['x']),
                      'v', ms=12.5, label='maximum Sharpe ratio')
             plt.xlabel('expected volatility')
             plt.ylabel('expected return')
             plt.legend();
```

➊ 固定预期收益率目标水平的新的约束条件。
➋ 生成一组目标预期收益率。
➌ 得到给定预期收益率目标的最小波动率投资组合。

![{%}](Images/image00485.jpeg)

**图 3-4：有效边界**

## 3.4 资本资产定价模型

资本资产定价模型（CAPM）是金融学中被广泛记载和应用的模型之一，其核心是以线性方式将一只股票的预期收益率与市场投资组合的预期收益率联系起来，市场投资组合通常用标准普尔 500 等各类股票指数来近似。这种模式可以追溯到 Sharpe（1964）和 Lintner（1965）的开创性工作。Jones（2012）的第 9 章将 CAPM 与 MVP 的关系描述如下：

> 资本市场理论是一个实证理论，因为它假设了投资者的行为方式，而不是投资者应该如何的行为方式，就像现代投资组合理论（MVP）的情况。将资本市场理论视为投资组合理论的延伸是合理的，但重要的是要明白，MVP 并不是建立在资本市场理论的有效性或缺乏有效性之上的。

> 
> 许多投资者感兴趣的特定均衡模型是资本资产定价模型，通常称为 CAPM。它允许我们评估单个证券的相关风险，以及评估风险与投资预期收益率之间的关系。作为一种均衡模型，CAPM 因其简单性和隐含性而具有吸引力。

### 3.4.1 假设和结论

假设上一节中的静态经济模式 ![\\mathcal{M}^N=({\\mathit{\\Omega},\\mathcal{F},P\\},\\mathbb{A})](Images/image00451.gif)有 ![N](Images/image00452.gif)个交易资产和所有的简化假设。在 CAPM 模型中，假设经济主体根据 MVP 进行投资，只关心风险资产一个时期内的风险和收益统计值。

在**资本市场均衡**中，所有可用资产均由所有经济主体持有，所有市场均已出清。由于经济主体被假定使用 MVP 来形成有效投资组合，这意味着所有经济主体必须持有相同的有效投资组合（就其构成而言），因为每个经济主体的可交易资产集合是相同的。换句话说，**市场投资组合**（一组可交易资产）必须位于有效前沿。如果不是这样，市场就不可能处于均衡状态。

实现资本市场均衡的机制是什么？当今的可交易资产价格是确保市场出清的机制。如果经济主体对某项可交易资产的需求不足，该资产的价格就需要下降。如果需求超过供给，其价格就会上涨。如果价格设定正确，每种可交易资产的需求和供给就会相等。MVP 使可交易资产的价格为给定值，CAPM 则是给定资产风险−收益特征情形下，该资产均衡价格**应该**是多少的一种理论和模型。

CAPM 假设存在（至少）一种无风险资产，每个经济主体可以投资任意金额，并获得 ![\\overline{r}](Images/image00486.gif) 的无风险利率。因此，每个经济主体将在均衡状态下持有市场投资组合和无风险资产的组合，这被称为**双基金分离定理**8。所有这些投资组合的集合被称为**资本市场线**（CML）。图 3-5 是 CML 的示意图。市场投资组合右侧的投资组合只有在允许经济主体卖空无风险资产并以这种方式借钱的情况下才能实现。

8如需了解更多信息，请参阅 Jones（2012）的第 9 章。

```
In [74]: plt.figure(figsize=(10, 6))
         plt.plot((0, 0.3), (0.01, 0.22), label='capital market line')
         plt.plot(0, 0.01, 'o', ms=9, label='risk-less asset')
         plt.plot(0.2, 0.15, '^', ms=9, label='market portfolio')
         plt.annotate('$(0, \\bar{r})$', (0, 0.01), (-0.01, 0.02))
         plt.annotate('$(\sigma_M, \mu_M)$', (0.2, 0.15), (0.19, 0.16))
         plt.xlabel('expected volatility')
         plt.ylabel('expected return')
         plt.legend();
```

![{%}](Images/image00487.jpeg)

**图 3-5：资本市场线（CML）**

