创建的这些节点（ summary nodes ）都围绕着图像，没有任何操作依赖于它们的结果。因此，为了生成汇总信息，需要运行所有的这些节点。这样的手动工作是很乏味的，因此可以使用 tf.merge_all_summaries 来将它们合并为一个操作。

然后可以执行合并命令，它会依据特定步骤将所有数据生成一个序列化的 Summary protobuf 对象。最后，为了将汇总数据写入磁盘，需要将汇总的 protobuf 对象传递给 tf.train.SummaryWriter。

SummaryWriter 的构造函数中包含了参数 logdir。这个 logdir 非常重要，所有事件都会写到它所指的目录下。此外，SummaryWriter 中还包含了一个可选择的参数 GraphDef。如果输入了该参数，那么 TensorBoard 也会显示图像。

现在就可以运行神经网络了。可以每一步执行一次合并汇总，这样会得到一大堆训练数据，但这很有可能超过了想要的数据量。也可以每 100 步执行一次合并汇总，或者如下面示范代码所示。

```
merged_summary_op = tf.merge_all_summaries()
summary_writer = tf.train.SummaryWriter('/tmp/mnist_logs', sess.graph)
total_step = 0
while training:
    total_step += 1
    session.run(training_op)
    if total_step % 100 == 0:
        summary_str = session.run(merged_summary_op)
        summary_writer.add_summary(summary_str, total_step)
```

现在已经准备好用 TensorBoard 来可视化这些数据了。

输入下面的指令来启动 TensorBoard。

```
python tensorflow/tensorboard/tensorboard.py --logdir=path/to/log-directory
```

这里的参数 logdir 指向 SummaryWriter 序列化数据的存储路径。如果 logdir 目录的子目录中包含另一次运行时的数据，那么 TensorBoard 会展示所有运行的数据。一旦 TensorBoard 开始运行，即可通过在浏览器中输入 localhost:6006 来查看 TensorBoard。

如果已经通过 pip 安装了 TensorBoard，则可以通过执行更为简单的命令来访问 TensorBoard，代码如下。

```
tensorboard --logdir=/path/to/log-directory
```

进入 TensorBoard 的界面时，会在右上角看到导航选项卡，每一个选项卡将展现一组可视化的序列化数据集。对于查看的每一个选项卡，如果 TensorBoard 中没有数据与这个选项卡相关，则会显示一条提示信息指示该如何序列化相关数据。

# 4.5 评估模型

模型性能如何呢？

首先找出那些预测正确的标签。tf.argmax 是一个非常有用的函数，它能给出某个张量对象在某 1 维上其数据最大值所在的索引值。由于标签向量是由 0 和 1 组成的，因此最大值 1 所在的索引位置就是类别标签，比如 tf.argmax(y,1)返回的是模型对于任一输入 x 预测到的标签值，而