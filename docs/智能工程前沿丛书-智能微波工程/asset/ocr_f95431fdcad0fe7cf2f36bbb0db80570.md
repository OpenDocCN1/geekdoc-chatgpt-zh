其 SLL 指标提升至 \(-13.04\mathrm{dB}\)，其中 6 个波束的 \(\mathrm{SLL}\) 指标分别为 \(-14.36\mathrm{dB}\)、\(-13.25\mathrm{dB}\)、\(-13.22\mathrm{dB}\)、\(-13.10\mathrm{dB}\)、\(-13.11\mathrm{dB}\) 及 \(-13.04\mathrm{dB}\)。在迭代过程中，HKDT 预测值与验证值间的 \(\mathrm{RMSE}\) 从 0.8319 降低至 0.3199。

# 5.4.4 采用凸优化算法的微带天线面阵设计

与线阵的设计实例类似，微带天线的面阵设计亦可以采用 HKDT 与凸优化算法协同优化的方法实现相较于进化算法等更有效的设计优化。考虑固定数目 \(N=16\) 的微带天线阵元，在算法每次外迭代中采用凸优化算法进行多次内迭代优化，第 \(i\) 次内迭代的优化可以表示为：

```
\begin{aligned}
\min_{\boldsymbol{\epsilon}_i, \boldsymbol{\delta}_i} \rho, \\
\text{s.t.} \left\{\begin{array}{l}
\max \left| f_{\boldsymbol{\epsilon}_i, \boldsymbol{\delta}_i}^i((\boldsymbol{U}, \boldsymbol{V})_{\mathrm{SL}}) \right| \leqslant \rho \\
|\boldsymbol{\epsilon}_i| \leqslant \mu, |\boldsymbol{\delta}_i| \leqslant \mu \\
d_{\mathrm{dis}, i} \geqslant d_{\min}
\end{array}\right.
\end{aligned}
\tag{5.21}
```

式中，\(\boldsymbol{\epsilon}_i, \boldsymbol{\delta}_i\) 分别代表第 \(i\) 次凸优化过程中各阵元在 \(x\) 方向和 \(y\) 方向移动的距离；\(\rho\) 代表在副瓣区域（sidelobe region, SL）内最大的 \(\mathrm{SLL}\)；\((\boldsymbol{U}, \boldsymbol{V})_{\mathrm{SL}}\) 代表在 SL 区域内的 \((u, v)\) 值；\(f_{\boldsymbol{\epsilon}_i, \boldsymbol{\delta}_i}^i((\boldsymbol{U}, \boldsymbol{V})_{\mathrm{SL}})\) 代表在 SL 区域内的辐射方向图信息；\(\mu\) 代表每次凸优化迭代时阵元移动最大步长；\(d_{\mathrm{dis}, i} \geqslant d_{\min}\) 限定了阵元间的最小间距为 \(d_{\min}\)。

图 5.19 展示了在初始化步骤下，采用理想阵元方向图优化得到的阵列方向

![](bbox=[0.2, 0.63, 0.77, 0.89])

(a) 初始化步骤中，采用理想阵元方向图的优化结果与全波仿真验证结果的对比