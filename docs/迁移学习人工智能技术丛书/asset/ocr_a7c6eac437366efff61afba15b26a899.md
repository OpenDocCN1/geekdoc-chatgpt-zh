# 2.2.2 核平均匹配

分布的核嵌入（kernel embedding of distribution）是另一种估算密度比的有效方法（Smola等人，2007a）。例如，Huang 等人（2006）提出了核平均匹配（Kernel Mean Matching，KMM）方法，通过在再生核希尔伯特空间（Reproducing Kernel Hilbert Space，RKHS）内将源域数据样本的均值与目标域数据样本的均值对齐，直接学习密度比。

特别地，我们用 $\beta_i$ 表示 $\frac{P_i(x_i^t)}{P_i(x_i^s)}$，$x_i^t$ 为源域数据样本。定义 $\boldsymbol{\beta} = (\beta_1, \beta_2, \cdots, \beta_n)$，$n_i$ 是源域数据集的大小。KMM 利用最大均值差异（Gretton等人，2007）计算不同分布间的距离。给定两个样本，根据 MMD，两个样本的分布距离等于两个分布的均值元素在 RKHS 中的距离。因此，KMM 通过匹配重加权的源域和目标域在 RKHS 上的样本均值来学习源域样本的权重

$$\min_{\boldsymbol{\beta}} \| \mu(\mathbb{P}_t^X) - \mathbb{E}_{x_i^s} [\beta(\boldsymbol{x}) \Phi(\boldsymbol{x})] \| \quad \text{s. t.} \quad \beta(\boldsymbol{x}) \geqslant 0, \mathbb{E}_{x_i^t} [\beta(\boldsymbol{x}) \Phi(\boldsymbol{x})] = 1 \quad (2.6)$$

其中 $\Phi$ 将源域样本转化到 RKHS $\mathscr{F}$ 上，$\mu(\mathbb{P}_t^X)$ 表示目标域样本在 RKHS 上的期望，即 $\mu(\mathbb{P}_t^X) = \mathbb{E}_{x_i^t} [\Phi(\boldsymbol{x})]$。

实际中，可以优化下面的经验目标：

$$\min_{\boldsymbol{\beta}} \left\| \frac{1}{n_s} \sum_{i=1}^{n_s} \beta \Phi(\boldsymbol{x}_i^s) - \frac{1}{n_t} \sum_{i=1}^{n_t} \Phi(\boldsymbol{x}_i^t) \right\|^2 \quad \text{s. t.} \quad \beta_i \geqslant 0, \left| \frac{1}{n_s} \sum_{i=1}^{n_s} \beta_i - 1 \right| \leqslant \varepsilon \quad (2.7)$$

其中 $\varepsilon$ 是正实数。优化 $\boldsymbol{\beta}$ 后，将其代入式 (2.4) 中，通过特定的损失函数为目标域学习预测模型 $\theta_t^*$。

# 2.2.3 函数估计

第三种估算密度比的代表性方法是将密度比视为一个未知函数，并通过学习一组基函数的组合来估算它。这种方法也被称为协变量移位法（covariate shift method）（Sugiyama等人，2008）。具体来说，通过定义 $\frac{P_t(\boldsymbol{x})}{P_s(\boldsymbol{x})}$ 为函数 $\omega(\boldsymbol{x})$，$\omega(\boldsymbol{x})$ 可以写成一组基函数的线性组合：

$$\widetilde{\omega}(\boldsymbol{x}) = \sum_{l=1}^{b} a_l \phi_l(\boldsymbol{x})$$

其中 $\boldsymbol{a} = (a_1, \cdots, a_b)^T$ 是需要学习的系数，$\phi_l(\cdot \cdot)$ 是第 $l$ 个基函数，可以是线性的或非线性的。这样，$P_t(\boldsymbol{x})$ 可通过 $\widetilde{P}_t(\boldsymbol{x}) = \widetilde{\omega}(\boldsymbol{x}) P_s(\boldsymbol{x})$ 估计。系数 $\boldsymbol{a}$ 可以通过最小化 $P_t(\boldsymbol{x})$ 和 $\widetilde{P}_t(\boldsymbol{x})$ 间的损失函