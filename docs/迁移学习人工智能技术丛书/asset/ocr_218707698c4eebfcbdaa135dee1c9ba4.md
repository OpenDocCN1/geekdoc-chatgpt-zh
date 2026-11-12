# 15.3.3.1 投票 ERM

投票 ERM 按如下方式工作：

+   1) 对于 $x \in \mathcal{D}_u$，生成投票标签 $v(x)：

$$v(x) = \begin{cases} 1 & \sum_{i=1}^m I[h_i(x) = 1] \geqslant \frac{m}{2} \\ 0 & \text{其他} \end{cases}$$

+   2) 找到最小化的 $w^* ：

$$w^* = \arg\min_w \frac{1}{n} \sum_{x \in \mathcal{S}_u} l(h(x; w), v(x)) + \frac{\lambda}{2} \|w\|_2^2$$

+   3) 从 $\mathbb{P}(b) \propto e^{-\epsilon \frac{5\epsilon}{8} \|b\|_2}$ 采样随机向量 $b$。

+   4) 输出 $w_{priv} = w^* + b$。

理论分析显示扰动输出 $w_{priv}$ 是 $\epsilon$-差分隐私的。

# 15.3.3.2 加权 ERM

投票 ERM 方法的主要问题是对单方决定的敏感性。为此，加权 ERM 方法被提出以解决这一问题。具体来说，$\alpha(x)$ 被定义为样本 $x$ 的 $m$ 个分类器的正投票比例：

$$ \alpha(x) = \frac{1}{m} \sum_{i=1}^m I[h_i(x) = 1] $$

然后加权 ERM 算法按如下方式工作：

+   1) 对所有 $i$ 计算 $\alpha(x_i)$。

+   2) 找到最小化的 $w^* ：

$$w^* = \arg\min_w \frac{1}{n} \sum_{x \in \mathcal{S}_u} l^*(h(x; w), \alpha(x)) + \frac{\lambda}{2} \|w\|_2^2$$

其中

$$l^*(\cdot) = \alpha(x) l(w^T x) + (1 - \alpha(x)) l(-w^T x)$$

+   3) 从 $\mathbb{P}(b) \propto e^{-\epsilon \frac{5\epsilon}{8} \|b\|_2}$ 采样随机向量 $b$。

+   4) 输出 $w_{priv} = w^* + b$。

理论分析显示 $w_{priv}$ 是 $\epsilon$-差分隐私的。