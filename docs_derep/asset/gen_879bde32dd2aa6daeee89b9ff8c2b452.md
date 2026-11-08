在复现 Transformer 解码器时，最容易踩坑的往往是三个向量的归属和掩码的形状。下面把整条流程拆开讲清楚，先定角色，再看如何造掩码，最后落到注意力计算和层级结构。

[PRE0]

query = 解码器词输入向量

key = 编码器词输出向量

value = 编码器词输出向量

在编码器-解码器交互里，解码器负责生成，编码器负责提供上下文。query 来自解码器自身的输入序列，key 与 value 都取自编码器最后的输出表示，这样解码器才能基于已生成的词去检索源端信息。三个角色一旦对齐，后续的注意力计算才有意义。

[PRE1]

#创建解码器中的交互注意力掩码

def creat_self_mask(from_tensor, to_tensor):

    """

    这里需要注意，from_tensor 是输入的文本序列，即 input_word_ids ，

    应该是2D的，即[1,2,3,4,5,6,0,0,0,0]

    to_tensor 是输入的 input_word_ids，应该是2D的，即

    [1,2,3,4,5,6,0,0,0,0]

    而经过本函数的扩充维度操作后，最终是输出两个3D的相乘后的结果

    注意：后面如果需要4D的，则使用expand添加一个维度即可

    """

    batch_size, from_seq_length = from_tensor.shape

    to_mask = torch.not_equal(from_tensor, 0).int()

    to_mask = elt.Rearrange("b l -> b l l")(to_mask)  # 这里扩充了数据维度

    broadcast_ones = torch.ones_like(to_tensor)

    broadcast_ones = torch.unsqueeze(broadcast_ones, dim=-1)

    mask = broadcast_ones * to_mask

    mask.to("cuda")

    return mask

这段代码的核心是把填充位置剔除。先用非零判断得到一个有效位置的布尔图，再通过重排把二维图扩成三维，方便后续与广播的张量相乘。得到的结果可以理解为只保留真实词的注意力通道，填充位被置零。

[PRE2]

def create_look_ahead_mask(from_tensor, to_tensor):

    corss_mask = creat_self_mask(from_tensor, to_tensor)

    look_ahead_mask = torch.tril(torch.ones(to_tensor.shape[1],

from_tensor.shape[1]))

    look_ahead_mask = look_ahead_mask.to("cuda")

    corss_mask = look_ahead_mask * corss_mask

    return corss_mask

自回归生成要求当前词只能看到过去的词。先生成一个下三角矩阵实现因果遮挡，再与前面得到的填充掩码做逐元素相乘，两层约束叠加后就得到了解码器自注意力真正能使用的掩码。

[PRE3]

tf.Tensor(

[[[[1. 0. 0. 0.]]]

 [[[1. 1. 0. 0.]]]

 [[[1. 1. 1. 0.]]]

 [[[1. 1. 1. 1.]]]], shape=(4, 1, 1, 4), dtype=float32)

+

tf.Tensor(

[[0. 1. 1. 1.]

 [0. 0. 1. 1.]

 [0. 0. 0. 1.]

 [0. 0. 0. 0.]], shape=(4, 4), dtype=float32)

=

tf.Tensor(

[[[[1. 1. 1. 1.]

   [1. 0. 1. 1.]

   [1. 0. 0. 1.]

   [1. 0. 0. 0.]]]

 [[[1. 1. 1. 1.]

   [1. 1. 1. 1.]

   [1. 1. 0. 1.]

   [1. 1. 0. 0.]]]

 [[[1. 1. 1. 1.]

   [1. 1. 1. 1.]

   [1. 1. 1. 1.]

   [1. 1. 1. 0.]]]

 [[[1. 1. 1. 1.]

   [1. 1. 1. 1.]

   [1. 1. 1. 1.]

   [1. 1. 1. 1.]]]],

shape=(4, 1, 4, 4), dtype=float32)

上面是一次典型的掩码叠加演示。左侧是填充掩码的广播形式，右侧是因果下三角。相乘后得到最终的注意力遮挡张量，既能屏蔽填充，又能保证自回归顺序。

[PRE4]

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

        # attn_mask所有为True的部分（即被掩码操作的部分），scores填充为负无穷，也就是这个位置的值对于Softmax没有影响

        attn = nn.Softmax(dim=-1)(scores)

        # attn: [batch_size, n_heads, len_q, len_k]

        # 对每一行进行Softmax

        context = torch.matmul(attn, V)

        # [batch_size, n_heads, len_q, d_v]

        return context, attn

缩放点积的思路很直接：先算 QK^T 再除以根号 d_k 抑制梯度震荡，用掩码把不允许的位置替换成极大负数使其在 Softmax 后权重趋近于零，最后用注意力权重加权 V 得到上下文表示。

[PRE5]

class MultiHeadAttention(tf.keras.layers.Layer):

    def __init__(self):

        super(MultiHeadAttention, self).__init__()

    def build(self, input_shape):

        self.dense_query = tf.keras.layers.Dense(units=embedding_size, activation=tf.nn.relu)

        self.dense_key = tf.keras.layers.Dense(units=embedding_size, activation=tf.nn.relu)

        self.dense_value = tf.keras.layers.Dense(units=embedding_size, activation=tf.nn.relu)

        self.dense = tf.keras.layers.Dense(units=embedding_size, activation=tf.nn.relu)

        super(MultiHeadAttention, self).build(input_shape)  # 一定要在最后调用它

    def call(self, inputs):

        query, key, value, mask = inputs

        shape = tf.shape(query)

        query_dense = self.dense_query(query)

        key_dense = self.dense_query(key)

        value_dense = self.dense_query(value)

        query_dense = splite_tensor(query_dense)

        key_dense = splite_tensor(key_dense)

        value_dense = splite_tensor(value_dense)

        attention = tf.matmul(query_dense, key_dense, transpose_b=True) / tf.math.sqrt(tf.cast(embedding_size, tf.float32))

        attention += (mask * -1e9)

        attention = tf.nn.softmax(attention)

        attention = tf.matmul(attention, value_dense)

        attention = tf.transpose(attention, [0, 2, 1, 3])

        attention = tf.reshape(attention, [shape[0], -1, embedding_size])

        attention = self.dense(attention)

        return attention

TensorFlow 版的多头实现先把 Q K V 投到子空间并切分头，计算缩放后的注意力分数后叠加掩码惩罚，再归一化、聚合，最后拼回原维度并做一次输出投影，完成一个完整的多头轮次。

[PRE6]

class FeedForWard(tf.keras.layers.Layer):

    def __init__(self):

        super(FeedForWard, self).__init__()

    def build(self, input_shape):

        self.conv_1 = tf.keras.layers.Conv1D(embedding_size*4,1,activation=tf.nn.relu)

        self.conv_2 = tf.keras.layers.Conv1D(embedding_size,1,activation=tf.nn.relu)

        super(FeedForWard, self).build(input_shape)  # 一定要在最后调用它

    def call(self, inputs):

        output = self.conv_1(inputs)

        output = self.conv_2(output)

        return output

位置-wise 前馈网络用两个 1x1 卷积实现扩张再压缩，中间以 ReLU 激活，等价于传统位置全连接，负责在注意力之后做非线性变换。

[PRE7]

class DecoderLayer(nn.Module):

    def __init__(self):

        super(DecoderLayer, self).__init__()

        self.dec_self_attn = MultiHeadAttention()

        self.dec_enc_attn = MultiHeadAttention()

        self.pos_ffn = PoswiseFeedForwardNet()

    def forward(self, dec_inputs, enc_outputs, dec_self_attn_mask, dec_enc_attn_mask):

        '''

        dec_inputs: [batch_size, tgt_len, d_model]

        enc_outputs: [batch_size, src_len, d_model]

        dec_self_attn_mask: [batch_size, tgt_len, tgt_len]

一个标准解码器层由两段注意力组成：先用自注意力处理目标序列自身，再用编码器-解码器注意力去对齐源序列，最后接前馈网络。掩码分别对应自回归和填充约束，层与层之间逐层堆叠即形成完整的解码器。