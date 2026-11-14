# 3. AdaGrad

以上方法均使用相同的学习率对参数进行更新，但是对于不常见的参数，由于其更新频率低可能导致其无法更新到较好的状态。解决此问题的一个思路是在训练过程中对不同参数的学习率进行动态调整：对于常见参数，由于其更新频率高从而调低其学习率；对于不常见参数，由于其更新频率低从而调高其学习率。可以记录截至第 $t$ 步参数 $x$ 出现（被更新）的次数 $s(\Theta, t)$，并将其学习率设置为 $\epsilon=\frac{\epsilon_0}{\sqrt{s(\Theta, t)+c}}$，其中，$\epsilon_0$ 为设置的初始全局学习率，$c$ 为一个较小的正数，常设置为 $10^{-6}$，用于保证数值稳定性，避免分母为零。AdaGrad ( Adaptive Gradient ) 算法 [10] 提出使用梯度的平方和作为 $s(\Theta, t)$ 的替换估计，记为 $s_i$ :

```
$x'=x-\frac{\epsilon_0}{\sqrt{s_i+c}}\nabla_{x_j}f(x_j,t) \quad (7-55)

s_i=s_{i-1}+\nabla_{y_j}f(x_j,t)^2 \quad (7-56)$
```

AdaGrad 也存在一定的问题：首先 AdaGrad 依赖于初始全局学习率 $\epsilon_0$，如果初始全局学习率太大，会使得训练初期对梯度的调节太大；此外随着训练次数的迭代，累积的梯度平方和会越来越大，会使得学习率单调递减至 0，从而导致训练提前结束。

# 4. RMSProp/ADADELTA

为解决 AdaGrad 中存在的梯度累计导致的学习率递减至 0 的问题，RMSProp ( Root Mean Square Prop ) [11] 改用指数移动平均改善学习率动态调整过程：

```
$x'=x-\frac{\epsilon_0}{\sqrt{s_i+c}}\nabla_{x_j}f(x_j,t) \quad (7-57)

s_i=\gamma s_{i-1}+(1-\gamma)\nabla_{x_j}f(x_j,t)^2 \quad (7-58)$
```

其中，$\gamma$ 为指数移动平均的加权系数，且 $\gamma \in [0, 1]$。RMSProp 改善了 AdaGrad 存在的问题，但是仍然需要手动指定初始学习率 $\epsilon_0$。ADADELTA [12] 是 AdaGrad 的另一种变体，同时使用了两个状态变量，$s_i$ 与 RMSProp 一样用于存储梯度平方和（二阶动量）的指数移动平均值，$\Delta x_i$ 用于存储模型本身参数更新变量平方和的指数移动平均值。

```
$x'=x-\frac{\sqrt{\Delta x_{i-1}+c}}{\sqrt{s_i+c}}\nabla_{x_j}f(x_j,t) \quad (7-59)

s_i=\gamma s_{i-1}+(1-\gamma)\nabla_{x_j}f(x_j,t)^2 \quad (7-60)

\Delta x_{i-1}=\gamma\Delta x_{i-2}+(1-\gamma)g_{i-1}^2 \quad (7-61)$
```

其中

```
$g_{i-1}=\frac{\sqrt{\Delta x_{i-2}+c}}{\sqrt{s_{i-1}+c}}\nabla_{x_j}f(x_j,t-1) \quad (7-62)$
```