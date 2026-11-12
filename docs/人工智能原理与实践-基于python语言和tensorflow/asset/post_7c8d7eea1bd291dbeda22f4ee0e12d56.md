
序将删除词汇表内容以便节省内存资源； print('Most common words (+UNK)', count[:5])表示程序将输出最常出现的 5 个单词； print('Sample data', data[:10], [reverse_dictionary[i] for i in data[:10]])表示程序将输出转换后的数据库 data 和原来的前 10 个单词； data_index = 0 表示程序将使用 data 来制作训练集。

## 7.2.2 模型结构

关于词向量的模型结构，在初始阶段，每个词汇都会被表示为一个随机向量。通过函数 generate_batch 的定义，来生成 Skip-Gram 模型用的 batch，代码如下。

```
def generate_batch(batch_size, num_skips, skip_window):
  global data_index
  assert batch_size % num_skips == 0
  assert num_skips <= 2 * skip_window
  batch = np.ndarray(shape=(batch_size), dtype=np.int32)
  labels = np.ndarray(shape=(batch_size, 1), dtype=np.int32)
  span = 2 * skip_window + 1 # [ skip_window target skip_window ]
  buffer = collections.deque(maxlen=span)

  for _ in range(span):
    buffer.append(data[data_index])
    data_index = (data_index + 1) % len(data)
  for i in range(batch_size // num_skips):
    target = skip_window # target label at the center of the buffer
    targets_to_avoid = [ skip_window ]
    for j in range(num_skips):
      while target in targets_to_avoid:
        target = random.randint(0, span - 1)
      targets_to_avoid.append(target)
      batch[i * num_skips + j] = buffer[skip_window]
      labels[i * num_skips + j, 0] = buffer[target]
      buffer.append(data[data_index])
      data_index = (data_index + 1) % len(data)

  data_index = (data_index + len(data) - span) % len(data)
  return batch, labels

batch, labels = generate_batch(batch_size=8, num_skips=2, skip_window=1)
for i in range(8):
  print(batch[i], reverse_dictionary[batch[i]], '->', labels[i, 0], reverse_dictionary[labels[i, 0]])
```

源程序代码内容中，global data_index 表示 data_index 是一个指针，它的初始值为 0； data_index = (data_index + 1) % len(data)表示 data_index 从当前数据位置开始，每生成一个 batch， data_index 的数值就会相应地进行加 1 操作； targets_to_avoid = [skip_window]实现样本不重复的保障目标； data_index = (data_index + 1) % len(data)表示每利用 buffer 生成 num_skips 个样本， data_index 就将进行加 1 操作； batch, labels = generate_batch(batch_size=8, num_skips=2, skip_window=1) 表示 generate_batch 函数每进行一次运行，就将产生一个 batch 以及它所对应的标签 labels，这个函数包含有 3 个参数，其中，参数 batch_size 表示的是一个 batch 中单词对的个数；num_skips=2, skip_window=1 表示程序会先选取长度为 3 的 buffer，假设它的内容是['onion', 'love', 'sampan']，单词 love 位于中间位置，onion 和 sampan 这两个单词作为单词 love 的上下文，在这两个单词中选择 num_skips 来生成标签，因为 num_skips=2，所以会将两个单词都选上，从而形成 love->onion 和 love->sampan 的形式，每次训练都会在 skip_window*2 个单词中选择 num_skips 个单词，并且单词不能重复，所以，通常会要求 skip_window*2>=num_skips。

在训练过程中，每一次的训练都会调用一次 generate_batch 函数，同时，返回的 batch 值和 labels 值作为训练数据进行训练。

## 7.2.3 处理噪声对比

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

源程序代码内容中，embedding_size = 128 表示词嵌入空间是 128 维的，即 Word2Vec 中的 Vec 是一个 128 维的向量。skip_window = 1，num_skips = 2 表示训练是在 skip_window *2 个单词中选择 num_skips 个单词。在训练过程中，会对模型进行验证，方法是寻找和某个特定单词最接近的单词，另外，这里只对前 valid_window 个词进行验证，因为这些词最常出现。valid_size = 16 表示每次验证 16 个单词；valid_window = 100 表示这 16 个词是在前 100 个最常见的词中选出来的；num_sampled = 64 表示构造损失时选取的噪声词的数量；embeddings = tf.Variable (tf.random_uniform([vocabulary_size, embedding_size], -1.0, 1.0))表示定义一个 embeddings 变量，相当于一行存储一个词的词嵌入；embed = tf.nn.embedding_lookup(embeddings, train_inputs)表示利用 embedding_lookup 可得到一个 batch 内所有的词嵌入；optimizer = tf.train.GradientDescentOptimizer(1.0).minimize(loss)表示得到 loss 后，构造出一个优化器；init = tf.global_variables_initializer()表示变量初始化步骤；在训练模型时，还需要对模型进行验证。选出一些单词作为验证使用，计算在嵌入空间中与其最相近的词。直接得到的词嵌入矩阵在不同维度空间上有不同的大小，所以，为了使得计算出的相似度更加合理，会先对其做一次归一化，用完成归一化的 normalized_embeddings 来进行验证，求得目标单词与其他单词之间的相似度。

## 7.2.4 模型训练

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
print('Average loss at step ', step, ': ', average_loss)
average_loss = 0

if step % 10000 == 0:
    sim = similarity.eval()
    for i in xrange(valid_size):
        valid_word = reverse_dictionary[valid_examples[i]]
        top_k = 8
        nearest = (-sim[i, :]).argsort() [1:top_k + 1]
        log_str = 'Nearest to %s:' % valid_word
        for k in xrange(top_k):
            close_word = reverse_dictionary[nearest[k]]
            log_str = '%s %s,' % (log_str, close_word)
        print(log_str)
final_embeddings = normalized_embeddings.eval()
```

源程序代码内容中,if step > 0:average_loss /= 2000 表示的是求取 2000 个 batch 的平均损失;if step % 10000 == 0:表示每执行 10000 步, 就进行一次验证, 选取一些验证单词, 挑选出在当前嵌入空间中与其距离最接近的一些单词, 并将这些单词进行输出, 输出结果是完全随机的, 没有什么特殊的意义; sim = similarity.eval()表示 sim 是验证单词与所有单词之间的相似度; for i in xrange(valid_size):表示有验证单词的取值范围是 valid_size; top_k = 8 表示输出的结果是最相邻的 8 个单词; final_embeddings = normalized_embeddings.eval()表示 final_embeddings 是最后得到的词嵌入向量, 它是一个归一后的词嵌入向量形式, 它的形状是[vocabulary_size, embedding_size], 每一行所代表的是对应 index 词的词嵌入表示。例如 final_embeddings[1, :]所表示的就是 id 为 1 的单词所对应的词嵌入表示, final_embeddings[53, :]所表示的就是 id 为 53 的单词所对应的词嵌入表示, 所有单词都以上述相同的方法进行类推。经过此部分代码块的运算, 词嵌入空间中的向量表示已经具备了一定的含义。单词与单词之间的相似性更加容易理解。如果增加训练的步数, 并对模型中所出现的参数进行更加合理的微调, 输出的词嵌入表示也将会更加精确。

## 7.3 嵌入学习可视化与评估

当程序进行到 final_embeddings 时, 可求得输出内容, 运行基本结束了, 如果还想对运算的结果进行可视化表示, 则可以继续输入可视化功能的代码, 具体代码如下。

```
def plot_with_labels(low_dim_embs, labels, filename='tsne.png'):
    assert low_dim_embs.shape[0] >= len(labels), 'More labels than embeddings'
    plt.figure(figsize=(18, 18)) #in inches
    for i, label in enumerate(labels):
        x, y = low_dim_embs[i, :]
        plt.scatter(x, y)
        plt.annotate(label, xy=(x, y), xytext=(5, 2), textcoords='offset points', ha='right', va='bottom')
    plt.savefig(filename)

try:
    from sklearn.manifold import TSNE
    import matplotlib.pyplot as plt
    tsne = TSNE(perplexity=30, n_components=2, init='pca', n_iter=5000)
    plot_only = 300
    low_dim_embs = tsne.fit_transform(final_embeddings[:plot_only, :])
    labels = [reverse_dictionary[i] for i in xrange(plot_only)]
    plot_with_labels(low_dim_embs, labels)

except ImportError:
    print('Please install sklearn, matplotlib, and scipy to show embeddings.')
```

源程序代码内容中，def plot_with_labels(low_dim_embs, labels, filename='tsne.png'):表示可视化的图片最终保存的名称为 tsne.png; tsne = TSNE(perplexity=30, n_components=2, init='pca', n_iter=5000)表示使用 t-SNE 方法进行了一个降维处理，因为之前针对词嵌入的大小进行过设定，为 128 维，即每个单词都会被表示为一个 128 维度的向量，在进行可视化处理时，无法将 128 维的空间直接表示出来，所以，针对此种状况，首先进行了降维操作，使 128 维的空间映射成为一个 2 维的空间，并在此空间中进行单词位置的刻画；plot_only = 300 表示的是在可视化处理时，只刻画出 300 个单词，生成的可视化效果图如图 7-3 所示。

![](img/4aa0d33e89ab0b6060ece712474d6e70_172_0.png)

## 图 7-3
