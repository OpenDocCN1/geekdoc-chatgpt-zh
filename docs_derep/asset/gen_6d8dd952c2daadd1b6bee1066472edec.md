[PRE0]

下面这段实现的具体含义可以拆成几个要点来看。

- `__init__()` 用来给 `TransformerEncoder` 设定基本配置，包含嵌入维度、隐藏层维度和多头数。
- 在初始化阶段会实例化一个多头注意力模块 `attention`、一个前馈网络模块 `feed_forward`，以及两组层归一化模块 `normalization_1`、`normalization_2`。
- `call()` 负责编码器的前向计算流程，先做多头注意力，再做层归一化，最后接入前馈网络。
- 当外部传入掩码 `mask` 时，多头注意力会带上该掩码，从而屏蔽掉填充位置的影响。
- 整体上这一层就是 Transformer 编码器的实现，作用是对输入序列做特征抽取和表示学习。

(3) 另起一个名为 `TransformerDecoder` 的自定义 Keras 层，用来实现 Transformer 的解码器部分。该解码器内部包含两组多头注意力、一个前馈网络，并配合层归一化来融合各路输出，完成目标序列的生成。推理时它会结合当前输入序列与编码器传来的上下文，在自回归模式下逐位置生成输出，并且通过因果遮罩保证当前位置只能看到之前的历史。这一部分为解码阶段提供了核心的注意力交互与特征变换能力。

[PRE1]

(4) 在此基础上搭建完整的 Transformer 模型，先有编码器 `encoder`、解码器 `decoder`，最终组合成整体模型 `my_transformer`。

[PRE2]

decoder = keras.Model([decoder_inputs, encoded_seq_inputs], decoder_outputs)

decoder_outputs = decoder([decoder_inputs, encoder_outputs])

# 创建整体 Transformer 模型

my_transformer = keras.Model(

    [encoder_inputs, decoder_inputs], decoder_outputs, name="my_transformer")

# 打印模型概要

print(my_transformer.summary())

# 绘制模型结构图

from tensorflow.keras.utils import plot_model

plot_model(my_transformer, show_shapes=True)

# 安装 TensorFlow Addons 库

!pip install -U tensorflow-addons

# 导入优化器

import tensorflow_addons as tfa

整段代码的执行逻辑梳理如下。

- 先确定模型的主要超参数，即嵌入维度 embed_dim、潜在隐藏维度 latent_dim 以及注意力头数 num_heads。
- 随后创建输入接口。编码器侧的 `encoder_inputs` 负责接收源序列，解码器侧的 `decoder_inputs` 负责接收目标序列。为保留顺序信息，会通过自定义的位置嵌入层 `PositionalEmbedding` 对序列做位置增强。
- 接着实例化 `TransformerEncoder` 并封装成 `encoder` 模型，它把原始源序列映射为更抽象的高层表征，捕捉语义关联。
- 再实例化 `TransformerDecoder` 并封装成 `decoder` 模型，它在编码器输出的基础上，结合自注意力机制对目标序列进行条件生成，得到每个位置的概率分布。
- 最后把编码器与解码器串联成 `my_transformer`。该整体模型同时接受源序列和目标序列作为输入，输出目标序列的分布预测。借助模型概要和结构图，可以直观看到各子模块的连接方式，为机器翻译等序列生成任务提供一个可用的深度学习骨架。

执行上述代码后，输出结果如下。