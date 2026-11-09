# Transformer 编码驱动扩散生成

把所有子元素按权重叠加成一个新向量是自注意力最核心的操作。下面先拆解Transformer的编码端，因为在扩散任务里我们只关心从图像里抽取特征来学习分数函数或逆向转移核的参数。若要让Transformer处理图像，得先用patch切分把二维空间转成token序列，再补上位置编码。对每个token，先用可学习的线性投影得到它的Query、Key、Value三组表示Qi、Ki、Vi，接着计算它与其他token的相似度分数 <Qi, Kj>/√dk，dk 为Key的维度。对分数做softmax归一化得到注意力权重aij，最后的输出 zi = Σj aij vj 再送入前馈网络继续加工。Query/Key/Value的命名来自信息检索：比如用户在电商搜索‘深度学习代码参考书’，输入的是Query，系统匹配的关键词‘深度学习、代码、参考书’是Key，按相关度返回的商品信息是Value。于是<Qi,Kj>可理解为两个向量的关联强度，aij 就是 i 对 j 的关注程度。为避免训练退化，自注意力引入了残差连接。一个token可以拥有多组QKV，对每组独立计算后再拼接，得到Multi-Head Attention，如图2-9所示[293]。把多个Transformer块堆叠就能形成完整网络。在扩散模型里，可用Transformer对每步加噪图像编码，用编码结果预测下一步转移核的均值和方差，从而替代传统的U-Net。

[PRE0]

[PRE1]

图2-9 Self-Attention（左）和 Multi-Head Attention（右）

# 用 Transformer 替换 U-Net 的扩散范式

Peebles等人在论文 Scalable Diffusion Models with Transformers 中提出DiT，用Transformer完全取代U-Net。实测显示该方案吞吐更高、Gflops更优，且在条件生成上质量提升。该DiT架构如图 2-10，核心是在Latent Diffusion Transformer基础上设计三种条件注入方式，把每步的temb、label等引导信息送入网络：（1）自适应层归一化。把标准的Layer Normalization换成Adaptive Layer Normalization，用条件信息动态生成缩放和偏移系数；（2）交叉注意力。让条件信息直接与中间特征在注意力层里交互；（3）上下文拼接。把条件信息作为额外token拼到输入序列前端。实验中AdaLN在效果和效率上最突出。ImageNet上的生成结果也验证了Transformer式扩散架构的优势。

+   * （1）自适应的层标准化。将 Transformer 模块中常用的层标准化换成自适应的层标准化，用引导信息自适应生成相应的缩放和漂移参数；
* （2）交叉注意力。将引导信息直接和输入的中间特征进行混合；
* （3）上下文条件。将引导信息作为额外的输入拼接在输入端。

[PRE2]

DiT还做了消融验证，如图 2-11 所示。增大Transformer的深度或宽度，或提升token数量即减小patch大小，都能显著改善生成质量。

# 第2章 扩散模型基础

[PRE3]

# 图2-11 DIT架构分析实验结果

# U-Net 实现代码

U-Net代码如下:

[PRE4]

# dim_mults: 下采样时每一步通道数是原始通道数的倍数

# channels: 原始通道数

# self_condition: 是否使用条件扩散

# resnet_block_groups: 使用组归一化时的组数

# learned_variance: 是否学习逆向扩散过程的方差

# learned_sinusoidal_cond: 时间嵌入是否使用学习的嵌入方式

# random_fourier_features: 是否使用傅里叶特征

# learned_sinusoidal_dim: 学习的时间嵌入维数

super().__init__()

#确定维数

self.channels = channels

self.self_condition = self_condition

input_channels = channels * (2 if self_condition else 1)

init_dim = default(init_dim, dim)

#进行第一次卷积将输入数据的3个通道转换为init_dim个通道

self.init_conv = nn.Conv2d(input_channels, init_dim, 7, padding=3)

#建立下采样和上采样时数据的通道数

dims = [init_dim, *map(lambda m: dim * m, dim_mults)]

#获得下采样的每一步输入维度和输出维度，即表中正序排列的每一个二元组。获得上采样的每一步输入维度和输出维度，只需将列表进行逆序排列

in_out = list(zip(dims[:-1], dims[1:]))

#初始化非线性映射模块，利用了时间嵌入的残差连接网络

block_klass = partial(ResnetBlock, groups = resnet_block_groups)

#时间嵌入

time_dim = dim * 4

self.random_or_learned_sinusoidal_cond = learned_sinusoidal_cond or 

random_fourier_features

#使用预先学习的嵌入方式

if self.random_or_learned_sinusoidal_cond:

sinu_pos_emb = RandomOrLearnedSinusoidalPosEmb(

learned_sinusoidal_dim,random_fourier_features)

fourier_dim = learned_sinusoidal_dim + 1

else:

#使用内置的时间嵌入方式

sinu_pos_emb = SinusoidalPosEmb(dim)

fourier_dim = dim

self.time_mlp = nn.Sequential(

sinu_pos_emb,

nn.Linear(fourier_dim, time_dim),

nn.GELU(),

nn.Linear(time_dim, time_dim))

#映射层。in_out记录了下采样的每一步的输入维度和输出维度

self.downs = nn.ModuleList([[]])

self.ups = nn.ModuleList([])

num_resolutions = len(in_out)

#进行下采样

[PRE5]

for ind, (dim_in, dim_out) in enumerate(in_out):

[PRE6]

is_last = ind >= (num_resolutions - 1)

[PRE7]

self.downs.append(nn.ModuleList({

[PRE8]

    #非线性映射，不改变数据维度

[PRE9]

    block_klass(dim_in, dim_in, time_emb_dim = time_dim),

[PRE10]

    block_klass(dim_in, dim_in, time_emb_dim = time_dim),

[PRE11]

    #有正则化和线性注意力的残差网络

[PRE12]

    Residual(PreNorm(dim_in, LinearAttention(dim_in))),

[PRE13]

    #在此映射后数据长度和宽度都除以2，维度增加为2倍

[PRE14]

    Downsample(dim_in, dim_out) if not is_last else

[PRE15]

        nn.Conv2d(dim_in, dim_out, 3, padding = 1)

[PRE16]

    ])

[PRE17]

)

[PRE18]

#对低维度特征进一步卷积来提取特征，但保持维度不变

[PRE19]

mid_dim = dims[-1]

[PRE20]

self.mid_block1 = block_klass(mid_dim, mid_dim, time_emb_dim =

[PRE21]

    time_dim)

[PRE22]

#有正则化和注意力的残差网络

[PRE23]

self.mid_attn = Residual(PreNorm(mid_dim, Attention(mid_dim)))

[PRE24]

self.mid_block2 = block_klass(mid_dim, mid_dim, time_emb_dim =

[PRE25]

    time_dim)

[PRE26]

#进行上采样

[PRE27]

for ind, (dim_in, dim_out) in enumerate(reversed(in_out)):

[PRE28]

    is_last = ind == (len(in_out) - 1)

[PRE29]

    self.ups.append(nn.ModuleList([

[PRE30]

        block_klass(dim_out + dim_in, dim_out, time_emb_dim = time_dim),

[PRE31]

        block_klass(dim_out + dim_in, dim_out, time_emb_dim = time_dim),

[PRE32]

        Residual(PreNorm(dim_out, LinearAttention(dim_out))),

[PRE33]

        #上采样函数，将数据的长度和宽度乘以2，并将通道数除以2

[PRE34]

        Upsample(dim_out, dim_in) if not is_last else

[PRE35]

            nn.Conv2d(dim_out, dim_in, 3, padding = 1)

[PRE36]

    ])

[PRE37]

)

[PRE38]

#如果学习逆向方差，则需要额外1倍的通道来进行学习

[PRE39]

default_out_dim = channels * (1 if not learned_variance else 2)

[PRE40]

self.out_dim = default(out_dim, default_out_dim)

[PRE41]

#最后的输出层

[PRE42]

self.final_res_block = block_klass(dim * 2, dim, time_emb_dim =

[PRE43]

    time_dim)

[PRE44]

self.final_conv = nn.Conv2d(dim, self.out_dim, 1)

[PRE45]

def forward(self, x, time, x_self_cond = None):

[PRE46]

    #使用条件扩散

[PRE47]

    if self.self_condition:

[PRE48]

        x_self_cond = default(x_self_cond, lambda: torch.zeros_like(x))

[PRE49]

        x = torch.cat((x_self_cond, x), dim = 1)

[PRE50]

    #初始化数据、数据复制和时间嵌入

[PRE51]

    x = self.init_conv(x)

[PRE52]

r = x.clone()

t = self.time_mlp(time)

h = []

#下采样时记录映射结果，之后需要拼接到上采样对应层的特征

for block1, block2, attn, downsample in self.downs:

    x = block1(x, t)

    h.append(x)

    x = block2(x, t)

    x = attn(x)

    h.append(x)

    x = downsample(x)

#对高级语义特征进一步卷积

x = self.mid_block1(x, t)

x = self.mid_attn(x)

x = self.mid_block2(x, t)

#上采样

for block1, block2, attn, upsample in self.ups:

    x = torch.cat((x, h.pop()), dim=1)

    x = block1(x, t)

    x = torch.cat((x, h.pop()), dim=1)

    x = block2(x, t)

    x = attn(x)

    x = upsample(x)

#将初始特征拼接到经过下采样、上采样学习得到的特征，从而充分利用信息

x = torch.cat((x, r), dim=1)

#输出前最后的卷积

x = self.final_res_block(x, t)

return self.final_conv(x)

[PRE53]

# 第2章 扩散模型基础

[PRE54]