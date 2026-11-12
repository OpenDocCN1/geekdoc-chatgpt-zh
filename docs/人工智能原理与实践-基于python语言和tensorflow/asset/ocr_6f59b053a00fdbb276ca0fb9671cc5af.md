```
if step % 100 == 0:
print 'Step %d: loss = %.2f (%.3f sec)' % (step, loss_value, duration)
```

# 4.4.2 状态可视化

为了释放 TensorBoard 所使用的事件文件（events file），所有的即时数据（在这里只有一个）都要在图表构建阶段合并至一个操作（op）中，代码如下。

```
summary_op = tf.merge_all_summaries()
```

在创建好 Session 之后，可以实例化一个 tf.train.SummaryWriter，用于写入包含了图表本身和即时数据具体值的事件文件，代码如下。

```
summary_writer = tf.train.SummaryWriter(FLAGS.train_dir, graph_def=sess.graph_def)
```

最后，每次运行 summary_op 时，都会往事件文件中写入最新的即时数据，函数的输出会传入事件文件读写器（writer）的 add_summary()函数，代码如下。

```
summary_str = sess.run(summary_op, feed_dict=feed_dict)
summary_writer.add_summary(summary_str, step)
```

事件文件写入完毕之后，可以在训练文件夹中打开一个 TensorBoard，查看即时数据的情况，如图 4-3 所示。

![](bbox=[0.143, 0.422, 0.802, 0.683])

图 4-3

TensorBoard 通过读取 TensorFlow 的事件文件来运行。TensorFlow 的事件文件包括了在 TensorFlow 运行中涉及的主要数据。下面是 TensorBoard 中汇总数据（summary data）的大体生命周期。

首先，创建想汇总数据的 TensorFlow 图，然后选择在哪个节点进行汇总（summary）操作。

比如，假设正在训练一个卷积神经网络，用于识别 MNIST 标签。希望记录学习速度（learning rate）如何变化，以及目标函数如何变化。可以通过向节点附加 scalar_summary 操作来分别输出学习速度和期望误差。然后可以给每个 scalar_summary 分配一个有意义的标签，比如 learning rate 和 loss function。

如果还希望显示一个特殊层中激活的分布，或者梯度权重的分布，可以通过分别附加 histogram_summary 运算来收集权重变量和梯度输出。

所有可用的 summary 操作详细信息，可以查看 summary_operation 文档。

在 TensorFlow 中，所有的操作只有执行时，或者另一个操作依赖于它的输出时才会运行。刚才