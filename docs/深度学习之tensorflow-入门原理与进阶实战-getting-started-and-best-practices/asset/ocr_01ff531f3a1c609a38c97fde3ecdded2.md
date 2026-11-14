对应的输入关系的权重。如 How 下面一列。

在训练过程中，模型会通过注意力机制把某个输出对应的所有输入列出来，学习其关系并更新到权重上。如图 9-31 所示，“you”下面那一列（80、5、0、15、0），就是模型在生成 you 这个词时的概率分布，对应列的表格中值最大的地方对应的是输入的“你”（对应图中第 1 行第 4 列，值为 80），说明模型在生成 you 这个词时最为关注的输入词是“你”。这样在预测时，该机制就会根据输入及其权重反向推出更有可能的预测值了。

注意力机制是在原有 Seq2Seq 中的 Encoder 与 Decoder 框架中修改而来，具体结构如图 9-32 所示。

修改后的模型特点是序列中每个时刻 Encoder 生成的 c，都将要参与 Decoder 中解码的各个时刻，而不是只参与初始时刻。当然对于生成的结果节点 c，参与到 Decoder 的每个序列运算都会经过权重 w，那么这个 w 就可以以 loss 的方式通过优化器来调节了，最终会逐渐逼近与它紧密的那个词，这就是注意力的原理。添加入了 Attention 注意力分配机制后，使得 Decoder 在生成新的 Target Sequence 时，能得到之前 Encoder 编码阶段每个字符的隐藏层的信息向量 Hidden State，使得新生成序列的准确度提高。

![](bbox=[0.245, 0.445, 0.705, 0.735])

# 2. TensorFlow中的attention_seq2seq

在 TensorFlow 中也有关于带有注意力机制的 Seq2Seq 定义，封装后的 Seq2Seq 与前面 basic_rnn_seq2seq 差不多，具体函数如下：

```
tf.contrib.legacy_seq2seq.embedding_attention_seq2seq (encoder_inputs,
 decoder_inputs,
 cell,
 num_encoder_symbols,
```