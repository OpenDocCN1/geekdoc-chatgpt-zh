$$L(\theta;n,z)=\left(\frac{1}{4}\right)n_{1}-z_{1}\left(\frac{1-\theta}{4}\right)n_{2}+z_{1}\left(\frac{1}{4}\right)n_{3}-z_{2}\left(\frac{\theta}{4}\right)z_{2}+n_{4}\propto \theta^{z_{2}+n_{4}}(1-\theta)^{z_{1}+n_{2}}$$

对数似然函数为：

$$I(\theta;n,z)=(z_{2}+n_{4})\ln(\theta)+(z_{1}+n_{2})\ln(1-\theta)$$

下面利用EM算法分两步计算：

E步：在已有观测数据 y 和第 i 估计值 $\theta^{(i)}$ 的条件下，求基于完全数据的对数似然函数的期望：

$$Q(\theta|y,\theta^{(i)})=E_{z}I(\theta;y,z)$$

M步：求 $Q(\theta|y,\theta^{(i)})$ 关于 $\theta$ 的最大值 $\theta^{(i+1)}$：

$$Q(\theta^{(i+1)}|y,\theta^{(i)})=\max_{\theta}Q(\theta|y,\theta^{(i)})$$

上两步反复迭代计算，收敛时即求得了 $\theta$ 的MLE。

# 3.3.2 利用EM算法进行Gauss混合分布的参数估计

混合高斯模型有 $M$ 个部分，每个部分的权重为 $a_k$。则随机变量 $x$ 的概率密度函数为：

$$p(x|\theta)=\sum_{k=1}^{M}a_k p_k(x|\theta_k),\sum_{k=1}^{M}a_k=1$$

其中 $\theta=(a_1,a_2,\cdots,a_M,\theta_1,\theta_2,\cdots,\theta_M)$

设样本观测值为 $X=\{x_1,x_2,\cdots,x_N\}$，则GMM的对数似然函数为：

$$\ln(L(\theta|X))=\ln\prod_{i=1}^{N}p(x_i|\theta)=\sum_{i=1}^{N}\sum_{k=1}^{M}\alpha_k p_k(x_i|\theta_k)$$

$$=\sum_{i=1}^{N}\sum_{k=1}^{M}\alpha_k \frac{1}{\sqrt{2\pi}\sigma}\exp\left(-\frac{(x_i-\mu_k)}{2\sigma_k^2}\right)$$

此时极大似然估计不易求得参数 $\theta=(a_1,a_2,\cdots,a_M,\theta_1,\theta_2,\cdots,\theta_M)$ 的估计，所以利用EM引入潜变量 $Y=\{y_1,y_2,\cdots,y_N\}$，且 $y_i\in\{1,2,\cdots,M\}$，$i=1,2,\cdots,N$。$y_i=k$ 是指第 $i$ 个观测样本是GMM的第 $k$ 部分产生的，则引入潜变量后的对数似然函数为：

$$\ln(L(\theta|x,y))=\ln\prod_{i=1}^{N}p(x_i,y_i|\theta)=\sum_{i=1}^{N}\ln(\alpha_{y_i}p_{y_i}(x_i|\theta_{y_i}))$$

下面用EM算法进行参数估计。

E步，求 $Q$ 函数：

$$Q(\theta|\theta^{(t-1)})=E_Y\{\ln[L(\theta|X,Y)]\}=\sum_{y}\ln[L(\theta|X,y)]p(y|X,\theta^{(t-1)})=\sum_{k=1}^{M}\sum_{i=1}^{N}\ln(\alpha_k p_k(x_i|\theta_k))p(k|x_i,\theta^{(t-1)})$$

其中：