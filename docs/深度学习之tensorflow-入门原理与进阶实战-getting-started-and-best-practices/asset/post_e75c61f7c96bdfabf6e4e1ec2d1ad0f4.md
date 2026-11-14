
## 代码 9-24 yuyinutils（续）

```python
123 ......
124 def pad_sequences(sequences, maxlen=None, dtype=np.float32,
125                 padding='post', truncating='post', value=0.):
126
127     lengths = np.asarray([len(s) for s in sequences], dtype=np.int64)
128
129     nb_samples = len(sequences)
130     if maxlen is None:
131         maxlen = np.max(lengths)
132
133     # 从第一个非空的序列中得到样本形状
134     sample_shape = tuple()
135     for s in sequences:
136         if len(s) > 0:
137             sample_shape = np.asarray(s).shape[1:]
138             break
139
140     x = (np.ones((nb_samples, maxlen) + sample_shape) * value).astype(dtype)
141     for idx, s in enumerate(sequences):
142         if len(s) == 0:
143             continue  # 如果序列为空，则跳过
144         if truncating == 'pre':
145             trunc = s[-maxlen:]
146         elif truncating == 'post':
147             trunc = s[:maxlen]
148         else:
149             raise ValueError('Truncating type "%s" not understood' %
150                              truncating)
151
152     # 检查trunc
153     trunc = np.asarray(trunc, dtype=dtype)
154     if trunc.shape[1:] != sample_shape:
155         raise ValueError('Shape of sample %s of sequence at position
156                         %s is different from expected shape %s' %
157                         (trunc.shape[1:], idx, sample_shape))
158
159     if padding == 'post':
160         x[idx, :len(trunc)] = trunc
161     elif padding == 'pre':
162         x[idx, -len(trunc):] = trunc
163     else:
164         raise ValueError('Padding type "%s" not understood' %
165                         padding)
166
167     return x, lengths
```

### 7. 文字样本的转化

对于文本方面的样本，需要将里面的文字转换成具体的向量。get_ch_lable_v 会按照传入的 word_num_map 将 txt_label 或是指定文件中的文字转化成向量。后面的 get_ch_lable 是读取文件操作，本例中用不到。

## 代码 9-24 yuyinutils（续）

```python
164 def get_ch_lable_v(txt_file, word_num_map, txt_label=None):
165     words_size = len(word_num_map)
166     to_num = lambda word: word_num_map.get(word, words_size)
167     if txt_file != None:
168         txt_label = get_ch_lable(txt_file)
169     labels_vector = list(map(to_num, txt_label))
170     return labels_vector
171 def get_ch_lable(txt_file):
172     labels = ""
173     with open(txt_file, 'rb') as f:
174         for label in f:
175             # labels = label.decode('utf-8')
176             labels = labels + label.decode('gb2312')
177     return labels
```

### 8. 密集矩阵转成稀疏矩阵

TensorFlow 中没有密集矩阵转稀疏矩阵函数，所以需要编写一个。该函数比较常用，可以当成工具来储备，具体代码如下。

## 代码 9-24 yuyinutils（续）

```python
186 def sparse_tuple_from(sequences, dtype=np.int32):
187     indices = []
188     values = []
189     for n, seq in enumerate(sequences):
190         indices.extend(zip([n] * len(seq), range(len(seq))))
191         values.extend(seq)
192     indices = np.asarray(indices, dtype=np.int64)
193     values = np.asarray(values, dtype=dtype)
194     shape = np.asarray([len(sequences), indices.max(0)[1] + 1],
195                     dtype=np.int64)
196     return indices, values, shape
```

这里主要是算出 indices、values、shape 这 3 个值，得到之后可以使用 tf.SparseTensor 随时生成稀疏矩阵。

### 9. 将字向量转成文字

字向量转成文字主要有两个函数：sparse_tuple_to_texts_ch 函数，将稀疏矩阵的字向量转成文字；ndarray_to_text_ch 函数，将密集矩阵的字向量转成文字。两个函数都需要传入字表，然后会按照字表对应的索引将字转化回来。

## 代码 9-24 yuyinutils（续）

```python
200 ......
201 # 常量
202 SPACE_TOKEN = '<space>'
203 SPACE_INDEX = 0
204 FIRST_INDEX = ord('a') - 1
205
206 def sparse_tuple_to_texts_ch(tuple, words):
207     indices = tuple[0]
208     values = tuple[1]
209     results = [""] * tuple[2][0]
210     for i in range(len(indices)):
211         index = indices[i][0]
212         c = values[i]
213
214         c = '' if c == SPACE_INDEX else words[c]
215         results[index] = results[index] + c
216     # 返回 strings 的 List
217     return results
218
219 def ndarray_to_text_ch(value, words):
220     results = ""
221     for i in range(len(value)):
222         results += words[value[i]]
223     return results.replace("'''", ' ')
```

## 9.5.3 训练模型

样本准备好后，就开始模型的搭建了。

### 1. 定义占位符

定义 3 个占位符，具体说明如下。

- input_tensor: 为输入的音频数据[None, None, MFCC_features]，第一个是 batch_size 用 None 来表示；第二个是时序数也用 None 来表示，因为每一批次的时序都是不同的；第三个是 MFCC 的特征，是取当前特征 n_input 和前后 n_context 个特征的组合，即 2 × n_context + 1 个序列，每个序列特征数为 n_input，于是得出 n_input + (2 × n_input * n_context)。
- targets: 音频数据所对应的文本，是一个稀疏矩阵的占位符。
- seq_length: 当前 batch 数据的序列长度。
- keep_dropout: dropout 的参数。

```python
......
input_tensor = tf.placeholder(tf.float32, [None, None, n_input + (2 * n_input * n_context)], name='input')    # 语音 MFCC features
# ctc_loss 计算时需要使用 sparse_placeholder 来生成 SparseTensor
targets = tf.sparse_placeholder(tf.int32, name='targets') # 文本
# 1D array of size [batch_size]
seq_length = tf.placeholder(tf.int32, [None], name='seq_length')    # 序列长
keep_dropout = tf.placeholder(tf.float32)
```

### 2. 构建网络模型

网络模型使用了双向 RNN 的结构，并将其封装在 BiRNN_model 函数里。调用的代码如下。

```python
logits = BiRNN_model(input_tensor, tf.to_int64(seq_length), n_input,
                     n_context, words_size + 1, keep_dropout)
```

BiRNN_model 的定义如下。

使用 3 个 1024 节点的全连接层，然后是一个双向 RNN，最后接上 2 个全连接层，并且都带有 dropout 层。这里使用的激活函数是带截断的 Relu，截断值设为 20。学习参数的初始化使用标准差为 0.046875 的 random_normal。keep_dropout_rate 为 0.95。

```python
......
b_stddev = 0.046875
h_stddev = 0.046875
n_hidden = 1024
n_hidden_1 = 1024
n_hidden_2 = 1024
n_hidden_5 = 1024
n_cell_dim = 1024
n_hidden_3 = 2 * 1024
keep_dropout_rate = 0.95
relu_clip = 20
def BiRNN_model(batch_x, seq_length, n_input, n_context, n_character,
                keep_dropout):
    # batch_x_shape: `[batch_size, n_steps, n_input + 2 * n_input * n_context]`
    batch_x_shape = tf.shape(batch_x)
    # 将输入转成时间序列优先
    batch_x = tf.transpose(batch_x, [1, 0, 2])
    # 再转成 2 维传入第一层
    batch_x = tf.reshape(batch_x,
                         [-1, n_input + 2 * n_input * n_context])
    # (n_steps*batch_size, n_input + 2*n_input*n_context)
    # 使用 clipped RELU activation and dropout.
    # 第一层
    with tf.name_scope('fc1'):
        b1 = variable_on_cpu('b1', [n_hidden_1], tf.random_normal_initializer(stddev=b_stddev))
        h1 = variable_on_cpu('h1', [n_input + 2 * n_input * n_context,
                                    n_hidden_1],
                           tf.random_normal_initializer(stddev=h_stddev))
        layer_1 = tf.minimum(tf.nn.relu(tf.add(tf.matmul(batch_x, h1),
                                               b1)), relu_clip)
        layer_1 = tf.nn.dropout(layer_1, keep_dropout)
    # 第二层
    with tf.name_scope('fc2'):
        b2 = variable_on_cpu('b2', [n_hidden_2], tf.random_normal_initializer(stddev=b_stddev))
        h2 = variable_on_cpu('h2', [n_hidden_1, n_hidden_2], tf.random_normal_initializer(stddev=h_stddev))
        layer_2 = tf.minimum(tf.nn.relu(tf.add(tf.matmul(layer_1, h2),
                                               b2)), relu_clip)
        layer_2 = tf.nn.dropout(layer_2, keep_dropout)
    # 第三层
    with tf.name_scope('fc3'):
        b3 = variable_on_cpu('b3', [n_hidden_3], tf.random_normal_initializer(stddev=b_stddev))
        h3 = variable_on_cpu('h3', [n_hidden_2, n_hidden_3], tf.random_normal_initializer(stddev=h_stddev))
        layer_3 = tf.minimum(tf.nn.relu(tf.add(tf.matmul(layer_2, h3),
                                               b3)), relu_clip)
        layer_3 = tf.nn.dropout(layer_3, keep_dropout)
    # 双向 RNN
    with tf.name_scope('lstm'):
        # 前向 cell:
        lstm_fw_cell = tf.contrib.rnn.BasicLSTMCell(n_cell_dim, forget_bias=1.0, state_is_tuple=True)
        lstm_fw_cell = tf.contrib.rnn.DropoutWrapper(lstm_fw_cell,
                                                      input_keep_prob=keep_dropout)
        # 反向 cell:
        lstm_bw_cell = tf.contrib.rnn.BasicLSTMCell(n_cell_dim, forget_bias=1.0, state_is_tuple=True)
        lstm_bw_cell = tf.contrib.rnn.DropoutWrapper(lstm_bw_cell,
                                                      input_keep_prob=keep_dropout)
        # 'layer_3' ' [n_steps, batch_size, 2*n_cell_dim]'
        layer_3 = tf.reshape(layer_3, [-1, batch_x_shape[0], n_hidden_3])
        outputs, output_states = tf.nn.bidirectional_dynamic_rnn(
            cell_fw=lstm_fw_cell,
            cell_bw=lstm_bw_cell,
            inputs=layer_3,
            dtype=tf.float32,
            time_major=True,
            sequence_length=seq_length)

        # 连接正、反向结果[n_steps, batch_size, 2*n_cell_dim]
        outputs = tf.concat(outputs, 2)
        # 转化形状[n_steps*batch_size, 2*n_cell_dim]
        outputs = tf.reshape(outputs, [-1, 2 * n_cell_dim])


        with tf.name_scope('fc5'):
            b5 = variable_on_cpu('b5', [n_hidden_5], tf.random_normal_initializer(stddev=b_stddev))
            h5 = variable_on_cpu('h5', [(2 * n_cell_dim), n_hidden_5],
                tf.random_normal_initializer(stddev=h_stddev))
            layer_5 = tf.minimum(tf.nn.relu(tf.add(tf.matmul(outputs, h5),
                b5)), relu_clip)
        layer_5 = tf.nn.dropout(layer_5, keep_dropout)

        with tf.name_scope('fc6'):
            # 全连接层用于 softmax 分类
            b6 = variable_on_cpu('b6', [n_character], tf.random_normal_initializer(stddev=b_stddev))
            h6 = variable_on_cpu('h6', [n_hidden_5, n_character], tf.random_normal_initializer(stddev=h_stddev))
            layer_6 = tf.add(tf.matmul(layer_5, h6), b6)

        # 将二维[n_steps*batch_size, n_character]转成三维 time-major [n_steps, batch_size, n_character].
        layer_6 = tf.reshape(layer_6, [-1, batch_x_shape[0], n_character])

        # Output shape: [n_steps, batch_size, n_character]
        return layer_6

    """
    使用 CPU memory.
    """
def variable_on_cpu(name, shape, initializer):
    # 使用/cpu:0 device
    with tf.device('/cpu:0'):
        var = tf.get_variable(name=name, shape=shape, initializer=
            initializer)
    return var
```

这里的 shape 变化比较复杂，需要先将输入变为二维的 Tensor，才可以传入全连接层。
全连接层进入 BIRNN 时也需要形状转换成三维的 Tensor，BIRNN 输出的结果是 2× n_hidden，所以后面的全连接层输入是 2×n_hidden，最终输出时还要再转回三维的 Tensor。
与图片分类不同的是，RNN 输出的 outputs 没有取 outputs[-1]，而是全部进入了后面的全连接层。语音识别是对输入的每个时序对应的结果进行转换，所以要将 RNN 的全部结果送入后面的全连接层；而在图片识别中，只是把行当成时序，只需要知道最后一行输入后的结果，所以只取了最后一个时序的输出。
