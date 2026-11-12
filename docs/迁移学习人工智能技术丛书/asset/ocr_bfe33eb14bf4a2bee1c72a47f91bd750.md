实例的表示可以认为是一个 C 通道的 “图像”。C 通道包含的像素可以通过空间聚类聚类如下：

\(\mathscr{R}_{spatial}(Y, \mu) = \frac{1}{2NC HW} \sum_{i}^{NHW} \| T^{N,H,W) \times (G} (Y)_i - \mu_{x_i} \|^2\) (3.13)

另外，可以通过使用以下损失函数在通道上执行聚类。

\(\mathscr{R}_{spatial}(Y, \mu) = \frac{1}{2NC HW} \sum_{i}^{NC} \| T^{N,C) \times (H, W)} (Y)_i - \mu_{x_i} \|^2\) (3.14)

Liao 等人 (2016) 著重研究了聚类的表示是否适用于没有训练过的类别，这是一个零样本学习问题。给定使用式 (3.12) 中的损失函数训练的特征，可以通过结构化 SVM 来学习输出嵌入 E 而无须正则化：

\(\min_E \frac{1}{N} \sum_{n=1}^{N} \max_{y \notin \mathscr{Y}} (0, \Delta(y_n, y) + x_n^T E (\phi(y) - \phi(y_n)))\) (3.15)

其中 \(x_n\) 和 \(y_n\) 分别是第 n 个样本的特征和类标签，\(\Delta\) 是 0-1 损失函数，\(\phi\) 是 CUB 数据集提供的类属性矩阵，每个输入表示一个属性在给定类别中存在的可能性。

# 3.4 特征增强

Daumé III (2007) 提出了一种简单的域适应方法，该方法使用特定域的信息来增强源域和目标域数据的特征向量，并将其视为学习算法的新输入。

将 X 和 Y 分别定义为输入和输出空间。假设原始输入空间为 \(\tilde{\mathscr{X}} \in \mathbb{R}^f\)，则其提出的方法将原始输入空间增强到 \(\tilde{\mathscr{X}} \in \mathbb{R}^{3f}\)。源域和目标域的映射函数 \(\Phi' \cdot \Phi'' : \mathscr{X} \to \tilde{\mathscr{X}}\) 定义为

\(\Phi'(x) = \langle x, x, 0 \rangle, \quad \Phi''(x) = \langle x, 0, x \rangle\) (3.16)

其中，0 表示 F 维空间中的零向量。增强特征的第一部分为原始特征，第二部分和第三部分分别表示源域和目标域的特定特征。

可以容易地将上述方法泛化为核函数版本。假设每个数据点 x 被投射到具有相应内核 k：\(\mathscr{X} \times \mathscr{X} \to \mathscr{R}\) 的 RKHS 中，k 可以写作两个向量的点乘形式，即 \(k(x, x') \leqslant \langle \Phi(x), \Phi(x') \rangle_x\)，那么 \(\Phi'\) 和 \(\Phi''\) 定义如下：

\(\Phi'(x) = \langle \Phi(x), \Phi(x), 0 \rangle, \quad \Phi''(x) = \langle \Phi(x), 0, \Phi(x) \rangle\) (3.17)

用 \(\tilde{k}(x, x')\) 表示扩展的内核。当 x 和 x' 来自同一域中时，\(\tilde{k}(x, x') = \langle \Phi(x), \Phi(x') \rangle_x + \langle \Phi(x), \Phi(x') \rangle_x = 2k(x, x')\)。当 x 和 x' 来自不同域时，\(\tilde{k}(x, x') = \langle \Phi(x), \Phi(x') \rangle_x = k(x, x')\)。

考虑到以核作为相似性的度量方法，上述核公式直观可见，因为来自相同域的数据点本质上是跨域点的两倍。考虑到对目标标数据的测试，目标域的训练数据的影响应该是源点的两倍。