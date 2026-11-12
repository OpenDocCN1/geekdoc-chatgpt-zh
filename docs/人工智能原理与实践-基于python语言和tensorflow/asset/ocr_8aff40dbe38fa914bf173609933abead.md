每次训练都会在 skip*2 个单词中选择 num_skips 个单词，并且单词不能重复，所以，通常会要求 skip_window*2>=num_skips。

在训练过程中，每一次的训练都会调用一次 generate_batch 函数，同时，返回的 batch 值和 labels 值作为训练数据进行训练。

# ## 7.2.3 处理噪声对比

模型可以抽象为用一个单词来对另一个单词进行预测，在实际输出时，不使用 softmax 损失，而使用 NCE 损失，即通过选取一些噪声单词作为负采样进行分类。

所谓的负采样（Negative Sampling）是指，在神经网络的训练过程中，需要输入训练样本并且不断调整神经元的权重，从而不断提高对目标的准确预测。神经网络每经过一个训练样本的训练，它的权重就会进行一次调整。正如上面所讨论的，词汇表的大小决定了 Skip-Gram 神经网络将会拥有大规模的权重矩阵，所有的这些权重需要通过数以亿计的训练样本来进行调整，这是非常消耗计算资源的，并且实际训练会非常慢。

负采样解决了这个问题，它是用来提高训练速度并且改善所得到词向量的质量的一种方法。不同于每个训练样本更新所有的权重，负采样每次让一个训练样本仅更新一小部分的权重，这样就会降低梯度下降过程中的计算量，代码如下。

```
batch_size = 128
embedding_size = 128
skip_window = 1
num_skips = 2
valid_size = 16
valid_window = 100
valid_examples = np.random.choice(valid_window, valid_size, replace=False)

#构造损失时选取的噪声词的数量
num_sampled = 64
graph = tf.Graph()
with graph.as_default():

#输入的 batch
train_inputs = tf.placeholder(tf.int32, shape=[batch_size])
train_labels = tf.placeholder(tf.int32, shape=[batch_size, 1])

#用于验证的词
valid_dataset = tf.constant(valid_examples, dtype=tf.int32)

#在 cpu 上定义模型
with tf.device('/cpu:0'):
    embeddings = tf.Variable(tf.random_uniform([vocabulary_size, embedding_size], -1.0, 1.0))
    embed = tf.nn.embedding_lookup(embeddings, train_inputs)

#创建两个变量用于 NCE Loss（即选取噪声词的二分类损失）
nce_weights = tf.Variable(tf.truncated_normal([vocabulary_size, embedding_size],
  stddev=1.0 / math.sqrt(embedding_size)))
nce_biases = tf.Variable(tf.zeros([vocabulary_size]))

#tf.nn.nce_loss 会自动选取噪声词，并且形成损失
#随机选取 num_sampled 个噪声词
loss = tf.reduce_mean(tf.nn.nce_loss(weights=nce_weights, biases=nce_biases,
```