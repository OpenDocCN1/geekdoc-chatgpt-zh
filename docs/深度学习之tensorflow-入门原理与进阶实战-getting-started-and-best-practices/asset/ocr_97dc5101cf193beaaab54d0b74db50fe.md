这回可以理解前面的代码了，norm 代表每一个词对应向量的长度矩阵，见式（9-5）：

![](bbox=[0.38, 0.13, 0.65, 0.26])

normalized_embeddings 表示的意思是向量除以自己的模，即单位向量，它可以确定向量的方向。

很显然，similarity 就是 valid_dataset 中对应的单位向量 valid_embeddings 与整个词嵌入字典中单位向量的夹角余弦。

如图 9-23 所示，算了这么多夹角余弦的目的就是为了衡量两个 n 维向量间的相似程度。当 \(cos\theta\) 为 1 时，表明夹角为 0，即两个向量的方向完全一样。所以当 \(cos\theta\) 的值越小，表明两个向量的方向越不一样，相似度越低。

![](bbox=[0.27, 0.44, 0.76, 0.59])

# 图 9-23 词嵌入夹角余弦结构

# 8. 启动session，训练模型

有了理论基础之后，对代码的模型应该好理解了，接下来启动 session 将模型训练出来。

# 代码9-26 word2vect（续）

```
151 num_steps = 100001
152 with tf.Session() as sess:
153     sess.run( tf.global_variables_initializer() )
154     print('Initialized')
155
156     average_loss = 0
157     for step in range(num_steps):
158         batch_inputs, batch_labels = generate_batch(training_label,
159             batch_size, num_skips, skip_window)
160     feed_dict = {train_inputs: batch_inputs, train_labels: batch_labels}
```