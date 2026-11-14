
散度的值非负，因此满足下列不等式：

\( E_{q(x_{1:T})}[-\log p_\theta(x_0)] \leqslant E_{q(x_{1:T})}[-\log p_\theta(x_0) + D_{\text{KL}}(q(x_{1:T}|x_0) \| p_\theta(x_{1:T}|x_0))] \) (6-57)

其中，KL 散度的定义如下：

\( D_{\text{KL}}(q(x_{1:T}|x_0) \| p_\theta(x_{1:T}|x_0)) = \sum \left[ q(x_{1:T}|x_0) \cdot \log \frac{q(x_{1:T}|x_0)}{p_\theta(x_{1:T}|x_0)} \right] = E_{q(x_{1:T}|x_0)} \left[ \log \frac{q(x_{1:T}|x_0)}{p_\theta(x_{1:T}|x_0)} \right] \) (6-58)

将公式 (6-58) 代入公式 (6-57) 中，并应用贝叶斯公式进行化简，有

\( E_{q(x_{1:T})}[-\log p_\theta(x_0)] \leqslant E_{q(x_{1:T})} \left[ -\log p_\theta(x_0) + E_{q(x_{1:T}|x_0)} \left[ \log \frac{q(x_{1:T}|x_0)}{p_\theta(x_{1:T}|x_0)} \right] \right] = E_{q(x_{1:T})} \left[ -\log p_\theta(x_0) + E_{q(x_{1:T}|x_0)} \left[ \log \frac{q(x_{1:T}|x_0)}{\underbrace{p_\theta(x_{0:T})/p_\theta(x_0)}_{\text{由贝叶斯公式}}} \right] \right] = E_{q(x_{1:T})} \left[ -\log p_\theta(x_0) + E_{q(x_{1:T}|x_0)} \left[ \log \frac{q(x_{1:T}|x_0)}{p_\theta(x_{0:T})} + \log p_\theta(x_0) \right] \right] = E_{q(x_{1:T})} \left[ E_{q(x_{1:T}|x_0)} \left[ \log \frac{q(x_{1:T}|x_0)}{p_\theta(x_{0:T})} \right] \right] = E_{q(x_{0,T})} \left[ \log \frac{q(x_{1:T}|x_0)}{p_\theta(x_{0:T})} \right] = L_{\text{VLB}} \) (6-59)

此时，DDPM 的优化目标可以从最小化 \( L \) 转换为最小化 \( L_{\text{VLB}} \)，由前向过程和逆向过程的介绍可知 \( q(x_{1:T}|x_0) = \Pi_{i=1}^T q(x_t|x_{t-1}), p_\theta(x_{0:T}) = p_\theta(x_T) \Pi_{i=1}^T p_\theta(x_{i-1}|x_i) \)，代入公式 (6-59) 可进一步推导导出：

\( L_{\text{VLB}} = E_{q(x_{0:T})} \left[ \log \frac{q(x_{1:T}|x_0)}{p_\theta(x_{0:T})} \right] = E_{q(x_{0:T})} \left[ \log \frac{\Pi_{i=1}^T q(x_t|x_{i-1})}{p_\theta(x_T) \Pi_{i=1}^T p_\theta(x_{i-1}|x_i)} \right] = E_{q(x_{0:T})} \left[ -\log p_\theta(x_T) + \sum_{i=1}^T \log \frac{q(x_i|x_{i-1})}{p_\theta(x_{i-1}|x_i)} \right] = E_{q(x_{0:T})} \left[ -\log p_\theta(x_T) + \sum_{i=2}^T \log \frac{q(x_i|x_{i-1})}{p_\theta(x_{i-1}|x_i)} + \log \frac{q(x_1|x_0)}{p_\theta(x_0|x_1)} \right] = E_{q(x_{0:T})} \left[ -\log p_\theta(x_T) + \sum_{i=2}^T \log \left( \frac{\underbrace{q(x_{i-1}|x_i, x_0)q(x_i|x_0)}_{\text{由公式 (6-44)}}}{p_\theta(x_{i-1}|x_i)q(x_{i-1}|x_0)} \right) + \log \frac{q(x_1|x_0)}{p_\theta(x_0|x_1)} \right] \)

\( = E_{q(x_{0:T})} \left[ - \log p_{\theta}(x_T) + \sum_{t=2}^T \log \frac{q(x_{t-1} | x_t, x_0)}{p_{\theta}(x_{t-1} | x_t)} + \sum_{t=2}^T \log \frac{q(x_t | x_0)}{q(x_{t-1} | x_0)} + \log \frac{q(x_1 | x_0)}{p_{\theta}(x_0 | x_1)} \right] = E_{q(x_{0:T})} \left[ - \log p_{\theta}(x_T) + \sum_{t=2}^T \log \frac{q(x_{t-1} | x_t, x_0)}{p_{\theta}(x_{t-1} | x_t)} + \log \prod_{t=2}^T \frac{q(x_t | x_0)}{q(x_{t-1} | x_0)} + \log \frac{q(x_1 | x_0)}{p_{\theta}(x_0 | x_1)} \right] = E_{q(x_{0:T})} \left[ - \log p_{\theta}(x_T) + \sum_{t=2}^T \log \frac{q(x_{t-1} | x_t, x_0)}{p_{\theta}(x_{t-1} | x_t)} + \log \frac{q(x_T | x_0)}{q(x_1 | x_0)} + \log \frac{q(x_1 | x_0)}{p_{\theta}(x_0 | x_1)} \right] = E_{q(x_{0:T})} \left[ \underbrace{\log \frac{q(x_T | x_0)}{q(x_1 | x_0) p_{\theta}(x_0 | x_1) p_{\theta}(x_T)}}_{全部为常数} + \sum_{t=2}^T \log \frac{q(x_{t-1} | x_t, x_0)}{p_{\theta}(x_{t-1} | x_t)} \right] = E_{q(x_{0:T})} \left[ \log \frac{q(x_T | x_0)}{p_{\theta}(x_T)} + \sum_{t=2}^T \log \frac{q(x_{t-1} | x_t, x_0)}{p_{\theta}(x_{t-1} | x_t)} - \log p_{\theta}(x_0 | x_1) \right] = E_{q(x_{0:T})} \left[ D_{KL}(q(x_T | x_0) \| p_{\theta}(x_T)) + \sum_{t=2}^T \underbrace{D_{KL}(q(x_{t-1} | x_t, x_0) \| p_{\theta}(x_{t-1} | x_t))}_{l_t} - \log p_{\theta}(x_0 | x_1) \right] \)

DDPM 对上述优化目标进行了简化，只考虑公式（6-60）中的 \( L_t \) 部分，即计算分布 \( q(x_{t-1} | x_t, x_0) \) 与 \( p_{\theta}(x_{t-1} | x_t) \) 之间的 KL 散度，且由逆向过程可知

q(x_{t-1} | x_t, x_0) = \mathcal{N}(x_{t-1}; \tilde{\mu}_t(x_t, x_0), \tilde{\beta}_t I)  
p_{\theta}(x_{t-1} | x_t) = \mathcal{N}(x_{t-1}; \mu_{\theta}(x_t, t), \Sigma_{\theta}(x_t, t)) = \mathcal{N}(x_{t-1}; \mu_{\theta}(x_t, t), \tilde{\beta}_t I)

因此

L_t = D_{KL}(q(x_{t-1} | x_t, x_0) \| p_{\theta}(x_{t-1} | x_t))  
= \left[ \frac{1}{2\beta_t} \| \tilde{\mu}_t(x_t, x_0) - \mu_{\theta}(x_t, t) \|^2 \right]  
= E_{x_0, \epsilon_0} \left[ \frac{1}{2\beta_t} \| \frac{1}{\sqrt{\alpha_t}} \left( x_t - \frac{\beta_t}{\sqrt{1 - \bar{\alpha}_t}} \epsilon_0 \right) - \frac{1}{\sqrt{\alpha_t}} \left( x_t - \frac{\beta_t}{\sqrt{1 - \bar{\alpha}_t}} \epsilon_{\theta}(x_t, t) \right) \|^2 \right]  
= E_{x_0, \bar{\epsilon}_0} \left[ \frac{\beta_t^2}{2\beta_t \alpha_t (1 - \bar{\alpha}_t)} \| \bar{\epsilon}_0 - \epsilon_{\theta}(x_t, t) \|^2 \right]  
= E_{x_0, \bar{\epsilon}_0} \left[ \frac{\beta_t^2}{2\beta_t \alpha_t (1 - \bar{\alpha}_t)} \| \bar{\epsilon}_0 - \epsilon_{\theta} \left( \sqrt{\bar{\alpha}_t} x_0 + \sqrt{1 - \bar{\alpha}_t} \bar{\epsilon}_0, t \right) \|^2 \right]

DDPM 进一步对 \( L_t \) 进行简化，得到

L_{simple} = E_{x_0, \bar{\epsilon}_0} \left[ \| \bar{\epsilon}_0 - \epsilon_{\theta} \left( \sqrt{\bar{\alpha}_t} x_0 + \sqrt{1 - \bar{\alpha}_t} \bar{\epsilon}_0, t \right) \|^2 \right]

可以发现，DDPM 最终损失函数的计算十分简单，核心就是最小化采样的真实噪声 \( \bar{\epsilon}_0 \) 与模型所预测噪声 \( \epsilon_{\theta} \) 之间的均方误差损失。

## 6.8 深度生成模型的应用

深度信念网络及其贪婪训练的思想使得高效训练深度网络成为可能，通常用来初始化深度前馈神经网络的网络参数，再加上一层 Softmax 层形成一个用于分类的深度前馈神经网络。同时，深度信念网络也可以用来对高维数据（如图像、文本等）进行降维，同样取得了很好的效果。自编码器及其变种也可以用来初始化前馈神经网络的参数，同时在数据压缩、表示学习、数据去噪、异常识别等领域也有很多应用。扩散模型自提出以后，在图像生成领域成了主流模型，同时它也可应用于自然语言处理、多模态建模和时间序列建模等领域。

## 复习题

- 1. 请说明什么是深度生成模型，目前有哪些典型的深度生成模型？
- 2. 请说明什么是深度信念网络，它与 Sigmoid 信念网络、深度玻尔兹曼机有哪些不同？
- 3. 请说明什么是自编码器，它有哪些主要变种？
- 4. 请说明什么是深度自编码器，它是如何训练的？
- 5. 请说明扩散模型的基本原理。

## 实验题

- 1. 请基于手写数字识别数据集 MNIST，加入高斯噪声，构建一个去噪自编码器实现手写数字的重构。要求能够完成数据读取、网络设计、网络构建、模型训练和模型推理等过程。MNIST 数据集官方下载地址：http://yann.lecun.com/exdb/mnist/。
- 2. 请基于 Smithsonian Butterflies 数据集子集，使用扩散模型实现图像生成，并与 DCGAN 模型进行性能对比。要求能够完成数据读取、网络设计、网络构建、模型训练和模型推理等过程。Smithsonian Butterflies 数据集子集下载地址：https://huggingface.co/datasets/huggan/smithsonian_butterflies_subset。

## 参考文献

- [1] HINTON G E, OSINDERO S, TEH Y. A fast learning algorithm for deep belief nets [J]. Neural computation, 2006, 18: 1527-1554.
- [2] SALAKHUTDINOV R, HINTON G E. Deep boltzmann machines [C]. Proceedings of the 12th International Conference on Artificial Intelligence and Statistics, 2009: 448-455.
- [3] HINTON G E, SALAKHUTDINOV R R. Reducing the dimensionality of data with neural networks [J]. Science, 2006, 313 (5786): 504-507.
- [4] HO J, JAIN A, ABBEEL P. Denoising diffusion probabilistic models [C]. Advances in Neural Information Processing Systems 33, 2020: 6840-6851.
- [5] HOPFIELD J J. Neural networks and physical systems with emergent collective computational abilities [J]. Proceedings of the National Academy of Sciences of the USA, 1982, 79 (8): 2554-2558.
- [6] SALAKHUTDINOV R, MNIH A, HINTON G E. Restricted boltzmann machines for collaborative filtering [C]. Proceedings of the 24th International Conference on Machine Learning, 2007: 791-798.
- [7] NEAL R M. Connectionist learning of belief networks [J]. Artificial intelligence, 1992, 56 (1): 71-113.
- [8] RUMELHART D E, HINTON G E, WILLIAMS R J. Learning internal representations by error propagation [M] //RUMELHART D E, MCCLELLAND J L. Parallel distributed processing: explorations in the microstructure of cognition, volume 1; foundations. Cambridge: MIT Press, 1986: 318-362.
- [9] VINCENT P, LAROCHELLE H, BENGIO Y, et al. Extracting and composing robust features with denoising autoencoders [C]. Proceedings of the 25th International Conference on Machine Learning, 2008: 1096-1103.
- [10] ANDREW N. Sparse autoencoder [ EB/OL ]. https://web.stanford.edu/class/cs294a/sparseAutoencoder.pdf, 2011.
- [11] WENG L. What are diffusion models [ EB/OL ]. https://lilianweng.github.io/posts/2021-07-11-diffusion-models/#forward-diffusion-process , 2021.

## 本章人物：David E. Rumelhart 教授

David E. Rumelhart（1942~2011），斯坦福大学教授，认知心理学家，主要从事认知神经科学和人工智能领域的研究。1991 年当选为美国科学院院士，1996 年获美国心理学会（American Psychological Association）颁发的杰出科学贡献奖。2000 年，为了纪念他的重要贡献，认知科学学会（Cognitive Science Society）以他的名字设立了一个重要的国际奖项——David E. Rumelhart 认知科学理论基础贡献奖。

![](img/57b180d92f84b0150a5b5e4fc7583b76_208_0.png)

David E. Rumelhart 教授于 1967 年获斯坦福大学数学心理学博士学位，之后加入加州大学圣迭戈分校（University of California, San Diego）开展研究工作，一开始进行符号计算理论研究，20 世纪 70 年代中期转向神经网络研究。1987 年，重返斯坦福大学继续研究工作。20 世纪 90 年代，不幸罹患一种神经性退行性疾病——Pick 病，1998 年退休，2011 年去世。

David E. Rumelhart 教授在人工智能领域的主要贡献是与 Geoffrey Hinton 教授、Ronald Williams 一起提出了反向传播算法，应该说反向传播算法的提出一定程度上扭转了当时人工神经网络发展的颓势。此外，David E. Rumelhart 教授还最早提出了自编码器的思想，实现了图像数据的压缩和重构。
