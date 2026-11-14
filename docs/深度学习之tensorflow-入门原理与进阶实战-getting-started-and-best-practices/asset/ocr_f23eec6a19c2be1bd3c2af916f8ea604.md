```
#生成 15 个连续序列，将 con 和 sin 随机偏移变化后的值叠加起来
def generate_data(isTrain, batch_size):
    seq_length = 15
    if isTrain :
        return do_generate_x_y(isTrain, batch_size, seq_length=seq_length)
    else:
        return do_generate_x_y(isTrain, batch_size, seq_length=seq_length*2)
```

将该曲线按照 30 个序列一组的样式组成训练用的样本。30 个序列分成了两部分：一部分当成现在的序列 batch_x，一部分当成将来的序列 batch_y。

# 2. 定义参数及网络结构

前面介绍过 basic_rnn_seq2seq 的输入是一个 list，这与我们平时遇到过的模型不太一样，所以需要构建一个 list，以方便传入 basic_rnn_seq2seq 中。

在代码中，定义 3 个 list（encoder_input、expected_output、decode_input），按照时间序列的数量来循环创建占位符，并使用 append 方法放入到 list 中。

网络模型定义为 2 层的循环网络，每层 12 个 GRUcell。用 MultiRNNCell 将 cell 定义好后与前面的 list 一起传入 basic_rnn_seq2seq 中。

生成的结果为 dec_outputs，dec_outputs 中为每个时刻有 12 个 GRUcell 的输出，所以还需要通过循环在每个时刻下加一个全连接层，将其转为输出维度 output_dim（output_dim=1）的节点。

# 代码9-30 基本Seq2Seq (续)

```
sample_now, sample_f = generate_data(isTrain=True, batch_size=3)
print("training examples : ")
print(sample_now.shape)
print("(seq_length, batch_size, output_dim)")
seq_length = sample_now.shape[0]
batch_size = 10
output_dim = input_dim = sample_now.shape[-1]
hidden_dim = 12
layers_stacked_count = 2
# 学习率
learning_rate = 0.04
nb_iters = 100
lambda_l2_reg = 0.003
 tf.reset_default_graph()
encoder_input = []
expected_output = []
decode_input = []
```