
```
In [74]: res = pd.DataFrame()
         for year in range(2010, 2019):
             rets_ = rets[symbols].loc[f'{year}-01-01':f'{year}-12-31']
             epv = port_volatility(rets_, opt_weights[year])  ➊
             epr = port_return(rets_, opt_weights[year])  ➊
             esr = epr / epv  ➊
             rets_ = rets[symbols].loc[f'{year + 1}-01-01':f'{year + 1}-12-31']
             rpv = port_volatility(rets_, opt_weights[year])  ➋
             rpr = port_return(rets_, opt_weights[year])  ➋
             rsr = rpr / rpv  ➋
             res = res.append(pd.DataFrame({'epv': epv, 'epr': epr, 'esr': esr,
                                            'rpv': rpv, 'rpr': rpr, 'rsr': rsr},
                                           index=[year + 1]))
```

In [75]: res
Out[75]:            epv       epr       esr       rpv       rpr       rsr
         2011  0.157440  0.303003  1.924564  0.160622  0.133836  0.833235
         2012  0.173279  0.169321  0.977156  0.182292  0.161375  0.885256
         2013  0.202460  0.278459  1.375378  0.168714  0.166897  0.989228
         2014  0.181544  0.368961  2.032353  0.197798  0.026830  0.135645
         2015  0.160340  0.309486  1.930190  0.211368 -0.024560 -0.116194
         2016  0.326730  0.778330  2.382179  0.296565  0.103870  0.350242
         2017  0.106148  0.090933  0.856663  0.079521  0.230630  2.900235
         2018  0.086548  0.260702  3.012226  0.157337  0.038234  0.243004
         2019  0.323796  0.228008  0.704174  0.207672  0.275819  1.328147

In [76]: res.mean()
Out[76]: epv    0.190920
         epr    0.309689
         esr    1.688320
         rpv    0.184654
         rpr    0.123659
         rsr    0.838755
         dtype: float64

➊ 投资组合预期的统计值。
➋ 投资组合实际的统计值。

图 4-6 比较了单年的预期与实际的投资组合波动率。MVP 理论在预测投资组合波动率方面做得相当好。两个时间序列之间相对较高的相关性也支持了这一点。

```
In [77]: res[['epv', 'rpv']].corr()
Out[77]:           epv       rpv
         epv  1.000000  0.765733
         rpv  0.765733  1.000000

In [78]: res[['epv', 'rpv']].plot(kind='bar', figsize=(10, 6),
                 title='Expected vs. Realized Portfolio Volatility');
```

![{%}](Images/image00584.jpeg)

**图 4-6：预期与实际的投资组合波动率**

然而，当预期与实际的投资组合收益率进行比较时，结论则正好相反（参见图 4-7）。MVP 理论显然无法预测投资组合的收益率，两个时间序列之间的负相关性证实了这一点。

```
In [79]: res[['epr', 'rpr']].corr()
Out[79]:           epr       rpr
         epr  1.000000 -0.350437
         rpr -0.350437  1.000000

In [80]: res[['epr', 'rpr']].plot(kind='bar', figsize=(10, 6),
                 title='Expected vs. Realized Portfolio Return');
```

![{%}](Images/image00585.jpeg)

**图 4-7：预期与实际的投资组合收益率**

对夏普比率来说，我们会得出类似甚至更糟的结论（参见图 4-8）。对以投资组合夏普比率最大化为目标的数据驱动型投资者来说，该理论的预测值通常与实际值相差很大。两个时间序列之间的相关性甚至比收益率之间的相关性还要低。

```
In [81]: res[['esr', 'rsr']].corr()
Out[81]:           esr       rsr
         esr  1.000000 -0.698607
         rsr -0.698607  1.000000

In [82]: res[['esr', 'rsr']].plot(kind='bar', figsize=(10, 6),
                 title='Expected vs. Realized Sharpe Ratio');
```

![{%}](Images/image00586.jpeg)

**图 4-8：预期与实际的投资组合夏普比率**

> ![](Images/image00324.jpeg)　**MVP 理论的预测能力**
>
> 将 MVP 理论应用到真实世界的数据中揭示了其实际的不足。如果没有额外的约束条件，那么最优的投资组合的构成和再平衡可能是很极端的状态。在数值例子中，对投资组合收益率和夏普比率的预测能力是相当差的，而对投资组合风险的预测能力似乎是可以接受的。然而，投资者通常感兴趣的是风险调整后的业绩衡量指标，比如夏普比率，而在这个例子中，MVP 理论从统计上来说是最不适用的。

### 4.4.3　资本资产定价模型

类似的方法也可以应用于 CAPM 的实际测试。假设前面所讲的数据驱动型投资者希望应用 CAPM 来获得之前的 4 只科技股的预期收益率。下面的 Python 代码会首先求出给定年份的每只股票的 `beta` 系数，然后根据该股票的 `beta` 系数和市场投资组合的表现，计算其明年的预期收益率。市场投资组合由标准普尔 500 指数近似计算。

```
In [83]: r = 0.005  ➊

In [84]: market = '.SPX'  ➋

In [85]: rets = np.log(raw / raw.shift(1)).dropna()

In [86]: res = pd.DataFrame()

In [87]: for sym in rets.columns[:4]:
             print('\n' + sym)
             print(54 * '=')
             for year in range(2010, 2019):
                 rets_ = rets.loc[f'{year}-01-01':f'{year}-12-31']
                 muM = rets_[market].mean() * 252
                 cov = rets_.cov().loc[sym, market]  ➌
                 var = rets_[market].var()  ➌
                 beta = cov / var  ➌
                 rets_ = rets.loc[f'{year + 1}-01-01':f'{year + 1}-12-31']
                 muM = rets_[market].mean() * 252
                 mu_capm = r + beta * (muM - r)  ➍
                 mu_real = rets_[sym].mean() * 252  ➎
                 res = res.append(pd.DataFrame({'symbol': sym,
                                                'mu_capm': mu_capm,
                                                'mu_real': mu_real},
                                               index=[year + 1]),
                                 sort=True)  ➏
                 print('{} | beta: {:.3f} | mu_capm: {:6.3f} | mu_real: {:6.3f}'
                       .format(year + 1, beta, mu_capm, mu_real))  ➏
```

➊ 指定无风险短期利率。
➋ 定义市场投资组合。
➌ 求出股票的 `beta` 系数。
❹ 根据上一年的 `beta` 系数和当年的市场投资组合表现计算预期收益率。
➎ 计算当年股票的实际业绩。
➏ 汇集并打印所有结果。

以上代码的输出如下。

```
        AAPL.O
        ======================================================
        2011 | beta: 1.052 | mu_capm: -0.000 | mu_real:  0.228
        2012 | beta: 0.764 | mu_capm:  0.098 | mu_real:  0.275
        2013 | beta: 1.266 | mu_capm:  0.327 | mu_real:  0.053
        2014 | beta: 0.630 | mu_capm:  0.070 | mu_real:  0.320
        2015 | beta: 0.833 | mu_capm: -0.005 | mu_real: -0.047
        2016 | beta: 1.144 | mu_capm:  0.103 | mu_real:  0.096
        2017 | beta: 1.009 | mu_capm:  0.180 | mu_real:  0.381
        2018 | beta: 1.379 | mu_capm: -0.091 | mu_real: -0.071
        2019 | beta: 1.252 | mu_capm:  0.316 | mu_real:  0.621

        MSFT.O
        ======================================================
        2011 | beta: 0.890 | mu_capm:  0.001 | mu_real: -0.072
        2012 | beta: 0.816 | mu_capm:  0.104 | mu_real:  0.029
        2013 | beta: 1.109 | mu_capm:  0.287 | mu_real:  0.337
        2014 | beta: 0.876 | mu_capm:  0.095 | mu_real:  0.216
        2015 | beta: 0.955 | mu_capm: -0.007 | mu_real:  0.178
        2016 | beta: 1.249 | mu_capm:  0.113 | mu_real:  0.113
        2017 | beta: 1.224 | mu_capm:  0.217 | mu_real:  0.321
        2018 | beta: 1.303 | mu_capm: -0.086 | mu_real:  0.172
        2019 | beta: 1.442 | mu_capm:  0.364 | mu_real:  0.440

        INTC.O
        ======================================================
        2011 | beta: 1.081 | mu_capm: -0.000 | mu_real:  0.142
        2012 | beta: 0.842 | mu_capm:  0.108 | mu_real: -0.163
        2013 | beta: 1.081 | mu_capm:  0.280 | mu_real:  0.230
        2014 | beta: 0.883 | mu_capm:  0.096 | mu_real:  0.335
        2015 | beta: 1.055 | mu_capm: -0.008 | mu_real: -0.052
        2016 | beta: 1.009 | mu_capm:  0.092 | mu_real:  0.051
        2017 | beta: 1.261 | mu_capm:  0.223 | mu_real:  0.242
        2018 | beta: 1.163 | mu_capm: -0.076 | mu_real:  0.017
        2019 | beta: 1.376 | mu_capm:  0.347 | mu_real:  0.243

        AMZN.O
        ======================================================
        2011 | beta: 1.102 | mu_capm: -0.001 | mu_real: -0.039
        2012 | beta: 0.958 | mu_capm:  0.122 | mu_real:  0.374
        2013 | beta: 1.116 | mu_capm:  0.289 | mu_real:  0.464
        2014 | beta: 1.262 | mu_capm:  0.135 | mu_real: -0.251
        2015 | beta: 1.473 | mu_capm: -0.013 | mu_real:  0.778
        2016 | beta: 1.122 | mu_capm:  0.102 | mu_real:  0.104
        2017 | beta: 1.118 | mu_capm:  0.199 | mu_real:  0.446
        2018 | beta: 1.300 | mu_capm: -0.086 | mu_real:  0.251
        2019 | beta: 1.619 | mu_capm:  0.408 | mu_real:  0.207
```

图 4-9 比较了在考虑到上一年的 `beta` 系数和当年的市场投资组合表现时，单只股票的预期（期望）收益率与该股票本年的实际收益率。显然，CAPM 的原始形式在仅基于 `beta` 系数预测股票表现时并没有真正发挥作用。

```
In [88]: sym = 'AMZN.O'

In [89]: res[res['symbol'] == sym].corr()
Out[89]:           mu_capm   mu_real
         mu_capm  1.000000 -0.004826
         mu_real -0.004826  1.000000

In [90]: res[res['symbol'] == sym].plot(kind='bar',
                         figsize=(10, 6), title=sym);
```

![{%}](Images/image00587.jpeg)

**图 4-9：单只股票的 CAPM 预期收益率与实际股票收益率**

图 4-10 比较了 CAPM 预期股票收益率的平均值与实际收益率的平均值。在这里，CAPM 同样表现不佳。

![{%}](Images/image00588.jpeg)

**图 4-10：多只股票的 CAPM 预期平均收益率与实际股票平均收益率**
