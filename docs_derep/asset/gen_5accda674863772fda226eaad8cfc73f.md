# MoE 视觉编码器核心组件实现笔记

下面把专家混合、编码块、Patch 切分和位置编码这几块的关键实现梳理清楚，代码保持可直接对照。

专家混合模块的初始化逻辑很直接，先调用基类构造，再校验专家列表非空，随后把专家注册为 ModuleList，并把门控和配置参数保存下来。
    def __init__(self, experts: List[nn.Module],

        super().__init__()

        assert len(experts) > 0

        self.experts = nn.ModuleList(experts)

        self.gate = gate

        self.args = moe_args

[PRE0]python

from 第十章 import baseMOE

class EncoderBlock(torch.nn.Module):

    def __init__(self, d_model, num_heads):

        super(EncoderBlock, self).__init__()

        self.d_model = d_model

        self.num_heads = num_heads

        self.attention_norm = torch.nn.RMSNorm(d_model)

        self.self_attention = attention_moudle.MultiHeadAttention(d_model, num_heads)

        #self.ffn = feedforward_layer.Swiglu(d_model)

        #读者可以替换FFNN部分完成

        self.ffn = baseMOE.SparseMoE(d_model,4,2)

    def forward(self, embedding):

        residual = embedding

        embedding = self.attention_norm(embedding)

        embedding = self.self_attention(embedding)

        embedding = self.ffn(embedding)

        return embedding + residual

编码块内部采用残差结构，先用 RMSNorm 稳定注意力输入，再做多头自注意力，最后把 FFN 换成稀疏 MoE，这里实例化为 4 个专家激活 2 个。残差在最后加回，保持梯度通畅。

[PRE1]

[49,16] ↔ 28×28 = (28×28 / 4×4) × (4×4) = 49×16

这一步是在说明特征重排的可逆性，28×28 的特征图经过 4×4 的块划分后等价于 49 个 token 每个 16 维的表示。

[PRE2]python

class PatchEmbed(torch.nn.Module):

    def __init__(self, img_size=224, patch_size=16, in_c=3, embed_dim=768):

        super().__init__()

        """

        此函数用于初始化相关参数

        :param img_size: 输入图像的大小

        :param patch_size: 一个patch的大小

        :param in_c: 输入图像的通道数

        :param embed_dim: 输出的每个token的维度

        :param norm_layer: 指定归一化方式，默认为None

        """

        patch_size = (patch_size, patch_size)  # 16 -> (16, 16)

        self.img_size = img_size = (img_size, img_size)  # 224 -> (224, 224)

        self.patch_size = patch_size

        self.grid_size = (img_size[0] // patch_size[0], img_size[1] // img_size[1])  # 计算原始图像被划分为(14, 14)个小块

        self.num_patches = self.grid_size[0] * self.grid_size[1]  # 计算patch的个数为14×14=196个

        # 定义卷积层

        self.proj = torch.nn.Conv2d(in_channels=in_c, out_channels=embed_dim,

                                    kernel_size=patch_size, stride=patch_size)

        # 定义归一化方式

        self.norm = torch.nn.LayerNorm(embed_dim)

    def forward(self, image):

        """

        此函数用于前向传播

        :param x: 原始图像

        :return: 处理后的图像

        """

        B, C, H, W = image.shape

        # 检查图像高、宽和预先设定是否一致，不一致则报错

        assert H == self.img_size[0] and W == self.img_size[1], \

            f"Input image size ({H}*{W}) doesn't match model ({self.img_size[0]}*{self.img_size[1]})."

        # 对图像依次进行卷积、展平和调换处理：[B, C, H, W] -> [B, C, HW] -> [B, HW, C]

        x = self.proj(image).flatten(2).transpose(1, 2)

        # 归一化处理

        x = self.norm(x)

        return x

PatchEmbed 用卷积完成图像到 token 的映射，默认 224 入 16 切块得到 196 个 patch。初始化时统一成元组算网格数，卷积核与步长同为 patch_size，输出再做 LayerNorm。

位置编码部分很简洁，预留一个可学习类 token，随后把位置参数加到 patch 序列上。
[PRE3]python

self.num_tokens = 1

self.pos_embed = torch.nn.Parameter(torch.zeros(1, num_patches + self.num_tokens, embed_dim))

x = x + self.pos_embed