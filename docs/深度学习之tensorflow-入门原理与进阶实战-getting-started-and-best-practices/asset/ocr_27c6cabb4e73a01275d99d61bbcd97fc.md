```
```python
'decoder_h2': tf.Variable(tf.random_normal([n_hidden_1, n
input])),
}
biases = {
    'encoder_b1': tf.Variable(tf.zeros([n_hidden_1])),
    'encoder_b2': tf.Variable(tf.zeros([n_hidden_2])),
    'decoder_b1': tf.Variable(tf.zeros([n_hidden_1])),
    'decoder_b2': tf.Variable(tf.zeros([n_input])),
}
```
```

```
```python
# 编码
def encoder(x):
    layer_1 = tf.nn.sigmoid(tf.add(tf.matmul(x, weights['encoder_
h1']),biases['encoder_b1']))
    layer_2 = tf.nn.sigmoid(tf.add(tf.matmul(layer_1, weights
['encoder_h2']), biases['encoder_b2']))
    return layer_2
```
```

```
```python
# 解码
def decoder(x):
    layer_1 = tf.nn.sigmoid(tf.add(tf.matmul(x, weights['decoder_
h1']),biases['decoder_b1']))
    layer_2 = tf.nn.sigmoid(tf.add(tf.matmul(layer_1, weights
['decoder_h2']),biases['decoder_b2']))
    return layer_2
```
```

```
```python
#输出的节点
encoder_out = encoder(x)
pred = decoder(encoder_out)

# cost 为 y 与 pred 的平方差
cost = tf.reduce_mean(tf.pow(y - pred, 2))
optimizer = tf.train.RMSPropOptimizer(learning_rate).minimize(cost)
```
```

上面代码里先定义了学习率为 0.01，这个值可以动态调节，会直接影响到收敛速度和学习的准确性，由于输出标签也是输入标签，所以后面直接定义 y=x。

# 3. 开始训练

接下来设置训练参数，一次取 256 条数据，将所有的训练数据集进行 20 次的迭代训练。

# 代码10-1 自编码（续）

```
```python
# 训练参数
training_epochs = 20      #一共迭代 20 次
batch_size = 256          #每次取 256 个样本
display_step = 5          #迭代 5 次输出一次信息

# 启动会话
with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())
    total_batch = int(mnist.train.num_examples/batch_size)
    # 开始训练
```
```