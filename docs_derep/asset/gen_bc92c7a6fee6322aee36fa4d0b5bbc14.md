# 掩码与堆叠：Transformer 编码器是怎么搭起来的

[PRE0]
`get_attn_pad_mask(seq_q, seq_k)` 这段代码在干什么，先把角色理清：`seq_q` 是 Query 序列，`seq_k` 是 Key 序列。两个张量的形状里都包含 `batch_size`，分别对应 Query 的长度 `len_q` 和 Key 的长度 `len_k`。

做法是先用 `seq_k.data.eq(0)` 得到一个布尔矩阵，标记出 Key 里哪些位置是填充 `<pad>`。这里默认填充标记的词表索引是 0，所以只要是 0 就被判定为填充位。接着 `unsqueeze(1)` 给矩阵加一维，形状变成 `batch_size×1×len_k`。随后 `expand(batch_size, len_q, len_k)` 把它广播到 `batch_size × len_q × len_k`。注意 expand 只是复用已有数据，并不会产生新信息。

在多头自注意力算注意力权重时，这个填充掩码会被加到原始的分数上。被掩掉的位置分数被压到接近负无穷，softmax 归一化后基本趋近 0，模型也就自动忽略掉这些填充部分了。

#### 组件5：编码器层拆解

有了多头自注意力和逐位置前馈这两个核心算子，再加上正弦位置编码表和填充掩码这两个辅助工具，编码器层就可以搭建起来了。

[PRE1]
##### EncoderLayer 结构解析

`EncoderLayer` 的 `__init__` 里只做两件事的实例化。

1. **多头自注意力层**：创建 `MultiHeadAttention` 实例 `enc_self_attn`，负责序列内部的自注意力。
2. **逐位置前馈层**：创建 `PoswiseFeedForwardNet` 实例 `pos_ffn`，对自注意力后的表示做进一步的特征提炼。

`forward` 接收两个输入：

- `enc_inputs`，待编码的序列；
- `enc_self_attn_mask`，自注意力时使用的掩码，通常就是填充掩码。

内部执行顺序：

1. 把 `enc_inputs` 同时作为 Q、K、V 送入 `enc_self_attn`，并带上 `enc_self_attn_mask`，得到 `enc_outputs` 和注意力权重 `att_weights`。
2. 将 `enc_outputs` 再喂给 `pos_ffn`，更新后的结果继续命名为 `enc_outputs`。
3. 最后返回 `enc_outputs` 与 `attn_weights`。前者是本层的编码结果，后者可以用来做分析和可视化。

##### 维度拆解

在 `MultiHeadAttention` 输出里：

- **enc_outputs** 形状是 `[batch_size, seq_len, embedding_dim]`。内部先把输入映射成 Q、K、V，维度变为 `[batch_size, n_heads, seq_len, d_k]`。自注意力计算后形状不变，最后把头合并，把最后两维拼接起来，`n_heads × d_k` 正好等于 `embedding_dim`，于是得到 `[batch_size, seq_len, embedding_dim]`。
- **attn_weights** 形状保持 `[batch_size, n_heads, seq_len, seq_len]`。先算 Q、K 点积得到分数，经 softmax 归一化后，每个批次、每个头、每个位置都有一个长度 `seq_len` 的权重向量，描述该位置与序列其它位置的关系强度。注意这里不会像输出那样合并头，所以维度保留为 `[batch_size, n_heads, seq_len, seq_len]`。

如图所示，这一层完整复现了 Transformer 编码器单层的工作方式：自注意力加前馈。实际模型里会把多层这样堆叠，形成深度网络，捕捉更丰富的序列特征。

#### 组件6：编码器堆叠

编码器就是把多个编码器层叠起来，所以才有了“层峦叠翠上青天”这个说法。

##### 编码器类实现

`n_layers = 6` 定义编码器层数

```python
class Encoder(nn.Module):
```

`__init__` 里完成三项初始化：

1. 词嵌入 `src_emb = nn.Embedding(len(corpus.src_vocab), d_embedding)`，把源词映射成向量。
2. 位置嵌入 `pos_emb = nn.Embedding.from_pretrained(get_sin_enc_table(corpus.src_len+1, d_embedding), freeze=True)`，使用预先算好的正弦位置编码表，并冻结参数不更新。
3. 层列表 `self.layers = nn.ModuleList(EncoderLayer() for _ in range(n_layers))`，把 `n_layers` 个 `EncoderLayer` 按序存放。

`forward` 只接收 `enc_inputs`，形状 `[batch_size, source_len]`。

流程如下：

1. 生成位置索引 `pos_indices = torch.arange(1, enc_inputs.size(1) + 1).unsqueeze(0).to(enc_inputs.device)`，形状 `[1, source_len]`。
2. 词嵌入加位置嵌入：`enc_outputs = self.src_emb(enc_inputs) + self.pos_emb(pos_indices)`，得到 `[batch_size, source_len, embedding_dim]`。
3. 生成自注意力掩码：`enc_self_attn_mask = get_attn_pad_mask(enc_inputs, enc_inputs)`，形状 `[batch_size, len_q, len_k]`。
4. 初始化空列表 `enc_self_attn_weights` 用来收集各层的注意力权重。
5. 遍历 `self.layers`，每层输出更新 `enc_outputs` 并把本层的权重追加到列表里。
6. 最终返回 `enc_outputs` 与 `enc_self_attn_weights`。`enc_outputs` 形状仍是 `[batch_size, seq_len, embedding_dim]`，`enc_self_attn_weights` 是个列表，每个元素形状 `[batch_size, n_heads, seq_len, seq_len]`，可用于后续分析。

[PRE2]
编码器类的构造逻辑很清晰：词嵌入负责语义映射，位置嵌入提供顺序信息，层叠的编码器层完成深度建模，填充掩码确保模型不被无用位置干扰。这一整套组合构成了 Transformer 编码侧的基础骨架。