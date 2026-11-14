```
['decoder_h4']),                 biases['decoder_b4']]))
return layer_4
# 构建模型
encoder_op = encoder(x)
y_pred = decoder(encoder_op)         # 784 维度
cost = tf.reduce_mean(tf.pow(y - y_pred, 2))
optimizer = tf.train.AdamOptimizer(learning_rate).minimize(cost)
```

# 3. 开始训练

这一步中还是一次取 256 条数据，将全部数据集迭代 20 次。

# 代码10-2 自编码进阶（续）

```
#训练
training_epochs = 20      # 迭代训练 20 一次
batch_size = 256
display_step = 1

with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())
    total_batch = int(mnist.train.num_examples/batch_size)
    # 启动循环开始训练
    for epoch in range(training_epochs):
        # 遍历全部数据集
        for i in range(total_batch):
            batch_xs, batch_ys = mnist.train.next_batch(batch_size)
            _, c = sess.run([optimizer, cost], feed_dict={x: batch_xs})
        # 显示训练中的详细信息
        if epoch % display_step == 0:
            print("Epoch:", '%04d' % (epoch+1),
                  "cost=", "{:.9f}".format(c))
    print("完成!")
```

输出结果如下：

```
Epoch: 0001 cost= 0.106694221
Epoch: 0002 cost= 0.096146211
Epoch: 0003 cost= 0.089687020
Epoch: 0004 cost= 0.085342437
Epoch: 0005 cost= 0.076942392
Epoch: 0006 cost= 0.077152036
Epoch: 0007 cost= 0.074504733
Epoch: 0008 cost= 0.071438089
Epoch: 0009 cost= 0.070937753
Epoch: 0010 cost= 0.067885153
Epoch: 0011 cost= 0.068935215
Epoch: 0012 cost= 0.067724347
Epoch: 0013 cost= 0.065405361
Epoch: 0014 cost= 0.069433592
Epoch: 0015 cost= 0.068582796
```