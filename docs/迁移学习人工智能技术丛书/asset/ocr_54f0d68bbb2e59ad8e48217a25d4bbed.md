须准确预测共现的非枢轴。其次，在这两个域中，枢轴词表示必须相似。因此，CDWRL 的目标函数表示为
\(\min L(\mathbf{C}_i, \mathbf{W}_i) + L(\mathbf{C}_t, \mathbf{W}_t) + \lambda R(\mathbf{C}_i, \mathbf{C}_t)\)

其中 \(s, t\) 分别是源和目标域，\(\mathbf{C}\)、\(\mathbf{W}\) 分别是枢轴和非枢轴。\(L(\mathbf{C}, \mathbf{W}_i)\) 是基于排序的预测铰链损失 (hinge loss) 函数：

\(L(\mathbf{C}, \mathbf{W}_i) = \sum_{d \in D_i} \sum_{(c_i, w_i^+) \in d} \sum_{w_i^- \sim p(w_i)} \max(0, 1 - \mathbf{c}_i^T \mathbf{w}_i + \mathbf{c}_i^T \mathbf{w}_i^*)\)

其中 \(\mathbf{c}_i\)、\(\mathbf{W}_i\) 是文档中共现的枢轴和非枢轴，\(\mathbf{w}_i^*\) 是不与 \(\mathbf{c}_i\) 共现的枢轴，非枢轴的边缘分布 \(p(w)\) 能从语料计数中估计。相似地，\(L(\mathbf{C}_i, \mathbf{W}_i)\) 定义为

\(L(\mathbf{C}_i, \mathbf{W}_i) = \sum_{d \in D_i} \sum_{(c_i, w_i^+) \in d} \sum_{w_i^- \sim p(w_i)} \max(0, 1 - \mathbf{c}_i^T \mathbf{w}_i + \mathbf{c}_i^T \mathbf{w}_i^*)\)

正则化项 \(R(\mathbf{C}_i, \mathbf{C}_t)\) 定义为

\(R(\mathbf{C}_i, \mathbf{C}_t) = \frac{1}{2} \sum_{i=1}^k \| \mathbf{c}_i^d - \mathbf{c}_i^o \|\)

通过学习枢轴和非枢轴的嵌入，可以捕捉它们的语义关系与情感倾向并将其用于域适应。

# 17. 3. 3.3 对抗学习模型

生成对抗网络 (Goodfellow 等人，2014) 是一个用于各种任务（从图像风格迁移到数据扩充）的受欢迎的深度生成模型。GAN 的目标是学习生成分布 \(\mathbb{P} \hat{g}\) 来模拟真实的数据分布 \(\mathbb{P}_{data}^{x}\)。具体地，GAN 学习一个生成网络 \(G\) 和一个判别网络 \(D\)。\(G\) 从生成器分布 \(\mathbb{P} \hat{z}\) 中生成样本，\(D\) 学习判别样本来自 \(\mathbb{P} \hat{g}\) 还是 \(\mathbb{P}_{data}^{x}\)。GAN 的目标是优化下列的最小-最大损失函数：

\(\phi = \min_G \max_D (\mathbb{E}_{x \sim p_{data}} [\log D(x)] + \mathbb{E}_{z \sim p_z} [\log(1 - D(G(z)))])\) (17.7)

GAN 被广泛应用到许多方面，其中，对抗学习被用来衡量分布之间的差异。Ganin 和 Lempitsky (2015) 及 Ganin 等人 (2016) 应用对抗损失来测量两个分布之间的 H 散度，并提出了神经网络的域对抗训练 (Domain-Adversari training of Neural Network, DANN) 用于域适应。

与 GAN 相比，DANN 由三部分组成：特征提取器 \(G_f\)、类别预测器 \(G_y\) 和域分类器 \(G_d\)。输入 \(\boldsymbol{x}\) 首先通过 \(G_f\) 映射到 \(D\) 维特征表示 \(\boldsymbol{f} = G_f(\boldsymbol{x}; \boldsymbol{\theta}_f) \in \mathbb{R}^D\)，其中 \(\boldsymbol{\theta}_f\) 表示 \(G_f\) 中的参数。然后，特征表示 \(\boldsymbol{f}\) 通过 \(G_y\) 映射到标签 \(y\)，其中 \(G_y\) 的参数由 \(\boldsymbol{\theta}_y\) 表示。同时，特征表示 \(\boldsymbol{f}\) 也通过 \(G_d\) 映射到域标签 \(d\)，参数由 \(\boldsymbol{\theta}_d\) 表示。

在学习阶段，DANN 的目标是最小化源有标签数据的标签预测损失。因此，它对特征提取器和类别预测器的参数 \(\boldsymbol{\theta}_f\) 和 \(\boldsymbol{\theta}_y\) 都进行了优化，以最小化源域的经验损失。这就保证了特征 \(\boldsymbol{f}\) 在源