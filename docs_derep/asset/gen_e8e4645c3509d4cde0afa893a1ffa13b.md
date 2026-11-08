[PRE0]device = input_ids.device if input_ids is not None else inputs_embeds.device

先确定计算设备，优先使用 input_ids 所在设备，没有则 fallback 到 inputs_embeds 设备。随后构造位置索引。

'''<1> GPT2Model第一次迭代时输入GPT2Model的forward()函数中的past_key_values参数为None，此时past_length为0，input_shape[-1] + past_length就等于第一次迭代时输入的文本编码(input_ids)的seq_len维度本身，创建的position_ids张量形状为(batch_size, seq_len)'''

position_ids = torch.arange(past_length, input_shape[-1] + past_length, dtype=torch.long, device=device)
position_ids = position_ids.unsqueeze(0).view(-1, input_shape[-1])

# Attention mask
# attention_mask 用来屏蔽填充符号 [PAD]，让这些位置的注意力得分趋近无效
# 对应词向量的贡献在多头聚合阶段基本被忽略
if attention_mask is not None:
    assert batch_size > 0, "batch_size has to be defined and > 0"
    attention_mask = attention_mask.view(batch_size, -1)
    # 把二维掩码升维到四维 [batch_size, 1, 1, to_seq_length]
    # 方便与 [batch_size, num_heads, from_seq_length, to_seq_length] 的注意力分数广播相加
    attention_mask = attention_mask[:, None, None, :]
    # 保留位为1，待屏蔽位为0，屏蔽处填入 -10000 以便 softmax 时被忽略
    attention_mask = attention_mask.to(dtype=self.dtype)  # fp16 compatibility
    attention_mask = (1.0 - attention_mask) * -10000.0

# If a 2D or 3D attention mask is provided for the cross-attention
# we need to make broadcastable to [batch_size, num_heads, seq_length, seq_length]
# 当开启交叉注意且存在 encoder_hidden_states 时，先读取编码器批次和序列长度
# 并为其准备 encoder_attention_mask
if self.config.add_cross_attention and encoder_hidden_states is not None:
    encoder_batch_size, encoder_sequence_length, _ = encoder_hidden_states.size()
    encoder_hidden_shape = (encoder_batch_size, encoder_sequence_length)
    if encoder_attention_mask is None:
        encoder_attention_mask = torch.ones(encoder_hidden_shape, device=device)
    encoder_attention_mask = self.invert_attention_mask(encoder_attention_mask)
else:
    encoder_attention_mask = None

# Prepare head mask if needed
# 1.0 in head_mask indicate we keep the head
# attention_probs has shape bsz x n_heads x N x N
# head_mask has shape n_layer x batch x n_heads x N x N
# prune_heads()可结合https://github.com/huggingface/transformers/issues/850理解
head_mask = self.get_head_mask(head_mask, self.config.n_layer)

# 将 token id、类型 id、位置 id 送入词嵌入 wte 与位置嵌入 wpe
# 得到 inputs_embeds、position_embeds 以及 token_type_embeds
if inputs_embeds is None:
    inputs_embeds = self.wte(input_ids)
position_embeds = self.wpe(position_ids)
hidden_states = inputs_embeds + position_embeds
if token_type_ids is not None:
    token_type_embeds = self.wte(token_type_ids)
    hidden_states = hidden_states + token_type_embeds

'''<1> GPT2Model第一次迭代时输入GPT2Model的forward()函数中的past_key_values参数为None，此时past_length为0，hidden_states张量形状为(batch_size, seq_len, n_embd)，config的GPT2Config()类中的n_emb默认为768'''

'''<2> 若为GPT2Mode第二次及之后的迭代，此时past_length为上一次迭代时记录保存下来的past_key_values中张量的seq_len维度，而input_shape[-1] + past_length则等于seq_len + 1，因为在第二次及之后的迭代中，输入的文本编码(input_ids)的seq_len维度本身为1，即第二次及之后的迭代中每次只输入一个字的文本编码，此时hidden_states张量形状为(batch_size, 1, n_embd)，config的GPT2Config()类中的n_emb默认为768'''

hidden_states = self.drop(hidden_states)
output_shape = input_shape + (hidden_states.size(-1),)

# config对应的GPT2Config()类中的use_cache默认为True.
presents = () if use_cache else None
all_self_attentions = () if output_attentions else None
all_cross_attentions = () if output_attentions and self.config.add_cross_attention else None
all_hidden_states = () if output_hidden_states else None

for i, (block, layer_past) in enumerate(zip(self.h, past_key_values)):
    '''此处past_key_values元组中一共有12个元素(layer_past)，分别对应GPT-2模型中的12层Transformer_Block，每个layer_past都为模型上一次迭代中每个Transformer_Block保留下来的present张量，而每个present张量保存着Transformer_Block中Attention模块将本次迭代的key张量与上一次迭代中的past_key张量(layer_past[0])合并，将本次迭代的value张量与上一次迭代中的past_value张量(layer_past[1])合并所得的新的key张量与value张量，之后保存着本次迭代中12层Transformer_Block每一层中返回的present张量的presents元组，便会被作为下一次迭代中的past_key_values元组输入下一次迭代的GPT-2模型中。新的key张量与value张量详细解析如下'''

    '''第一次迭代时，query, key, value张量的seq_len维度处的维数就为seq_len而不是1，第二次之后seq_len维度的维数大小皆为1'''

    '''<1> 本次迭代中新的key张量