\(\frac{2\mu}{\sigma^2}\) 对应②部分：

\[ \frac{2\tilde{\mu}_t(x_t, x_0)}{\tilde{\beta}_t} = \left( \frac{2\sqrt{\bar{\alpha}_{t-1}}x_0}{1-\bar{\alpha}_{t-1}} + \frac{2\sqrt{1-\beta_t}x_t}{\beta_t} \right) \tag{6-51} \]

根据公式 (6-50) 和公式 (6-51)，可以求得 \(q(x_{t-1} | x_t, x_0)\) 的均值 \(\tilde{\mu}_t(x_t, x_0)\) 和方差 \(\tilde{\beta}_t\)：

\[ \tilde{\mu}_t(x_t, x_0) = \frac{\sqrt{\bar{\alpha}_{t-1}}\beta_t x_0 + (1-\bar{\alpha}_{t-1})\sqrt{\alpha_t}x_t}{1-\bar{\alpha}_t}, \quad \tilde{\beta}_t = \frac{(1-\bar{\alpha}_{t-1})\beta_t}{1-\bar{\alpha}_t} \tag{6-52} \]

到目前为止，推导出了 \(q(x_{t-1} | x_t, x_0)\) 的详细分布，其中方差 \(\tilde{\beta}_t\) 为一个定量，由前面设置的方差超参数确定，而均值 \(\tilde{\mu}_t(x_t, x_0)\) 依赖于 \(x_t\) 和 \(x_0\)。实际上，去噪从而得到 \(x_0\) 是 DDPM 逆向过程的最终目标，但是无法在当前步获取其准确值。针对这一问题，需要引入前向过程中已得到的 \(x_t = \sqrt{\bar{\alpha}_t}x_0 + \sqrt{1-\bar{\alpha}_t}\bar{\epsilon}_0, \bar{\epsilon}_0 \sim \mathcal{N}(0, \mathbf{I})\)，反之可以得到 \(x_0 = \frac{1}{\sqrt{\bar{\alpha}_t}}(x_t - \sqrt{1-\bar{\alpha}_t}\bar{\epsilon}_0)\)，代入公式 (6-52) 中的 \(\tilde{\mu}_t(x_t, x_0)\)，有

\[ \tilde{\mu}_t(x_t, x_0) = \frac{1}{\sqrt{\alpha_t}} \left( x_t - \frac{\beta_t}{\sqrt{1-\bar{\alpha}_t}}\bar{\epsilon}_0 \right) \tag{6-53} \]

虽然去除了 \(x_0\) 的影响，但是引入了一个新的变量 \(\bar{\epsilon}_0\)，\(\bar{\epsilon}_0\) 在前向过程中为标准高斯分布采样的噪声，但是在逆向过程中无法得知其真实值，DDPM 的做法是引入一个参数化的神经网络模型 \(\epsilon_\theta(x_t, t)\) 去预测高斯噪声 \(\bar{\epsilon}_0\)，\(\theta\) 表示模型的参数。

至此，已经可以确定 DDPM 需要学习的逆向过程 \(p_\theta(x_{t-1} | x_t)\) 的分布：

\[ p_\theta(x_{t-1} | x_t) = \mathcal{N}(x_{t-1}; \mu_\theta(x_t, t), \Sigma_\theta(x_t, t)) \tag{6-54} \]

其中：

\[ \mu_\theta(x_t, t) = \frac{1}{\sqrt{\alpha_t}} \left( x_t - \frac{\beta_t}{\sqrt{1-\bar{\alpha}_t}}\epsilon_\theta(x_t, t) \right), \quad \Sigma_\theta(x_t, t) = \tilde{\beta}_t = \frac{(1-\bar{\alpha}_{t-1})\beta_t}{1-\bar{\alpha}_t} \simeq \beta_t \tag{6-55} \]

因此，DDPM 逆向过程可总结为：在给定 \(x_t\) 的前提下，首先预测高斯噪声 \(\epsilon_\theta(x_t, t)\)，然后计算 \(p_\theta(x_{t-1} | x_t)\) 的均值 \(\mu_\theta(x_t, t)\) 和方差 \(\Sigma_\theta(x_t, t)\)，最后通过重参数技巧计算得到 \(x_{t-1}\) 完成一步推断，循环进行直至得到 \(x_0\)。

# 6.7.3 DDPM 的训练

DDPM 使用最大似然估计作为优化目标，损失函数为

\[ L = E_{q(x_0)} [-\log p_\theta(x_0)] \tag{6-56} \]

基于文献 [4] 和 [11]，应用变分下限（Variational Lower Bound，VLB）优化负对数似然，在原始损失函数 L 的基础上，引入一项 KL 散度 \(D_{KL}(q(x_{1:T} | x_0) \| p_\theta(x_{1:T} | x_0))\)，因为 KL