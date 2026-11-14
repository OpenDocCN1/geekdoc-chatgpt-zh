对于\(|\phi| < 1\)的情况，序列本身就是平稳的，而对于另外两种情况，只有当\(|\phi| = 1\)时，差分后的序列才可以转化为平稳序列。所以我们需要进行一个检验——Dickey-Fuller(DF) Test，也叫单位根检验，检验单位根是否存在，即\(\phi\)是否为1，来判断差分后的序列是否平稳。

```
\(x_t = c + \phi x_{t-1} + e_t\)
```

其中\(e_t \sim N(0, \sigma_e^2)\)，\(c\) 是截距。

```
\(H_0: |\phi| = 1 \Rightarrow x_t \sim I(1)\)
\(H_1: |\phi| < 1 \Rightarrow x_t \sim I(0)\)
\(x_t - x_{t-1} = c + (\phi - 1)x_{t-1} + e_t\)
\(\Delta x = c + \delta x_{t-1} + e_t\)
```

如果 \(\delta = 0\)，则存在单位根：

```
\(H_0: \delta = 0\)
\(H_1: \delta = 1\)
```

\(H_0\) 代表原假设（null hypothesis），\(H_1\) 代表备择假设（alternative hypothesis）。

# 3. ARIMA

对于一组不平稳序列\(x_t\)，需要先进行差分平稳化处理：

```
\(z_t = x_{t+1} - x_t\)
\(z_t = \varphi_1 z_{t-1} + \theta_1 \varepsilon_{t-1} + \varepsilon_t\)
```

假设原序列是\([x_0, x_1, \cdots, x_l]\)，我们要预测的是\(x_k(k > l)\)，如何通过对\(z_t\)序列的预测进行还原呢？可以参考下面的公式。

```
\(x_k = z_{k-1} + x_{k-1} = z_{k-1} + z_{k-2} + x_{k-2} = \sum_{i=1}^{k-l} z_{k-i} + x_l\)
```

# 4. SARIMA

SARIMA 中的 S 指的是 Seasonality，即季节性。一般如何剔除季节性呢？假设我们有一组冰激凌售卖的销量序列\(y_t\)，通常情况下，每年夏天会有销量峰值，是以年为粒度的季节性变化，如下面的公式所示，但是我们会损失一年的数据，因为第一年之前是没有数据的。

```
\(z_t = y_{t+365} - y_t\)
```