# 代码10-6 自编码综合（续）

```
#第 1 层的编码输出
l1_out = tf.nn.sigmoid(tf.add(tf.matmul(x, weights['h1']), biases
['b1']))

#l1 解码器 MODEL
def noise_l1_autodecoder(layer_1, _weights, _biases, _keep_prob):
    layer_lout = tf.nn.dropout(layer_1, _keep_prob)
    layer_2 = tf.nn.sigmoid(tf.add(tf.matmul(layer_lout, _weights
    ['l1_h2']]), _biases['l1_b2']))
    layer_2out = tf.nn.dropout(layer_2, _keep_prob)
    return tf.nn.sigmoid(tf.matmul(layer_2out, _weights['l1_out']) +
    _biases['l1_out'])

# 第一层的解码输出
l1_reconstruction = noise_l1_autodecoder(l1_out, weights, biases,
dropout_keep_prob)

# 计算 COST
l1_cost = tf.reduce_mean(tf.pow(l1_reconstruction-y, 2))
# OPTIMIZER
l1_optm = tf.train.AdamOptimizer(0.01).minimize(l1_cost)
```

# 5. 第2层网络结构

为第 2 层建立一个自编码网络，并定义其网络结构。

# 代码10-6 自编码综合（续）

```
#12 解码器 MODEL
def l12_autodecoder(layer1_2, _weights, _biases):
    layer1_2out = tf.nn.sigmoid(tf.add(tf.matmul(layer1_2, _weights
    ['l12_h2']]), _biases['l12_b2']))
    return tf.nn.sigmoid(tf.matmul(layer1_2out, _weights['l12_out']) +
    _biases['l12_out'])

#第二层的编码输出
12_out = tf.nn.sigmoid(tf.add(tf.matmul(l2x, weights['l2_h1']),
biases['l2_b1']))

# 第二层的解码输出
l2_reconstruction = l2_autodecoder(l2_out, weights, biases)

# COST 计算
l2_cost = tf.reduce_mean(tf.pow(l2_reconstruction-l2y, 2))
# 优化器
optm2 = tf.train.AdamOptimizer(0.01).minimize(l2_cost)
```

# 6. 第3层网络结构

为第 3 层建立一个自编码网络，并定义其网络结构。

# 代码10-6 自编码综合（续）

```
13_out = tf.matmul(l3x, weights['out']) + biases['out']
```