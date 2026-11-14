具体代码步骤如下。

# 实例描述

使用条件变分自编码模型，通过指定标签输入生成对应类别的 MNIST 模拟数据。

# 1. 添加标签占位符

在 “10-8 变分自编码.py” 代码基础上修改，添加占位符 y。

# 代码10-9 条件变分自编码

```
01 ......
02 y = tf.placeholder(tf.float32, [None, n_labels])
03 ......
```

# 2. 添加输入全连接权重

添加全连接层的权重'wlab1'与'blab1'，作为输入标签的特征转换。这里输入的标签也转换成 256 个输出，因为最终要连接到原始的图片全连接输出，所以到第二层全连接时，输入就变成了 256×2，因此也需要将 mean_w1 和 log_sigma_w1 的输入修改成 n_hidden_1×2。

# 代码10-9 条件变分自编码（续）

```
04 weights = {
05
06     'w1': tf.Variable(tf.truncated_normal([n_input, n_hidden_1],
07         stddev=0.001)),
08     'b1': tf.Variable(tf.zeros([n_hidden_1])),
09
10     'wlab1': tf.Variable(tf.truncated_normal([n_labels, n_hidden_1],
11         stddev=0.001)),
12     'blab1': tf.Variable(tf.zeros([n_hidden_1])),
13     'mean_w1': tf.Variable(tf.truncated_normal([n_hidden_1*2, n_
14         hidden_2],
15         stddev=0.001)),
16     'log_sigma_w1': tf.Variable(tf.truncated_normal([n_hidden_1*2, n_
17         hidden_2],
18         stddev=0.001)),
19 ......
```

同样，对于生成的 z 也要与 label 连接后输入加码器，所以 w2 的输入维度需要被改成 n_hidden_2+n_labels。

# 3. 修改模型，将标签输入接入编码

定义新节点 hlab1 为输入标签的输出，接着使用 concat 函数将它与原来的 h1 合并到一起，变成 hall1。此时，hall1 的 shape 为[batch_size,n_hidden_1×2]。接着，将合成好的 hall1 代替原来的 h1 输入 z_mean 与 z_log_sigma_sq 中。