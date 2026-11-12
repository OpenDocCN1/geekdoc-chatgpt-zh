```
labels=train_labels, inputs=embed,
num_sampled=num_sampled,
num_classes=vocabulary_size))
optimizer = tf.train.GradientDescentOptimizer(1.0).minimize(loss)
#计算单词与单词相互之间的相似度（用于验证）
norm = tf.sqrt(tf.reduce_sum(tf.square(embeddings), 1, keep_dims=True))
normalized_embeddings = embeddings / norm
#找出和验证词的 embedding 并计算它们和所有单词的相似度
valid_embeddings = tf.nn.embedding_lookup(normalized_embeddings, valid_dataset)
similarity = tf.matmul(valid_embeddings, normalized_embeddings, transpose_b=True)
init = tf.global_variables_initializer()
```

源程序代码内容中，embedding_size = 128 表示词嵌入空间是 128 维的，即 Word2Vec 中的 Vec 是一个 128 维的向量。skip_window = 1，num_skips = 2 表示训练是在 skip_window *2 个单词中选择 num_skips 个单词。在训练过程中，会对模型进行验证，方法是寻找和某个特定单词最接近的单词，另外，这里只对前 valid_window 个词进行验证，因为这些词最常出现。valid_size = 16 表示每次验证 16 个单词；valid_window = 100 表示这 16 个词是在前 100 个最常见的词中选出来的；num_sampled = 64 表示构造损失时选取的噪声词的数量；embeddings = tf.Variable (tf.random_uniform([vocabulary_size, embedding_size], -1.0, 1.0))表示定义一个 embeddings 变量，相当于一行存储一个词的词嵌入；embed = tf.nn.embedding_lookup(embeddings, train_inputs)表示利用 embedding_lookup 可得到一个 batch 内所有的词嵌入；optimizer = tf.train.GradientDescent Optimizer(1.0).minimize(loss)表示得到 loss 后，构造出一个优化器；init = tf.global_variables_initializer()表示变量初始化步骤；在训练模型时，还需要对模型进行验证。选出一些单词作为验证使用，计算在嵌入空间中与其最相近的词。直接得到的词嵌入矩阵在不同维度空间上有不同的大小，所以，为了使得计算出的相似度更加合理，会先对其做一次归一化，用完成归一化的 normalized_embeddings 来进行验证，求得目标单词与其他单词之间的相似度。

# 7.2.4 模型训练

完成了模型的定义和噪声处理之后，就可以进行下一步的训练计算了，训练所对应的代码如下。

```
num_steps = 100001
with tf.Session(graph=graph) as session:
#初始化变量
init.run()
print('Initialized')

average_loss = 0
for step in xrange(num_steps):
    batch_inputs, batch_labels = generate_batch(
    batch_size, num_skips, skip_window)
    feed_dict = {train_inputs: batch_inputs, train_labels: batch_labels}

#优化
_, loss_val = session.run([optimizer, loss], feed_dict=feed_dict)
average_loss += loss_val

if step % 2000 == 0:
    if step > 0:
    average_loss /= 2000
```