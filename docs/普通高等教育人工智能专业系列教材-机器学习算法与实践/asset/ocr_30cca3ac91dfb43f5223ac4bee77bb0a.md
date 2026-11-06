$$egin{cases}\min_{w,b,\xi}\frac12\|w\|^2 + C\sum_{i=1}^{N}\xi_i\text{s.t.}\ y_iig(w^T\phi(x_i)+big)\ge 1-\xi_i\xi_i\ge0,\ i=1,2,\cdots,N\end{cases}	ag{6-29}$$

注意到利用拉格朗日乘子法求解式（6-7）或式（6-22）时，在其对偶问题式（6-16）或者对偶问题式（6-23）中，无论是目标函数还是决策函数都只涉及样本 $x_i(i=1,2,\cdots,N)$ 之间的内积。因此对于式（6-29）的也只涉及 $\phi(x_i)(i=1,2,\cdots,N)$ 之间的内积 $\phi^T(x_i)\phi(x_j)$。由于新的空间维数可能很高，甚至是无穷维的，直接计算 $\phi^T(x_i)\phi(x_j)$ 是非常困难的，可以定义一个函数 $\kappa:\mathbb{R}^n\times\mathbb{R}^n\to\mathbb{R}$；

$$\kappa(x_i,x_j):=\phi^T(x_i)\phi(x_j),\quad\forall x_i,x_j\in\mathbb{R}^n\tag{6-30}$$

这里的函数 $\kappa(\cdot,\cdot)$ 称为核函数（kernel function），这种方法称为核技巧（kernel trick）。这等价于利用 $\phi$ 将原样本空间变换到新的空间，将原内积变换为 $\phi^T(x_i)\phi(x_j)$，在新的空间训练线性支持向量机。那么，在核函数 $\kappa(\cdot,\cdot)$ 确定的情况下，可以利用求解线性分类的方法求解非线性分类问题。注意到，在不知道 $\phi$ 的具体形式时，并不知道什么样的函数适合做核函数，而核函数对于能否利用线性支持向量机求解非线性可分的分类问题至关重要。因此，核函数的选择是支持向量机最大的变数。

# 2. 对偶算法

# （1）对偶问题

引入核函数 $\kappa(\cdot,\cdot)$ 之后，回顾线性可分支持向量机与线性支持向量机的对偶问题，即式（6-16）和式（6-23），类似地，式（6-29）的对偶问题的目标函数均可写为

$$W(\lambda)=\frac12\sum_{i=1}^{N}\sum_{j=1}^{N}\lambda_i\lambda_j y_i y_j \kappa(x_i,x_j)-\sum_{i=1}^{N}\lambda_i\tag{6-31}$$

约束条件不变，即式（6-29）的对偶问题为

$$\begin{cases}\min_{\lambda}\frac12\sum_{i=1}^{N}\sum_{j=1}^{N}\lambda_i\lambda_j y_i y_j \kappa(x_i,x_j)-\sum_{i=1}^{N}\lambda_i\\\text{s.t.}\ \sum_{i=1}^{N}\lambda_i y_i=0\\\lambda_i\ge0\text{或者}\ 0\le\lambda_i\le C,\ i=1,2,\cdots,N\end{cases}\tag{6-32}$$

式中，对于第二个约束条件，对于线性可分问题选取 $\lambda_i\ge0$，近似线性可分问题选取 $0\le\lambda_i\le C$。

类似的原始问题式（6-28）的参数为

$$w^*=\sum_{i=1}^{N}\lambda_i^* y_i\phi(x_i),\quad b^*=y_j-\sum_{i=1}^{N}\lambda_i^* y_i\kappa(x_i,x_j)\tag{6-33}$$

式中，对于下标 $j$ 的选取，新空间中线性可分问题选取 $\lambda_j>0$，新空间中近似线性可分问题选取 $0<\lambda_j<C$。

决策函数也可写为

$$\begin{aligned}f(x)&=\operatorname{sign}\Big(\sum_{i=1}^{N}\lambda_i^* y_i\phi^T(x_i)\phi(x)-\sum_{i=1}^{N}\lambda_i^* y_i\phi^T(x_i)\phi(x_j)+y_j\Big)\\&=\operatorname{sign}\Big(\sum_{i=1}^{N}\lambda_i^* y_i\kappa(x_i,x)-\sum_{i=1}^{N}\lambda_i^* y_i\kappa(x_i,x_j)+y_j\Big)\end{aligned}\tag{6-34}$$