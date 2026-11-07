LoRA压缩注意力模块怎么跑的

这个模块的核心思路是先把 Key/Value 做低秩压缩，再在压缩空间里算注意力，最后再把结果映射回来，配合 CoPE 做位置编码。

参数准备阶段
先建了几组可训练的矩阵：把输入降到 lora 秩的 W_dkv 和 W_duv，配合 RMSNorm 做键值归一化，还有一个按头展开的 kv_b_proj 用于后续投影。输出侧用 W_o 把多头结果拉回模型维度，并配上 dropout。查询-键的交互权重 uk_weight 和值重建权重 uv_weight 都是按头、按秩、按头维度的张量，负责把压缩表示转回标准查询空间。位置信息交给了 CoPE。

前向流程
输入进来后，查询先走一次降维-归一化-升维的路径，然后按头拆开得到 q_nope。键值这边会先判断是否已有历史缓存，有就拼接，没有就直接用当前步的压缩表示。压缩后的键值先过 W_duv，再做 RMSNorm，得到紧凑的中间表示，这个中间态也会作为 present 返回。

注意力计算
用 uk_weight 把压缩键值映射到查询维度，配合 q_nope 做 einsum 得到原始分数。分数除以根号头维做缩放，再叠加因果掩码和外部 attention_mask，随后加上 CoPE 的位置修正。经过 softmax 和 dropout 得到归一化权重。

值侧重建
同样用 uv_weight 把压缩键值映射回值空间，再用注意力权重加权求和得到多头输出。合并多头后通过 W_o 投影，并再次应用 dropout，最后返回注意力输出和当步的压缩键值。

辅助函数
_split_heads 把最后一维拆成 [num_heads, head_dim] 并转成 batch-head-seq-head 的布局，_merge_heads 做相反操作。_attn 封装了上面分数计算、掩码处理、CoPE 注入、softmax 与值重建的完整链路。

[PRE0]python

class Expert(torch.nn.Module):

    def __init__(self, n_embd):

        super().__init__()

        self.net = torch.nn.Sequential(

            torch.nn.Linear(n_embd, 4 * n_embd),