ViT 搭上 MoE 的 MNIST 实战拆解

最近把视觉 Transformer 和稀疏专家混合做了一个最小可运行的实验，用 MNIST 验证思路是否通顺。下面把整体结构、训练流程和一个可复用的 MoE 块拆开说明，代码全部保留方便直接对照。

先看单层结构。每个 ViTBlock 先做 RMSNorm，再做多头自注意力，最后交给稀疏 MoE 做前馈，输出加上残差。参数包括模型维度、头数，FFN 部分调用来自第五章的 SparseMoE，专家数 4，激活数 2。
[PRE0]python
from 第五章 import baseMOE

class ViTBlock(torch.nn.Module):

    def __init__(self, d_model, num_heads):

        super(ViTBlock, self).__init__()

        self.d_model = d_model

        self.num_heads = num_heads

        self.attention_norm = torch.nn.RMSNorm(d_model)

        self.self_attention = attention_moudle.MultiHeadAttention(d_model, num_heads)

        self.ffn = baseMOE.SparseMoE(d_model,4,2)

    def forward(self, embedding):

        residual = embedding

        embedding = self.attention_norm(embedding)

        embedding = self.self_attention(embedding)

        embedding = self.ffn(embedding)

        return embedding + residual
整体网络很紧凑。PatchEmbedding 把图像切块后加上可学习的位置编码，维度固定为 312，16 个位置。只放一层 ViTBlock，头数 6，最后 Flatten 再接一个线性头映射到 10 类。
[PRE1]python
class ViT(torch.nn.Module):

    def __init__(self,dim = 312):

        super(ViT, self).__init__()

        self.patch_embedding = PatchEmbed()

        self.position_embedding = torch.nn.Parameter(torch.rand(1, 16, dim))

        self.vit_layers = ViTBlock(d_model=312, num_heads=6)

        self.logits_layer = torch.nn.Linear(4992,10)

    def forward(self, x):

        embedding = self.patch_embedding(x) + self.position_embedding

        embedding = self.vit_layers(embedding)

        embedding = torch.nn.Flatten()(embedding)

        logits = self.logits_layer(embedding)

        return logits
训练部分采用标准的 PyTorch 循环。设备自动选择 GPU，batch 128，AdamW 学习率 1e-5，交叉熵损失。数据来自自定义 MNIST 封装，36 个 epoch，用 tqdm 显示每轮 loss。评估时关闭梯度，统计预测正确数并输出百分比。
[PRE2]python
from torch.utils.data import DataLoader

from get_dataset import MNIST

import vmoe_model

from tqdm import tqdm

import torch

DEVICE='cuda' if torch.cuda.is_available() else 'cpu' # 设备

BATCH_SIZE = 128

dataset=MNIST() # 数据集

model = vmoe_model.VIT().to(DEVICE)

optimizer =torch.optim.AdamW(model.parameters(),lr=1e-5)   # 优化器

loss_fn = torch.nn.CrossEntropyLoss()

dataloader=DataLoader(dataset,batch_size=BATCH_SIZE,shuffle=True)   # 数据加载器

for epoch in range(36):

    pbar = tqdm(dataloader, total=len(dataloader))

    for imgs, labels in pbar:

        imgs, labels = imgs.to(DEVICE), labels.to(DEVICE) #将数据和标签移动到指定的设备上

        # 前向传播

        outputs = model(imgs)

        loss = loss_fn(outputs, labels)

        # 反向传播和优化

        optimizer.zero_grad()      # 清空梯度

        loss.backward()            # 反向传播计算梯度

        optimizer.step()           # 更新模型参数

        # 更新进度条信息

        pbar.set_description(f"Epoch {epoch + 1}/{36}, Loss: {loss.item():.4f}")

model.eval()  # 设置模型为评估模式

val_dataset = MNIST(is_train=False)  # 验证数据集

val_dataloader = DataLoader(val_dataset, batch_size=BATCH_SIZE, shuffle=False)  # 验证数据加载器，通常不需要打乱

correct = 0  # 正确预测的计数

total = 0  # 总样本数

with torch.no_grad():  # 不需要计算梯度，节省内存和计算资源

    for imgs, labels in tqdm(val_dataloader, total=len(val_dataloader)):

        imgs, labels = imgs.to(DEVICE), labels.to(DEVICE) #将数据和标签移动到指定的设备上

        # 前向传播

        outputs = model(imgs)

        _, predicted = torch.max(outputs.data, 1)  # 得到预测结果

        # 计算准确率

        total += labels.size(0)  # 更新总样本数

        correct += (predicted == labels).sum().item()  # 更新正确预测的计数

##### 计算并打印准确率

accuracy = 100 * correct / total

print(f'Validation Accuracy of the model on the test images: {accuracy:.2f}%')
训练时进度条会显示类似
[PRE3]
Epoch 1/36
这样的信息，随着轮次推进 loss 下降。

如果想复用社区的 MoE 实现，先安装依赖即可。
[PRE4]
pip install st_moe_pytorch
下面是一个基于 st_moe_pytorch 的通用专家模块示例。包含自定义 SwiGLU 激活，以及 MoE 配置：16 个专家，top-2 路由，训练和评估阈值 0.2，容量因子 1.25/2，平衡损失和 router z 损失系数分别 1e-2 与 1e-3。整体先 RMSNorm，再 SparseMoEBlock，后接 SwiGLU 与线性投影。
[PRE5]python
import torch

from st_moe_pytorch import MoE

from st_moe_pytorch import SparseMoEBlock

class Swiglu(torch.nn.Module):

    def __init__(self, hidden_size=384, add_bias_linear=False):

        super(Swiglu, self).__init__()

        self.add_bias = add_bias_linear

        self.hidden_size = hidden_size

        self.dense_h_to_4h = torch.nn.Linear(

            hidden_size,

            hidden_size * 4,

            bias=self.add_bias

        )

        def swiglu(x):

            x = torch.chunk(x, 2, dim=-1)

            return torch.nn.functional.silu(x[0]) * x[1]

        self.activation_func = swiglu

        self.dense_4h_to_h = torch.nn.Linear(

            hidden_size * 2,

            hidden_size,

            bias=self.add_bias

        )

    def forward(self, hidden_states):

        intermediate_parallel = self.dense_h_to_4h(hidden_states)

        intermediate_parallel = self.activation_func(intermediate_parallel)

        output = self.dense_4h_to_h(intermediate_parallel)

        return output

class MOE(torch.nn.Module):

    def __init__(self, dim = 512):

        """

            from:https://github.com/lucidrains/st-moe-pytorch

        """

        super(MOE, self).__init__()

        self.moe = MoE(

            dim=dim,  # MoE模型的维度

            num_experts=16,  # 专家数量，增加此值可以在不增加计算量的情况下增加模型的参数

            gating_top_n=2,  # 默认选择前两个专家，但也可以选择更多（论文中测试了3个，但使用了较低的阈值）

            threshold_train=0.2,  # 训练时，决定一个token是否被路由到第二个及以后专家的阈值，对于两个专家路由，0.2是最优的，对于3个显然应该更低

            threshold_eval=0.2,  # 评估时的阈值，与train时的意义相同

            capacity_factor_train=1.25,  # 每个批次的专家有固定的容量，我们需要一些额外的容量以防路由不完全平衡

            capacity_factor_eval=2.,  # 评估时的容量因子，capacity_factor_*的值应设置为 >=1

            balance_loss_coef=1e-2,  # 辅助专家平衡损失的乘数

            router_z_loss_coef=1e-3,  # 路由z损失的权重

        )

        self.moe_block = SparseMoEBlock(

            self.moe,

            add_ff_before=True,

            add_ff_after=True

        )

        self.norm = torch.nn.RMSNorm(dim)

        self.moe_linear = torch.nn.Linear(dim, dim, bias=False)

        self.activity_layer = Swiglu(hidden_size = dim)

    def forward(self, x):

        x = self.norm(x)

        enc_out = self.moe_block(x)[0]

        enc_out = self.activity_layer(enc_out)#torch.nn.functional.gelu(enc_out)

        enc_out = self.moe_linear(enc_out)

        return enc_out
从这个最小范式可以看出，ViT 的注意力保留不变，前馈换成稀疏专家即可快速尝鲜 MoE 的收益，后续再扩展层数与专家规模即可。