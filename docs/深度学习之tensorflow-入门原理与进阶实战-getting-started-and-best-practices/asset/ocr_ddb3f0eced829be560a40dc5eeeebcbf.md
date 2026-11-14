在 tf.contrib.legacy_seq2seq 下；另一套为 TensorFlow 1.0 版本之后推出的新接口，在 tf.contrib.seq2seq 下。

旧接口的功能相对简单，是静态展开的网络模型。而新接口的功能更加强大，使用的是动态展开的网络模型，并提供了训练和应用两种场景的 Helper 类封装。从使用角度来看，旧接口同样也是比较简单。而新接口会更加灵活，需要自己组建 Encoder 和 Decoder 并通过函数把它们手动连接起来。

为了便于理解，本书主要以旧接口中 Seq2Seq 框架来举例介绍。关于 Seq2Seq 的更多例子，及新接口的应用演示，可以参考如下网址中的实例：

https://github.com/ematvey/tensorflow-seq2seq-tutorials

旧接口中基本 Seq2Seq 函数的定义如下。

```
tf.contrib.legacy_seq2seq.basic_rnn_seq2seq(encoder_inputs,
    decoder_inputs,
    cell,
    dtype=dtypes.float32,
    scope=None)
```

参数说明如下。

+   * encoder_inputs：一个形状为[batch_size x input_size]的 list。
+   * decoder_inputs：同 encoder_inputs。
+   * cell：定义的 cell 网络。
+   * dtype：encoder_inputs 和 decoder_inputs 中的类型（默认是 tf.float32）。
+   * 返回值：outputs 和 state。outputs 为 [batch_size, output_size]的张量；state 为 [batch_size, cell.state_size]; cell.state_size 可以表示一个或者多个子 cell 的状态，视输入参数 cell 而定。

其函数的实现只有如下几行代码：

```
with variable_scope.variable_scope(scope or "basic_rnn_seq2seq"):
    enc_cell = copy.deepcopy(cell)
    ', enc_state = core_rnn.static_rnn(enc_cell, encoder_inputs,
    dtype=dtype)
    return rnn_decoder(decoder_inputs, enc_state, cell)
```

现将传入的 cell 做一次深拷贝（deepcopy），用来当做 Encoder 的网络，将生成的结果和原来的 cell 再加上输入的 decoder_inputs 一起放到 Decoder 中，并输出生成结果。

> 注意：在使用过程中，由于需要通过输入 x 来预测 y，没有标签，这种情况就需要手动填充 Decoder 来代替训练时的标签。

# 9.8.3 实例 72: 使用 basic_rnn_seq2seq 拟合曲线

TensorFlow 虽然对 Seq2Seq 的框架的封装只用一个函数就完成了。但是，Seq2Seq 的这个函数用起来并不友好，跟我们以前使用的 TensorFlow 中的函数并不是一样，所以有