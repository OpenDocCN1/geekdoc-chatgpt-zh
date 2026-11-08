GPT-2 的 Attention 类内部是怎么跑的

在 Attention 类里，负责收尾工作的有 `merge_heads()`。它的作用是把多头聚合后的结果张量 `a` 从头的维度上压平。处理前 `a` 的形状是 `batch_size, num_head, 1, head_features`，合并后变成 `batch_size, 1, all_head_size`，相当于把所有头的特征拼接成一条完整的向量。

负责拆分的则是 `split_heads()`，它会对 query、key、value 三个输入张量进行切分，生成多头结构。`prune_heads()` 相对直接，可以把不需要的注意力头裁掉。

真正做计算的是 `_attn()`。这个函数是类的核心，专门完成 query、key、value 的多头注意力聚合。

进入 `forward()` 的入口逻辑分两条判断。

第一条看是否带入了编码器传来的 `encoder_hidden_states`。如果带了，就走交叉注意力 Cross_Attention 的流程；如果没有带，就走 GPT-2 自带的 Masked_Multi_Self_Attention 自注意力流程。

第二条判断是否传入了 `layer_past`。`layer_past` 的含义可以结合 GPT2Model 的 `forward` 里 `for i, (block, layer_past) in enumerate(zip(self.h, past_key_values)):` 的注释，以及 Attention `forward` 中 `if use_cache is True:` 下对 `present` 的注释来理解。

一旦 `forward` 接收到 `layer_past`，计算就必须限定在 Masked_Multi_Self_Attention 这个分支，因为交叉注意力的场景里并不需要用到 `layer_past`。

有了 `layer_past` 之后，当前 step 的 key 和 value 要和历史信息拼接，做法是：

1. 新的 key
先对 `layer_past[0]` 也就是 `past_key` 做一次 `transpose(-2, -1)`，形状从历史记录变成 `batch_size, num_head, head_features, seq_len`。当前步的 key 形状是 `batch_size, num_head, head_features, 1`。两者在最后一个维度 dim=-1 上拼接，把当前 token 的 key 接到历史序列后面，得到的新 key 形状为 `batch_size, num_head, head_features, seq_len+1`，有效长度 `new_seq_len = seq_len+1`。

2. 新的 value
`past_value` 本身形状就是 `batch_size, num_head, seq_len, head_features`，不需要变形。当前步的 value 形状是 `batch_size, num_head, 1, head_features`。在倒数第二个维度 dim=-2 上拼接，把当前 token 的 value 并入历史，得到的新 value 形状为 `batch_size, num_head, seq_len+1, head_features`，长度同样变为 `new_seq_len`。

这样历史缓存就能在后续生成时被持续累加使用。