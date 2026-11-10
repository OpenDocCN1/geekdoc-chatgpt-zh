Embedding 构造要点

（3）第 14 ~ 18 行创建了段落嵌入 token_type_Embeddings，同样调用 Embedding 类，除了固定的隐藏维度 hidden_size 外，还要传入句子类型数 type_vocab_size，通常设为 2。从规模上看，它的权重体量明显小于位置嵌入。

（4）第 19 ~ 22 行定义了词嵌入 word_Embeddings，通过继承 tf.keras.layers.Layer 并使用 add_weight 来声明参数，核心是词表大小 vocab_size，一般在 3 万到 5 万之间，以及隐藏维度 hidden_size。其本质与 TensorFlow 的 Embedding 相同，都是查表操作，因此权重规模远大于位置嵌入和段落嵌入。

（5）第 23 行加入归一化层 Layer Norm，主要负责对 Embedding 模块的最终输出做归一化处理。

## 2. call 流程拆解（第 25 ~ 40 行代码）
实现了 Embedding 模块对输入文本的编码过程。

(1) 第 30 ~ 34 行代码：基于 input_ids 衍生出特征，对每个样本按句子长度生成 position_ids，编号从左到右递增。若 token_type_ids 未提供，则默认整段来自同一句子，所有位置均填 0。

(2) 第 35 行代码：借助 tf.gather，按 input_ids 在词表中对应的序号取出词向量，属于标准的查表。

(3) 第 36 ~ 37 行代码：调用已定义的位置编码与段落编码，把位置信息和分句信息转成相同维度的向量，并用 tf.cast 统一与词向量的格式。

(4) 第 38 ~ 39 行代码：将词向量、位置向量、段落向量直接相加，随后进行归一化，即得到 Embedding 模块的最终特征。

下面聚焦 Encoder 模块，多个子模块的代码区间用虚线分隔。由于底层的 Self-Attention 计算与 Transformer Encoder 原理一致，为避免重复，此处不再展开其内部实现，可回看第 3 章的源码。

[PRE0]
# BERT 预训练骨架拆解
## 1. TFBertEncoder（第 1 ~ 11 行代码）
TFBertEncoder 是 Encoder 的主干，由若干个 TFBertLayer 堆叠而成。

(1) 第 4 行代码：依据设定的层数实例化 TFBertLayer 列表，基本等价于 Transformer Encoder。

(2) 第 6 ~ 9 行代码：逐层传递特征，上层的输出作为下一层的输入。

(3) 第 11 行代码：将结果整理为字典格式后返回。

## 2. TFBertLayer（第 13 ~ 27 行代码）
TFBertLayer 代表单层 Encoder，同时包含 Self-Attention 与 Feed Forward 的操作。

(1) 第 17 行代码：用 TFBertAttention 类来完成 Self-Attention。

(2) 第 18 ~ 19 行代码：分别用 TFBertIntermediate 与 TFBertOutput 两个层来搭建 Feed Forward。

(3) 第 22 ~ 23 行代码：对上一层输出进行 Self-Attention 计算。

（4）第 24 ~ 25 行代码：依次经过 TFBertIntermediate 层，将输入经全连接映射到隐层维度并做非线性激活，隐层通常为 2048 或更大；随后 TFBertOutput 层把隐层特征映射回标准维度，一般为 512。

（5）第 26 行代码：构建 Feed Forward 的残差结构。Feed Forward 被拆成两层实现，残差必须在 TFBertLayer 层完成，Self-Attention 的残差则在其内部完成。

## 3. TFBertAttention（第 29 ~ 40 行代码）
TFBertAttention 构成 Self-Attention 层，由 self_attention 与 dense_output 两小块组成。

（1）第 33 ~ 34 行代码：分别以 TFBertSelfAttention 类和 TFBertSelfOutput 类定义子模块。

（2）第 37 ~ 38 行代码：TFBertSelfAttention 负责 Multi-Head Attention 的计算，输出为多头特征的拼接，因此 TFBertSelfOutput 需要把过大的拼接向量压缩回标准尺寸，对应图 3.15 的前两步。

（3）第 39 行代码：实现 Self-Attention 的残差连接。

## 4. TFBertIntermediate（第 42 ~ 58 行代码）
TFBertIntermediate 负责 Feed Forward 的前半段。

（1）第 46 ~ 48 行代码：使用 tf.keras.layers.Dense 定义全连接层。隐层大小由 config.intermediate_size 决定，BERT 采用输入维度的 4 倍。

（2）第 50 ~ 53 行代码：根据配置选择非线性激活，BERT 多用 GeLU，而原 Transformer 用 ReLU。

（3）第 56 ~ 57 行代码：输入先经全连接扩到 4 倍维度，再经激活得到 Feed Forward 的中间隐层特征。

## 5. TFBertOutput（第 60 ~ 73 行代码）
TFBertOutput 负责 Feed Forward 的后半段。

（1）第 64 ~ 66 行代码：用 tf.keras.layers.Dense 将中间隐层特征映射回标准特征维度 hidden_size。

（2）第 67 行代码：定义归一化层。

# BERT 预训练骨架拆解
(3) 第 70 ~ 71 行代码: 中间隐层特征经全连接恢复为标准维度后，与 Self-Attention 的输出做残差拼接，再执行归一化。

经过 TensorFlow 2 源码的梳理，BERT 预训练的结构已经清晰。在阅读时不必深究每个函数的底层运行机制，结合精简可读的源码理解各模块的算法流程即可。实际上 HuggingFace 原始实现中还包含大量与训练、Batch 计算相关的控制参数，本节为提升可读性删除了与主数据流无关的部分， interested 读者可自行查阅各版本源码。

## 5.5.2 BERT 微调模型
BERT 的训练分两阶段，先用海量无监督文本训练基础模型，再在下游任务的特定数据上微调即可达到 SOTA。下面介绍四类下游任务的源码改写思路。总的来说，只需在 BERT 基础预训练模型外再包一层即可完成微调。

[PRE1]
TFBertForSequenceClassification 类适用于句子分类任务，单句或句对均可。

- （1）第 5 行代码：从配置中读取类别数。若标签是连续值，比如计算两句相似度，需要把相似度离散化。取值范围 0～1 的相似度按 0.2 分桶，可得到 [0,0.2], [0.2,0.4], [0.4,0.6], [0.6,0.8], [0.8,1] 共 5 个区间，对应类别数为 5。