为了解决算法复杂度的问题，论文中采用了两种近似方法，分别是切比雪夫多项式趋近和一阶近似。首先介绍切比雪夫近似。

用一个关于 Λ 的多项式将核 Θ 限制起来，即：

$$\Theta(\Lambda)=\sum_{k=0}^{K-1}\theta_k\Lambda^k=\sum_{k=0}^{K-1}\theta_k T^k(\tilde{\Lambda})$$

K是卷积从中心开始的最大半径，是图卷积核的大小。$T_k(x)$是切比雪夫多项式，用于近似核。$\tilde{\Lambda}=2\frac{\Lambda}{\lambda_{\max}}-I_n$，$\lambda_{\max}$是$L$的最大值。

则图卷积即为：

$$\Theta *_G x = \Theta(L)x = \sum_{k=0}^{K-1}\theta_k T_k(\tilde{L})x$$

而一阶近似中假设 $\lambda_{\max}=2$，则有

$$\Theta *_G x \approx \theta_0 x + \theta_1\left(2\frac{L}{\lambda_{\max}}-I_n\right)x \approx (L)x = \theta_0 x + \theta_1\left(D^{-\frac12}WD^{-\frac12}\right)x$$

为了约束参数并为稳定数值计算，不妨令 $\theta \approx \theta_0 = -1 \theta$，$\tilde{W}=W+I_n$，$\widetilde{D}_{tt}=\sum_j\tilde{W}_{ij}$，则可得新的表达式：

$$\Theta *_G x \approx \theta_0 x + \theta_1\left(D^{-\frac12}WD^{-\frac12}\right)x = \theta\left(\tilde{D}^{-\frac12}\tilde{W}\tilde{D}^{-\frac12}\right)x$$

此时卷积核只有$K$个参数，一般$K$远小于$n$。同时也降低了计算复杂度。

# （2）High-Order Adaptive GCN

这里介绍的High-Order Adaptive GCN来自于2017年一篇论文《Convolutionon Graph A High-Order and Adaptive Approach》。

首先定义顶点$v_i$的$k$阶邻居$NB_i=\{v_j\in V\mid d(v_i,v_j)\le k\}$。$A\in R^{N\times N}$是邻接矩阵，$A^k$的第$i$行第$j$列表示的是顶点$i$到顶点$j$的$k$步路径的个数。

定义$k$阶卷积：