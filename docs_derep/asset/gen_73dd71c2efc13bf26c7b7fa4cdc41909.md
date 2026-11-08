# 解码器掩码全拆解：Transformer如何禁止偷看未来

自回归生成是Transformer的核心，掩码就是让模型“失忆”的关键开关。下面用最直观的代码片段把前瞻掩码、填充掩码和交叉掩码的构造逻辑串起来，顺带看一眼注意力评分如何被掩码驯服。

先看TensorFlow里最经典的前瞻掩码写法，用带状矩阵把未来位置一次性屏蔽掉。
[PRE0]

def create_look_ahead_mask(size):

    mask = 1 - tf.linalg.band_part(tf.ones((size, size)), -1, 0)

    return mask

size为4时实际能得到什么形状，直接跑一下就有体感。
[PRE1]

mask = create_look_ahead_mask(4)

print(mask)

在解码阶段，Q、K、V的来源要拎清。
[PRE2]

query = 解码器词输入向量

key = 编码器词输出向量

value = 编码器词输出向量

下面这段是为了解码器自注意力准备的填充掩码，先把0填充的位置标记出来，再通过维度扩充让它能广播到注意力矩阵上。
[PRE3]

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

有了填充掩码，再叠加因果三角就能得到解码器里真正使用的交叉掩码。下面是把下三角和填充掩码做点乘的组合方式。
[PRE4]

def create_look_ahead_mask(from_tensor, to_tensor):

    corss_mask = creat_self_mask(from_tensor, to_tensor)

    look_ahead_mask = torch.tril(torch.ones(to_tensor.shape[1], from_tensor.shape[1]))

    look_ahead_mask = look_ahead_mask.to("cuda")

    corss_mask = look_ahead_mask * corss_mask

    return corss_mask

直观感受下两种掩码叠加后的数值效果，一块是填充，一块是前瞻，相乘后得到最终可用的注意力遮罩。
[PRE5]

tf.Tensor(

[[[1. 0. 0. 0.]]

 [[1. 1. 0. 0.]]

 [[1. 1. 1. 0.]]

 [[1. 1. 1. 1.]]], shape=(4, 1, 1, 4), dtype=float32)

+

tf.Tensor(

[[0. 1. 1. 1.]

 [0. 0. 1. 1.]

 [0. 0. 0. 1.]

 [0. 0. 0. 0.]], shape=(4, 4), dtype=float32)

=

tf.Tensor(

[[[1. 1. 1. 1.]

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

  [1. 1. 1. 1.]]],

shape=(4, 1, 4, 4), dtype=float32)

掩码准备好后，就进入注意力评分阶段。缩放点积注意力先算分数，再用掩码把非法位置打成负无穷，Softmax之后这些位置的权重基本归零。
[PRE6]python

class ScaledDotProductAttention(nn.Module):

    def __init__(self):

        super(ScaledDotProductAttention, self).__init__()

    def forward(self, Q, K, V, attn_mask):

        """

        Q: [batch_size, n_heads, len_q, d_k]

        K: [batch_size, n_heads, len_k, d_k]

        V: [batch_size, n_heads, len_v(=len_k), d_v]

        attn_mask: [batch_size, n_heads, seq_len, seq_len]

        """

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

最后是TensorFlow版的多头注意力骨架，Query、Key、Value分别用Dense投影，再统一做输出融合。
[PRE7]python

class MultiHeadAttention(tf.keras.layers.Layer):

    def __init__(self):

        super(MultiHeadAttention, self).__init__()

    def build(self, input_shape):

        self.dense_query = tf.keras.layers.Dense(units=embedding_size,

activation=tf.nn.relu)

        self.dense_key = tf.keras.layers.Dense(units=embedding_size,

activation=tf.nn.relu)

        self.dense_value = tf.keras.layers.Dense(units=embedding_size,

activation=tf.nn.relu)

        self.dense = tf.keras.layers.Dense(units=embedding_size,activation=tf.nn.relu)