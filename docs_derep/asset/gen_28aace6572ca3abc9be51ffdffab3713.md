[PRE0]

### 3. 字库索引到Token序列

仅靠标注好的拼音和汉字还不能直接投喂给模型，训练前必须先把它变成模型能识别的数字序列，转换示例代码如下：

[PRE1]

实现里新建了两份独立的列表，分别用来收纳拼音侧和汉字侧的Token，借助字库里的序号映射最终得到重排后的Token序列。

### 12.2.2 经典编解码翻译模型

这里采用的就是业界常用的编码器-解码器架构，完整实现展示如下。

> 【程序12-5】

[PRE2]

[PRE3]

[PRE4]

中的值

[PRE5]

掩码操作同时存在于编码器和解码器，所以函数里 seq_len 的取值并不是一成不变的。Encoder 端被调用时，seq_len 就是源序列长度 src_len；Decoder 端则更灵活，可能取 src_len，也可能取目标序列长度 tgt_len，这是因为解码器内部执行了两次不同位置的掩码。其中 src_len 对应编码器-解码器交叉掩码，tgt_len 对应解码器自注意力掩码。

[PRE6]

[PRE7]python

look_ahead_mask = look_ahead_mask.to("cuda")

cross_mask = look_ahead_mask * cross_mask

return cross_mask

[PRE8]

python

class ScaledDotProductAttention(nn.Module):

    def __init__(self):

        super(ScaledDotProductAttention, self).__init__()

    def forward(self, Q, K, V, attn_mask):

        '''

        Q: [batch_size, n_heads, len_q, d_k]

        K: [batch_size, n_heads, len_k, d_k]

        V: [batch_size, n_heads, len_v(=len_k), d_v]

        attn_mask: [batch_size, n_heads, seq_len, seq_len]

        '''

        scores = torch.matmul(Q, K.transpose(-1, -2)) / np.sqrt(d_k)

        # scores : [batch_size, n_heads, len_q, len_k]

        scores.masked_fill_(attn_mask == 0, -1e9)

        # attn_mask所有为True的部分（有Padding的部分），scores填充为负无穷，也就是这个位置的值对于Softmax没有影响

        attn = nn.Softmax(dim=-1)(scores)

        # attn: [batch_size, n_heads, len_q, len_k]

        # 对每一行进行Softmax计算

        context = torch.matmul(attn, V)

        # [batch_size, n_heads, len_q, d_v]

        return context, attn

[PRE9]

python

class MultiHeadAttention(nn.Module):

    def __init__(self):

        super(MultiHeadAttention, self).__init__()

        self.W_Q = nn.Linear(d_model, d_k * n_heads, bias=False)

        self.W_K = nn.Linear(d_model, d_k * n_heads, bias=False)

        self.W_V = nn.Linear(d_model, d_v * n_heads, bias=False)

        # 3个矩阵，分别对输入进行3次线性变化

        self.fc = nn.Linear(n_heads * d_v, d_model, bias=False)

        # 变换维度