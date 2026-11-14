= E_{q(x_{0:T}')} \left[ - \log p_{\theta}(x_T) + \sum_{t=2}^T \log \frac{q(x_{t-1} | x_t, x_0)}{p_{\theta}(x_{t-1} | x_t)} + \sum_{t=2}^T \log \frac{q(x_t | x_0)}{q(x_{t-1} | x_0)} + \log \frac{q(x_1 | x_0)}{p_{\theta}(x_0 | x_1)} \right]  
= E_{q(x_{0:T}')} \left[ - \log p_{\theta}(x_T) + \sum_{t=2}^T \log \frac{q(x_{t-1} | x_t, x_0)}{p_{\theta}(x_{t-1} | x_t)} + \log \prod_{t=2}^T \frac{q(x_t | x_0)}{q(x_{t-1} | x_0)} + \log \frac{q(x_1 | x_0)}{p_{\theta}(x_0 | x_1)} \right]  
= E_{q(x_{0:T}')} \left[ - \log p_{\theta}(x_T) + \sum_{t=2}^T \log \frac{q(x_{t-1} | x_t, x_0)}{p_{\theta}(x_{t-1} | x_t)} + \log \frac{q(x_T | x_0)}{q(x_1 | x_0)} + \log \frac{q(x_1 | x_0)}{p_{\theta}(x_0 | x_1)} \right]  
= E_{q(x_{0:T}')} \left[ \underbrace{\log \frac{q(x_T | x_0)}{q(x_1 | x_0) p_{\theta}(x_0 | x_1) p_{\theta}(x_T)}}_{全部为常数} + \sum_{t=2}^T \log \frac{q(x_{t-1} | x_t, x_0)}{p_{\theta}(x_{t-1} | x_t)} \right]  
= E_{q(x_{0:T}')} \left[ \log \frac{q(x_T | x_0)}{p_{\theta}(x_T)} + \sum_{t=2}^T \log \frac{q(x_{t-1} | x_t, x_0)}{p_{\theta}(x_{t-1} | x_t)} - \log p_{\theta}(x_0 | x_1) \right]  
= E_{q(x_{0:T}')} \left[ D_{KL}(q(x_T | x_0) \| p_{\theta}(x_T)) + \sum_{t=2}^T \underbrace{D_{KL}(q(x_{t-1} | x_t, x_0) \| p_{\theta}(x_{t-1} | x_t))}_{l_t} - \log p_{\theta}(x_0 | x_1) \right]

DDPM 对上述优化目标进行了简化，只考虑公式（6-60）中的 \( L_t \) 部分，即计算分布 \( q(x_{t-1} | x_t, x_0) \) 与 \( p_{\theta}(x_{t-1} | x_t) \) 之间的 KL 散度，且由逆向过程可知

q(x_{t-1} | x_t, x_0) = \mathcal{N}(x_{t-1}; \tilde{\mu}_t(x_t, x_0), \tilde{\beta}_t I)  
p_{\theta}(x_{t-1} | x_t) = \mathcal{N}(x_{t-1}; \mu_{\theta}(x_t, t), \Sigma_{\theta}(x_t, t)) = \mathcal{N}(x_{t-1}; \mu_{\theta}(x_t, t), \tilde{\beta}_t I)

因此

L_t = D_{KL}(q(x_{t-1} | x_t, x_0) \| p_{\theta}(x_{t-1} | x_t))  
= \left[ \frac{1}{2\beta_t} \| \tilde{\mu}_t(x_t, x_0) - \mu_{\theta}(x_t, t) \|^2 \right]  
= E_{x_0, \epsilon_0} \left[ \frac{1}{2\beta_t} \| \frac{1}{\sqrt{\alpha_t}} \left( x_t - \frac{\beta_t}{\sqrt{1 - \bar{\alpha}_t}} \epsilon_0 \right) - \frac{1}{\sqrt{\alpha_t}} \left( x_t - \frac{\beta_t}{\sqrt{1 - \bar{\alpha}_t}} \epsilon_{\theta}(x_t, t) \right) \|^2 \right]  
= E_{x_0, \epsilon_0} \left[ \frac{\beta_t^2}{2\beta_t \alpha_t (1 - \bar{\alpha}_t)} \| \bar{\epsilon}_0 - \epsilon_{\theta}(x_t, t) \|^2 \right]  
= E_{x_0, \bar{\epsilon}_0} \left[ \frac{\beta_t^2}{2\beta_t \alpha_t (1 - \bar{\alpha}_t)} \| \bar{\epsilon}_0 - \epsilon_{\theta} \left( \sqrt{\bar{\alpha}_t} x_0 + \sqrt{1 - \bar{\alpha}_t} \bar{\epsilon}_0, t \right) \|^2 \right]

DDPM 进一步对 \( L_t \) 进行简化，得到

L_{simple} = E_{x_0, \bar{\epsilon}_0} \left[ \| \bar{\epsilon}_0 - \epsilon_{\theta} \left( \sqrt{\bar{\alpha}_t} x_0 + \sqrt{1 - \bar{\alpha}_t} \bar{\epsilon}_0, t \right) \|^2 \right]

可以发现，DDPM 最终损失函数的计算十分简单，核心就是最小化采样的真实噪声 \( \bar{\epsilon}_0 \) 与模型所预测噪声 \( \epsilon_{\theta} \) 之间的均方误差损失。

# 6.8 深度生成模型的应用

深度信念网络及其贪婪训练的思想使得高效训练深度网络成为可能，通常用来初始化深