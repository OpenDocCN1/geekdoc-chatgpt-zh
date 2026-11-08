[PRE0]

MLP 部分的实现可以结合注释来阅读。

在 GPT-2 的主干里，每个 Block 的运行都由自注意力模块和前馈模块共同完成。MLP 其实就是一个两层的全连接结构，它会把 Attention 层吐出来的 hidden_states 接进来做前馈神经网络的变换。MLP 计算完后的结果再送入残差连接 residual_connection，经过这一步一个 Block 的工作就算收尾，模型会继续进入下一个 Block。

## 14.2 Hugging Face 版 GPT-2 源码深度拆解

14.1 节已经把 GPT-2 的整体架构以及输入输出的规范讲过，但并没有涉及训练。现实情况是，和官方已训好的模型相比，普通用户几乎没有条件训练出一个同等水准的 GPT-2。所以我们把视角转向 Hugging Face 库里的源码，从实现层面去理解模型的结构。

### 14.2.1 GPT2LMHeadModel 与 GPT2Model 源码解读

Hugging Face 官方提供了一个用 GPT2LMHeadModel 调用 GPT-2 的示例，代码如下：

[PRE1]

从这段示例可以看出，context 对应每次迭代喂入模型的 input_ids；past_key_values 是 12 层 Block 计算后返回的 presents 元组，一共 12 组。每组 present 里保存着本次 key 与历史 past_key 合并后的新 key，以及本次 value 与历史 past_value 合并后的新 value。它的形状是 (2, batch_size, num_head, seq_len+1, head_features)。key、past_key、value、past_value、present 都是 Attention 计算过程中用到的张量。

past_key_values 是 GPT-2 推理中的关键加速机制，它让模型在文本生成时不必对已经算过的上下文重复计算，从而大幅提升生成效率。要注意的是，第一次迭代时没有上一轮的缓存，所以 past_key_values 会是 None。

目前大多数可用于文本生成的预训练模型都采用了类似的缓存设计，例如 Google 的 T5、Meta 的 BART。理解 GPT-2 里这套 past_key_values 的原理，对读懂 T5、BART 等模型的推理流程也有帮助。

GPT2LMHeadModel 不仅能做自回归预训练，即传入 labels 的训练，也能直接用于下游任务比如文本生成。GPT-2 源码中 GPT2LMHeadModel 的部分代码如下：