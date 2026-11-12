使用类 MuLiRiNNCeLL 可以无缝地将其实现，代码如下。

```
lstm = rnn_cell.BasicLSTMCell(lstm_size)
stacked_lstm = rnn_cell.MultiRNNCell([lstm] * number_of_layers)
initial_state = state = stacked_lstm.zero_state(batch_size, tf.float32)
for i in range(len(num_steps)):
    output, state = stacked_lstm(words[:, i], state)
final_state = state
```

源程序代码内容中，output, state = stacked_Lstm(words[:, i], state)表示每次处理一批词语后更新状态值。

# 8.7 代码的编译与运行

首先需要构建库，在 CPU 上编译，代码如下。

```
bazel build -c opt tensorflow/models/rnn/ptb:ptb_word_lm
```

如果有强大的 GPU 可以运行，代码如下。

```
bazel build -c opt --config=cuda tensorflow/models/rnn/ptb:ptb_word_lm
```

运行模型，代码如下。

```
bazel-bin/tensorflow/models/rnn/ptb/ptb_word_lm \
--data_path=/tmp/simple-examples/data/ --alsologtostderr --model small
```

有 3 个模型配置参数：small, medium 和 large。它们指的是 LSTM 的大小，以及用于训练的超参数集。模型越大，得到的结果应该越好。在测试集中，small 模型应该可以达到低于 120 的复杂度，large 模型则是低于 80 的复杂度，但它可能花费数小时来训练。