SARIMA 的模型表达式为SARIMA(p, d, q)(P, D, Q)m，其中，(p, d, q)代表模型中的非季节部分；(P, D, Q)m 代表模型中的季节部分；m 代表在一个季节周期内有几个阶段。如图 2-11 所示为冰激凌每个月的销量，周期为“年”，即每一周期有 12 个阶段。

![](bbox=[0.19, 0.212, 0.685, 0.435])

图 2-11 季节性

以SARIMA(1,1,1)(1,1,1)4为例，通过 Lag 算子的推导，该模型的公式推导如下：

```
$$(1 - \varphi_1 L)(1 - \gamma_1 L^4)(1 - L)(1 - L^4)x_t = (1 + \theta_1 L)(1 + \eta_1 L^4)\varepsilon_t$$
```

其中，(1 - \varphi_1 L)项指代的是非季节部分的 p 项；(1 - L)项指代的是非季节部分的 d 项，即 x_t - Lx_t = x_t - x_{t-1}；(1 + \theta_1 L)项指代的是非季节部分的 q 项；(1 - \gamma_1 L^4)指代的是季节部分的 P 项；(1 - L^4)项指代的是季节部分的 D 项，即 x_t - x_{t-4}；(1 + \eta_1 L^4)指代的是季节部分的 Q 项。

下面也可以看一个更简单的例子，SARIMA(1,0,0)(0,1,1)4：

```
$$(1 - \varphi_1 L)(1 - L^4)x_t = (1 + \eta_1 L^4)\varepsilon_t$$  
$$(1 - \varphi_1 L - L^4 + \varphi_1 L^5)x_t = \varepsilon_t + \eta_1 \varepsilon_{t-4}$$  
$$x_t - x_{t-4} = \varphi_1 x_{t-1} - \varphi_1 x_{t-5} + \varepsilon_t + \eta_1 \varepsilon_{t-4}$$
```

令 z_t = x_t - x_{t-4}:

```
$$z_t = \varphi_1 z_{t-1} + \varepsilon_t + \eta_1 \varepsilon_{t-4}$$
```

由这个公式我们可以看到 SARIMA 中的所有成分，z_t 体现了 D 项，\varphi_1 z_{t-1} 体现了 p 项，\eta_1 \varepsilon_{t-4} 体现了 Q 项。