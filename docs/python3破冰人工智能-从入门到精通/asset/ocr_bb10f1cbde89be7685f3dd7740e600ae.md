通过上面的分析得到一元线性回归拟合图像，如图 9-3 所示。最终得到回归方程为 y=2x+20，同时绘制出了线性图形，回归系数为 2.0，常数项为 20.0。当 x=7 时，预测得出 y=34。

# 9.2.3 检验、预测与控制

# 1. 平方和分解公式

对任意一组数据 $(x_1,y_1),\dots,(x_n,y_n)$，恒有：

$$\sum_{i=1}^{n}(y_i-\bar y)^2 = \sum_{i=1}^{n}(y_i-\hat y_i)^2 + \sum_{i=1}^{n}(\hat y_i-\bar y)^2$$

证明：$\sum_{i=1}^{n}(y_i-\bar y)^2 = \sum_{i=1}^{n}(y_i-\hat y_i)^2 + 2\sum_{i=1}^{n}(y_i-\hat y_i)(\hat y_i-\bar y) + \sum_{i=1}^{n}(\hat y_i-\bar y)^2$

而

$$\sum_{i=1}^{n}(y_i-\hat y_i)(\hat y_i-\bar y)=\sum_{i=1}^{n}[y_i-(\hat\beta_0+\hat\beta_1x)][\hat\beta_0+\hat\beta_1x-(\hat\beta_0+\hat\beta_1\bar x)]$$ $$=\sum_{i=1}^{n}[(y_i-\bar y)-\hat\beta_1(x_i-\bar x)]\cdot\hat\beta_1(x_i-\bar x)^2$$ $$=\hat\beta_1\sum_{i=1}^{n}[(y_i-\bar y)(x_i-\bar x)-\hat\beta_1(x_i-\bar x)^2]=0$$

记作：

$$S_T=\sum_{i=1}^{n}(y_i-\bar y)^2\quad\text{为总离差平方和}$$ $$S_R=\sum_{i=1}^{n}(\hat y_i-\bar y)^2\quad\text{为回归平方和}$$ $$S_E=\sum_{i=1}^{n}(y_i-\hat y)^2\quad\text{为残差平方和}$$

其中，$S_R=\sum_{i=1}^{n}(\hat y_i-\bar y)^2=\hat\beta_1^2\sum(x_i-\bar x)^2$ $x_1,x_2,\dots,x_n$ 为偏差平方和。

定理 9.1：$S_R/\sigma^2 \sim \chi^2(1)$，$S_E/\sigma^2 \sim \chi^2(n-2)$
$$F=\frac{S_R}{S_E/(n-2)} \sim F(1,n-2)\qquad(9-7)$$

# 2. 回归模型的显著性检验

对回归方程 $Y=\beta_0+\beta_1x$ 的显著性检验，归结为对假设 $H_0:\beta_1=0; H_1:\beta_1\neq0$ 进行检验。假设 $H_0:\beta_1=0$ 被拒绝，则回归显著，认为 $y$ 与 $x$ 存在线性关系，所求的线性回归方程有意义；否则回归不显著，$y$ 与 $x$ 的关系不能用一元线性回归模型来描述，所得到的回归方程也无