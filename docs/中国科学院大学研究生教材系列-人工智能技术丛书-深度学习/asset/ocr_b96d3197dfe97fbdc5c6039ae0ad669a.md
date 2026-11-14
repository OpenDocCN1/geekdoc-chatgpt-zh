$$L_{loc}(t^u, v) = \sum_{i \in \{x,y,w,h\}} \text{smooth}_{L_1}(t_i^u - v_i) \quad (2-37)$$ 
$$\text{smooth}_{L_1}(x) = \begin{cases} 0.5x^2 & |x| < 1 \\ |x| - 0.5 & \text{其他情况} \end{cases} \quad (2-38)$$

其中 \( L_{cls}(p, u) \) 指的是类 \( u \) 为正例的对数损失，\( p = (p_0, p_1, \cdots) \) 是每个类别的概率，\( L_{loc}(t^u, v) \) 是定义在类 \( u \) 上的一组正例边框回归目标的损失，\( v = (v_x, v_y, v_w, v_h) \)，\( t^u \) 表示一组类 \( u \) 上的预测边框，\( t^u = (t_x^u, t_y^u, t_w^u, t_h^u) \)，\( \lambda[u \geqslant 1] \) 当 \( u \geqslant 1 \) 时为 1，其他情况为 0，\( \lambda \) 是边框回归损失的权重参数，\( \text{smooth}_{L_1}(x) \) 是平滑函数。

Faster R-CNN [14] 使用一种称作区域建议网络（Region Proposal Network，RPN）的全卷积神经网络来生成区域建议（Region Proposal），替代之前的选择性搜索算法。Faster R-CNN 的结构如图 2-42 所示。

![](bbox=[0.04, 0.345, 0.905, 0.64])

[图 2-42 Faster R-CNN 的结构(详见彩插)]

Faster R-CNN 首先使用一组由卷积、ReLU、池化组成的结构提取输入图像的特征图，特征图同时用于后续的 RPN 和 RoI Pooling。RPN 层用于生成区域建议，它通过 Softmax 判断区域是有目标的正例（Positive）还是没有目标的负例（Negative），再利用边框回归获得候选区域。RoI Pooling 层接收卷积和池化后的特征图和 RPN 层的候选区域，综合这些信息后送入全连接层判定目标类别，同时再次使用边框回归获得边框的最终精确位置。

在 RPN 中，使用长宽比为 1:1、1:2 和 2:1 三种矩形框，称作 Anchor。首先，为特征图每个位置生成 \( k \)（默认为 9）个 Anchor。其中分类用 Softmax 判断 Anchor 是有目标的正例（Positive Anchor）还是没有目标的负例（Negative Anchor），因此输出大小为 2k。回归实现