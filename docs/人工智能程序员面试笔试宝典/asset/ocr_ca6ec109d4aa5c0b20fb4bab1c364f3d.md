$f(x)=f_M(x)=\sum_{m=1}^{M}\beta_m b(x;\gamma_m))$

这样，前向分步算法就完成了先“拉拉面”再汇总的模型求解问题。

# 3. 前向分步算法与AdaBoost

事实上，AdaBoost也是加法模型，它是前向分步算法的一个特例，当加法模型的基函数是AdaBoost算法的基分类器并把损失函数设为指数函数时，用前向分步算法求解加法模型就可以推导出AdaBoost算法。

已知，AdaBoost算法的分类器为：

$f(x)=\sum_{m=1}^{M}\alpha_m G_m(x)$

由形式可知，这就是基函数为$G_m(x)$、基函数权重为$\alpha_m$的加法模型。下面只需证明该向前分步算法的损失函数为指数损失函数。

假设经过$m-1$轮迭代的前向分步算法，要在第$m$轮迭代得到$\alpha_m$和$G_m$。其中，$\alpha_m$和$G_m$为：

$(\alpha_m,G_m)=\min_{\alpha,G}\sum_{i=1}^{n}\exp\{-y_i(f_{m-1}(x)+\alpha G(x_i))\}$

上式等价于

$(\alpha_m,G_m)=\min_{\alpha,G}\sum_{i=1}^{n}s_{mi}\exp\{-y_i(\alpha G(x_i))\}$

其中$s_{mi}=\exp\{-y_i f_{m-1}(x)\}$，只依赖于$m$与$i$。

先求$G_m$

$G_m=\arg\min_G\sum_{i=1}^{n}s_{mi}I(y_i\neq G(x_i))$

再求$\alpha_m$

$\alpha_m=\frac{1}{2}\log_e\frac{1-e_m}{e_m}$

二者都与AdaBoost模型中的结果完全一致，因此可以证明二者等价，即AdaBoost是前向分步算法的一个特例。