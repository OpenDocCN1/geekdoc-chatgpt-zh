(2) 在L2 中对绝对残差进行修正，使其适应异方差数据。

\(\bar{R}_i = \frac{|Y_i - \hat{\mu}(X_i)|}{\hat{\sigma}(X_i)} = \frac{R_i}{\hat{\sigma}(X_i)}, \quad i \in \text{L2}\)

或者

\(\bar{R}_i = \frac{|Y_i - \hat{\mu}(X_i)|}{\hat{\sigma}(X_i) + \gamma} = \frac{R_i}{\hat{\sigma}(X_i) + \gamma}\)

(3) 给定误差水平\(\alpha\)，计算上述\(\bar{R}\)的经验分布的分位点。

\(Q_{1-\alpha}(\bar{R}, \text{L2}) = (1-\alpha)\left(1 + \frac{1}{|\text{L2}|}\right) \text{ the empirical quantile of }\{\bar{R}_i : i \in \text{L2}\}\)

(4) 在测试集中，给定一个新的数据点\(X_{n+1}\)，我们可以得到其预测区间。

\(\hat{\sigma}(X_{n+1})Q_{1-\alpha}(\bar{R}, \text{L2})=\frac{R_m}{\hat{\sigma}(X_m)}\hat{\sigma}(X_{n+1})\)

\(\mathcal{C}(X_{n+1}) = [\hat{\mu}(X_{n+1}) - \hat{\sigma}(X_{n+1})Q_{1-\alpha}(\bar{R}, \text{L2}), \hat{\mu}(X_{n+1}) + \hat{\sigma}(X_{n+1})Q_{1-\alpha}(\bar{R}, \text{L2})]\)

在这个方法中得到的区间长度是\(2\hat{\sigma}(X_{n+1})Q_{1-\alpha}(\bar{R}, \text{L2})\)，其中\(\hat{\sigma}(X_{n+1})\)是可变的，因此也就弥补了简单共形预测方法无法适应异方差数据的缺陷。

最后，有一类方法将共形预测与弹球损失的思路结合在一起，具体步骤如下，也可以结合图 9-4 来学习，会更加直观。

(1) 将数据集按一定比例切分成训练集L1和验证集L2，选择一个合适的回归算法\(A\)，根据给定的误差水平\(\alpha\)，锁定上下分位点（\(\alpha/2\) 与 \(1 - \alpha/2\)），在L1中拟合这两个分位数回归模型\(\hat{q}_{\text{alo}}\)和\(\hat{q}_{\text{ahi}}\)。

\(\{\hat{q}_{\text{alo}}, \hat{q}_{\text{ahi}}\} \leftarrow A(\{(X_i, Y_i) : i \in \text{L1}\})\)

(2) 在L2 中计算出每个数据点的预测区间 \(\hat{C}(x) = [\hat{q}_{\text{alo}}(x), \hat{q}_{\text{ahi}}(x)]\)，绝对残差的修正规则如下。

\(E_i = \max\{\hat{q}_{\text{alo}}(X_i)-Y_i, Y_i-\hat{q}_{\text{ahi}}(X_i)\}\)

如果真实值小于下分位点，那么

\(E_i = |\hat{q}_{\text{alo}}(X_i)-Y_i| \quad (\text{点到下界的距离})\)

如果真实值大于上分位点，那么

\(E_i = |\hat{q}_{\text{ahi}}(X_i)-Y_i| \quad (\text{点到上界的距离})\)