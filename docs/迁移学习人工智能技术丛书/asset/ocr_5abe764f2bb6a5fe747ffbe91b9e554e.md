其中 c、$	heta_y$、$f$、$f'$、$\mathbf{x}_l$、$	heta'_x$ 分别表示第 c 个类别、关于第 c 个类别的模型、源域中的特征、目标域中的特征、目标域中的某一样本、关于样本 $\mathbf{x}_l$ 的模型。TLRisk 通过直接评估经验误差 $R(\mathbf{x}_l, c)$ 对目标样本 $\mathbf{x}_l$ 进行分类，将样本确定为可以最小化损失函数的类别。根据 Dai 等人的研究 (2008)，$R(\mathbf{x}_l, c) \propto \Delta(	heta'_x, 	heta_y) \propto KL(p(f'|	heta_y) || p(f'|	heta'_x))$。源域被翻译并用于计算 $p(f'|	heta_y)$:

```
$p(f'|	heta_y) = \int_{\mathcal{X}_l} \sum_{c' \in \mathcal{C}} p(f'|f') p(f'|c) p(c|\theta_y) d\mathbf{f} + \sum_{c \in \mathcal{C}} p(f'|c) p(c|\theta_y)$  
(6. 19)
```

式 (6.19) 中的 $p(f'|f')$ 是通过文本和图像之间的对应关系建立的特征级别翻译器。随后，Chen 等人 (2010b) 按着这项工作首次提出面向图片广告系统的异构迁移学习，这种方法在没有前后文本的情况下为图片推荐广告。

Kulis 等人 (2011) 指出 TLRisk 的鲁棒性高度依赖高质量的对应关系数据，并提出了非对称正则化的跨域转换方法 (Asymmetric Regularized Cross-domain transformation, ARC-t)，该方法利用所有域中的标签来学习翻译器。ARC-t 引入了相似性和差异性的约束，即域间一对具有相同标签的样本应该在翻译后具备更高的相似度，而带有不同标签的样本应该在翻译后具备更低的相似度。其目标函数如下：

```
$\min_T \Omega(T) + \lambda \sum_{i,j} c((\mathbf{x}_i^t)^T T \mathbf{x}_j^t)$  
(6. 20)
```

其中 $\Omega$ 将翻译器 $T$ 的复杂度进行正则化约束。当样本 $\mathbf{x}_j^t$ 和 $\mathbf{x}_i^t$ 属于同一个类别时，函数 $c(\cdot \cdot)$ 定义为 $c((\mathbf{x}_i^t)^T T \mathbf{x}_j^t) = (\max(0, 1 - (\mathbf{x}_j^t)^T T \mathbf{x}_i^t))^2$；而当 $\mathbf{x}_j$ 和 $\mathbf{x}_i^t$ 属于不同类别时，函数 $c(\cdot \cdot)$ 定义为 $c((\mathbf{x}_j^t)^T T \mathbf{x}_i^t) = (\max(0, (\mathbf{x}_j^t)^T T \mathbf{x}_i^t - u))^2$。经过翻译的源域样本可以和目标域样本一起训练，以应用于多种任务，比如分类任务。

Hoffman 等人 (2013) 设计了一种端到端的模型：最大边界域变换 (Max-Margin Domain Transform, MMDT)。这种模型利用带有标签的样本，从所有域中同时学习一个分类器和一个翻译器。MMDT 利用线性转换矩阵桥接不同域，并将其与一个 SVM 类型的最大边界分类器结合。其最终的目标函数为

```
$\min_{T, \mathbf{w}, b} \frac{1}{2} \|T\|_F^2 + \frac{1}{2} \|\mathbf{w}\|^2$  
s. t. $y_i^t \left[ \begin{pmatrix} \mathbf{x}_i^t \\ 1 \end{pmatrix}^T \begin{bmatrix} \mathbf{w} \\ b \end{bmatrix} \right] \geqslant 1 \quad \forall i \in \mathcal{D}_t,$  
$y_i^t \left[ \begin{pmatrix} \mathbf{x}_i^t \\ 1 \end{pmatrix}^T T^r \begin{bmatrix} \mathbf{w} \\ b \end{bmatrix} \right] \geqslant 1 \quad \forall i \in \mathcal{D}_t$  
(6. 21)
```

其中 $T \in R^{d' \times d}$ 是翻译矩阵，将每一个目标域的样本 $\mathbf{x}^t \in R^{d' \times 1}$ 翻译成源域空间的一个 $d$ 维向量，$\mathbf{w}$