[PRE0]## 14.2.2 Block类精读

GPT-2 源码里 Block 类的关键实现就长这样：

[PRE1]

- <1> 这一阶段的 hidden_states 维度为 (batch_size, 1, nx)，nx 等价于 n_state、n_embed 和 all_head_size，数值都是 768，也就是实际形态是 (batch_size, 1, 768)。

- <2> 此时 layer_past 负责承载历史的 key 与 value，张量的整体形状是 (2, batch_size, num_head, seq_len, head_features)。