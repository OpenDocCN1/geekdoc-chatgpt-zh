络就是对具有不同投影矩阵的同一输入序列应用 $h$ 次的注意力网络计算。注意力层首先通过矩阵 $W_i^Q$、$W_i^K$ 和 $W_i^V$ 将每个 $Q_{in}$、$K_{in}$ 和 $V_{in}$ 投射到一个潜在空间中。

$Q_i = \left[q_i^1, \cdots, q_i^k \right] = Q_{in} W_i^O$ \tag{6.45}  
$K_i = \left[k_i^1, \cdots, k_i^k \right] = K_{in} W_i^K$ \tag{6.46}  
$V_i = \left[v_i^1, \cdots, v_i^k \right] = V_{in} W_i^V$ \tag{6.47}

式中，$q$、$k$ 和 $v$ 分别为投影的查询、键和值。每个值与给定查询的相关性由查询与值对应的键之间的点积决定。SAINT 模型的注意力网络需要一种屏蔽机制，阻止当前位置关注后续位置。掩蔽机制（MASK）将矩阵 $Q_i K_i^T$ 的上三角部分替换为 $-\infty$ 的点积，经过 Softmax 运算后，具有消零后续位置注意力权值的效果。注意力头（head$_i$）是 $V_i$ 乘以掩蔽的注意力权重得到的。

$\text{head}_i = \text{Softmax}\left( \text{Mask}\left( \frac{Q_i K_i^T}{\sqrt{d}} \right) \right) V_i$ \tag{6.48}

$h$ 个注意力头的合并是通过乘以可学习的权重矩阵 ($W^O$) 来聚合不同注意力头的输出。这个连接张量是多头注意网络的最终输出。

$\text{Multihead}(Q_{in}, K_{in}, V_{in}) = \text{Concat}(\text{head}_1, \cdots, \text{head}_h) W^O$ \tag{6.49}

将位置前馈网络（FFN）应用于多头注意输出，以增加模型的非线性：

$F = \left(F_1, \cdots, F_k \right) = \text{FFN}(M)$ \tag{6.50}  
$F_i = \text{ReLU}(M_i W_1^{FF} + b_1^{FF}) W_2^{FF} + b_2^{FF}$ \tag{6.51}

式中，$M = \left[M_1, \cdots, M_k \right] = \text{Multihead}(Q_{in}, K_{in}, V_{in})$：$W_1^{FF}$、$W_2^{FF}$、$b_1^{FF}$ 及 $b_2^{FF}$ 为不同的 $M_i$ 共享的权重。

编码器由 $N$ 层堆叠而成，这些层分别是前馈网络（FFN）和多头注意力网络（Multihead）。每个相同层，用以下公式表示

$M = \text{SkipConct}(\text{Multihead}(\text{LayerNorm}(Q_{in}, K_{in}, V_{in})))$ \tag{6.52}  
$O = \text{SkipConct}(\text{FFN}(\text{LayerNorm}(M)))$ \tag{6.53}

式中，$O$ 为编码器的最终输出；每个子层应用残差连接（SkipConct）和层归一化（LayerNorm）。需要注意的是，第一层的 $Q_{in}$、$K_{in}$ 和 $V_{in}$ 为 $E^e$，是试题嵌入的顺序，后续层的输入是前一层的输出。

解码器也是由 $N$ 个相同层组成的堆栈，由多头注意力网络和前馈网络组成。与编码器类似，每个子层采用残差连接和层归一化。每个相同的层用以下公式表示：

$M_1 = \text{SkipConct}(\text{Multihead}(\text{LayerNorm}(Q_{in}, K_{in}, V_{in})))$ \tag{6.54}  
$M_2 = \text{SkipConct}(\text{Multihead}(\text{LayerNorm}(M_1, O, O)))$ \tag{6.55}