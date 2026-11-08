#### 6.3.2 模型结构拆解

在准备数据阶段，训练集和验证集的 DataLoader 都开启打乱，测试集则保持原有顺序不打乱。三个加载器统一使用多进程读取、开启 pin_memory，并不丢弃尾数样本，返回的正是训练、验证、测试三套迭代器。

##### 1. 图像编码器设计

SCAN 里图像侧的工作很直接：基于预提取的区域特征加一层线性投影，把 2048 维映射到统一的嵌入空间。输入形状是 batch_size x 36 x 2048，PyTorch 的 Linear 作用在最后维，所以不需要额外 reshape。投影后立刻做 L2 长度归一化，归一化维度固定为 -1。

class ImageRepExtractor(nn.Module):
    def __init__(self, img_feat_dim, embed_size):
        super(ImageRepExtractor, self).__init__()
        self.fc = nn.Linear(img_feat_dim, embed_size)
    def forward(self, x):
        out = self.fc(x)
        out = nn.functional.normalize(out, dim=-1)
        return out

##### 2. 文本编码器设计

文本侧采用双向 GRU。先用 Embedding 把词 id 转成词向量，再通过 pack_padded_sequence 去掉填充部分输入 RNN，处理完后 pad 回来。每个词的最终表示取正向和反向隐藏态的均值，并在末尾做长度归一化。权重初始化使用 [-0.1,0.1] 均匀分布。

class TextRepExtractor(nn.Module):
    def __init__(self, vocab_size, word_dim, embed_size, num_layers):
        super(TextRepExtractor, self).__init__()
        self.embed_size = embed_size
        self.embed = nn.Embedding(vocab_size, word_dim)
        self.rnn = nn.GRU(word_dim, embed_size, num_layers,
                          batch_first=True, bidirectional=True)
        self.init_weights()
    def init_weights(self):
        self.embed.weight.data.uniform_(-0.1, 0.1)
    def forward(self, x, lengths):
        x = self.embed(x)
        packed = pack_padded_sequence(x, lengths, batch_first=True)
        out, _ = self.rnn(packed)
        padded = pad_packed_sequence(out, batch_first=True)
        cap_emb, _ = padded
        cap_emb = (cap_emb[:,:,:cap_emb.size(2)//2] + cap_emb[:,:,cap_emb.size(2)//2:])/2
        out = nn.functional.normalize(cap_emb, dim=-1)
        return out

##### 3. SCAN 整体模型

图像编码器和文本编码器组合起来就是 SCAN。为了让 RNN 高效，正式前向时先按 caption 长度降序排列，图像与 caption 同步重排。编码完成后，如果处于推理阶段，则借助排序索引把结果恢复到原始输入顺序。

class SCAN(nn.Module):
    def __init__(self, vocab_size, word_dim, embed_size, num_layers, img_feat_dim):
        super(SCAN, self).__init__()
        self.image_encoder = ImageRepExtractor(img_feat_dim, embed_size)
        self.text_encoder = TextRepExtractor(vocab_size, word_dim,
                                            embed_size, num_layers)
    def forward(self, images, captions, cap_lens):
        sorted_cap_lens, sorted_cap_indices = torch.sort(cap_lens, 0, True)
        images = images[sorted_cap_indices]
        captions = captions[sorted_cap_indices]
        cap_lens = sorted_cap_lens
        image_code = self.image_encoder(images)
        text_code = self.text_encoder(captions, cap_lens)
        if not self.training:
            _, recover_indices = torch.sort(sorted_cap_indices)
            image_code = image_code[recover_indices]
            text_code = text_code[recover_indices]
        return image_code, text_code

#### 6.3.3 损失与匹配机制

SCAN 延续了 VSE++ 的在线困难样本挖掘 Triplet 损失，区别只在于图文相似度的计算方式换成了基于跨模态注意力的对齐得分。

##### 1. 通用注意力实现

注意力接收查询 Q、键值 K V，三步完成：先用 Q 与 K 做相关性打分，再对分数做归一化，最后用归一化后的权重加权求和 V 得到输出。跨模态场景下 Q 来自一个模态，K V 来自另一个模态。打分函数支持 plain、softmax、l2norm、clipped、clipped_l2norm 等多种形式，随后统一用带 smooth 系数的 Softmax 做归一化。

def func_attention(query, key_value, smooth, func_attn_score='plain'):
    batch_size, n_query = query.size(0), query.size(1)
    n_kv = key_value.size(1)
    queryT = torch.transpose(query, 1, 2)
    attn = torch.bmm(key_value, queryT)
    if func_attn_score == "plain":
        pass
    elif func_attn_score == "softmax":
        attn = nn.Softmax(dim=2)(attn)
    elif func_attn_score == "l2norm":
        attn = nn.functional.normalize(attn, dim=2)
    elif func_attn_score == "clipped":
        attn = nn.LeakyReLU(0.1)(attn)
    elif func_attn_score == "clipped_l2norm":
        attn = nn.LeakyReLU(0.1)(attn)
        attn = nn.functional.normalize(attn, dim=2)
    else:
        raise ValueError("unknown function for attention score:", func_attn_score)
    attn = torch.transpose(attn, 1, 2).contiguous()
    attn = attn.view(batch_size*n_query, n_kv)
    attn = nn.Softmax(dim=1)(attn*smooth)
    attn = attn.view(batch_size, n_query, n_kv)
    attnT = torch.transpose(attn, 1, 2).contiguous()
    key_valueT = torch.transpose(key_value, 1, 2)
    output = torch.bmm(key_valueT, attnT)
    output = torch.transpose(output, 1, 2)
    return output, attnT

##### 2. 跨模态对齐计算

SCAN 实验了两种查询方向：图像到文本 i2t 和文本到图像 t2i，前者以图像区域为查询、文本词为键值，后者反之。对齐后会计算对齐前后局部表示的余弦相似度，再通过累积函数汇总。累积函数可选均值或带超参 λ_lse 的 LogSumExp，即 log sum exp / λ_lse，λ 越大越接近 max，越小越平滑。

def xattn_score(images, captions, cap_lens, config):
    similarities = []
    n_image = images.size(0)
    n_caption = captions.size(0)
    for i in range(n_caption):
        n_word = cap_lens[i]
        cap_i = captions[i, :n_word, :].unsqueeze(0).contiguous()
        cap_i_expand = cap_i.repeat(n_image, 1, 1)
        if config.cross_attn == 'i2t':
            align_feature, _ = func_attention(images, cap_i_expand,
                                             config.lambda_softmax,
...

##### 3. 困难样本挖掘的 triplet 损失函数

与 VSE++ 的差别仅在于相似度由交叉注意力产生，其余的在线硬负样本挖掘流程保持一致。