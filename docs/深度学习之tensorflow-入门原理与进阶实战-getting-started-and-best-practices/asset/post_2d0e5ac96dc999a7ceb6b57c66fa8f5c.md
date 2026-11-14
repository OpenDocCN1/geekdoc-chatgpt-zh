
注意：这里使用了一个小技巧。通过函数 variable_on_cpu 来声明学习参数变量，将所有的学习参数定义在 CPU 的内存中，可以让 GPU 的内存充分地用于运算。

### 3. 定义损失函数即优化器

语音识别是属于非常典型的时间序列分类问题，前面讲过，对于这样的问题要使用 ctc_loss 的方法来计算损失值。优化器还是使用 AdamOptimizer，学习率为 0.001。代码如下。

代码9-23 yuyinchall（续）

```
167 ......
168 #调用 ctc_loss
169 avg_loss = tf.reduce_mean(ctc_ops.ctc_loss(targets, logits, seq_length))
170
171 ##############################################
172 #优化器

174 learning_rate = 0.001
175 optimizer = tf.train.AdamOptimizer(learning_rate=learning_rate).minimize(avg_loss)
```

### 4. 定义解码并评估模型节点

使用 ctc_beam_search_decoder 函数以 CTC 的方式对预测结果 logits 进行解码，生成了 decoded。前面说过，decoded 是一个只有一个元素的数组，所以将其 decoded[0]传入 edit_distance 函数，计算与正确标签 targets 之间的 levenshtein 距离。下列代码第 182 行中的 targets 与 decoded[0]都是稀疏矩阵张量（SparseTensor）类型。对得到的 distance 取 reduce_mean，可以得出该模型对于当前 batch 的平均错误率。

代码9-23 yuyinchall（续）

```
176 ......
177 with tf.name_scope("decode"):
178     decoded, log_prob = ctc_ops.ctc_beam_search_decoder( logits, seq_length, merge_repeated=False)

180 with tf.name_scope("accuracy"):
181     distance = tf.edit_distance( tf.cast(decoded[0], tf.int32),
182     targets)
183     # 计算 label error rate (accuracy)
184 ler = tf.reduce_mean(distance, name='label_error_rate')
```

### 5. 建立session并添加检查点处理

至此模型已经建立好了，剩下的就是训练部分的搭建了。由于样本比较大，运算时间比较长，所以很有必要为模型添加检查点功能。如下代码在 session 建立之前，定义一个类（名为 saver），用于保存检查点的相关操作，并指定检查点文件夹为当前路径下的 log\yuyinchalltest\，然后启动 session，进行初始，同时在指定路径下查找最后一次检查点。如果有文件就载入到模型，同时更新迭代次数 epoch。

代码9-23 yuyinchall（续）

```
184 ......
185 epochs = 100
186 savedir = "log/yuyinchalltest/"
187 saver = tf.train.Saver(max_to_keep=1)       # 生成 saver
188 # 创建 session
189 sess = tf.Session()
190 # 没有模型，就重新初始化
191 sess.run(tf.global_variables_initializer())
192
193 kpt = tf.train.latest_checkpoint(savedir)
194 print("kpt:",kpt)
195 startepo= 0
196 if kpt!=None:
197     saver.restore(sess, kpt)
198     ind = kpt.find("-")
199     startepo = int(kpt[ind+1:])
200 print(startepo)
```

### 6. 通过循环来迭代训练模型

记录下开始时间，启用循环，进行迭代训练，每次循环通过 next_batch 函数取一批次样本数据，并设置 keep_dropout 参数，通过 sess.run 来运行模型的优化器，同时输出 loss 的值。总样本迭代 100 次，每次迭代中，一批次取 8 条数据。

代码9-23 yuyinchall（续）

```
201 ......
202 # 准备运行训练步骤
203 section = '\n{0:=>40}\n'
204 print(section.format('Run training epoch'))
205
206 train_start = time.time()
207 for epoch in range(epochs):        #样本集迭代次数
208     epoch_start = time.time()
209     if epoch<startepo:
210         continue
211
212 print("epoch start:",epoch,"total epochs= ",epochs)
213 ########################运行 batch####################
214     n_batches_per_epoch = int(np.ceil(len(labels) / batch_size))
215     print("total loop ","n_batches_per_epoch,"in one epoch ,",batch_size,"items in one loop")
217     train_cost = 0
218     train_ler = 0
219     next_idx=0
221    for batch in range(n_batches_per_epoch):
    #每次取 batch_size 条数据，共循环 n_batches_per_epoch 次
222    #取数据
223    next_idx,source,source_lengths,sparse_labels = \
224    next_batch(labels,next_idx ,batch_size)
225    feed = {input_tensor: source, targets: sparse_labels,seq_length: source_lengths,keep_dropout:keep_dropout_rate}
227    #计算 avg_loss optimizer
228    batch_cost, _ = sess.run([avg_loss, optimizer], feed_dict=feed )
    train_cost += batch_cost
```

### 7. 定期评估模型，输出模型解码结果

每取 20 次 batch 数据，就将过程信息打印出来，将样本数据送入模型进行语音识别，并输出预测结果。为防止打印信息过多，每次只打印一条信息，并将其文件名、原始的文本和解码文本打印出来。

代码9-23 yuyinchall（续）

```
230 ......
231    if (batch +1)%20 == 0:
232        print('loop:',batch, 'Train cost: ', train_cost/(batch+1))
233        feed2 = {input_tensor: source, targets: sparse_labels,
234    seq_length: source_lengths,keep_dropout:1.0}
235        d,train_ler = sess.run([decoded[0],ler], feed_dict=feed2)
236        dense_decoded = tf.sparse_tensor_to_dense( d, default_value=-1).eval(session=sess)
238        dense_labels = sparse_tuple_to_texts_ch(sparse_labels, words)
240        counter =0
241        print('Label err rate: ', train_ler)
242        for orig, decoded_arr in zip(dense_labels, dense_decoded):
243            # 转成strings
244            decoded_str = ndarray_to_text_ch(decoded_arr,words)
245            print(' file {}'.format( counter))
246            print('Original: {}'.format(orig))
247            print('Decoded: {}'.format(decoded_str))
248            counter=counter+1
249            break
250    epoch_duration = time.time() - epoch_start
251
252    log = 'Epoch {}/{}, train_cost: {:.3f}, train_ler: {:.3f}, time: {:.2f} sec'
254    print(log.format(epoch ,epochs, train_cost,train_ler,epoch_duration))
254    saver.save(sess, savedir+"yuinch.ckpt", global_step=epoch)
255
256 train_duration = time.time() - train_start
257 print('Training complete, total duration: {:.2f} min'.format(train_duration / 60))
258
259 sess.close()
```

通过 sess.run 计算 decoded[0]的值只是个 SparseTensor 类型，需要用 tf.sparse_tensor_to_dense 将其转成 dense 矩阵（记住 TensorFlow 里的类型必须用 eval 或 session.run 才能得到真实值），然后再调用 sparse_tuple_to_texts_ch 将其转成文本 dense_labels。

在每次迭代的最后加入检查点保存代码，以便中途中断可以恢复。

运行以上代码，经过一段时间之后（十几小时或几十个小时），会得到如下输出：

```
.....
file 0
Original: 另外 加工 修理 和 修配 业务 不 属于 营业税 的 应 税 劳务 不 缴纳 营业税
Decoded: 另外 加工 理 和 修配 务 不 属于 营业税 的 应 税 劳务 不 缴纳 营业税
loop: 79 Train cost: 10.3595850527
Label err rate: 0.0189385
file 0
Original: 这 碗 离 娘 饭 姑娘 再有 离 娘 痛楚 也 要 每样 都 吃 一点 才 算 循 规 蹈 俗 的
Decoded: 这 碗 离 娘 饭 姑 有 离 娘 痛楚 也 要 每样 都 吃 一点 才 外 算 循 规 蹈 俗 的
loop: 99 Train cost: 10.3084330273
Label err rate: 0.0270463
file 0
Epoch 99/100, train_cost: 1176.815, train_ler: 0.047, time: 706.20 sec
WARNING:tensorflow:Error encountered when serializing LAYER_NAME_UIDS.
Type is unsupported, or the types of the items don't match field type in CollectionDef.
'dict' object has no attribute 'name'
Training complete, total duration: 1182.50 min
```

由此可见程序基本可以将样本库中的语音全部识别出来，错误率在 0.02 左右。最后打印的警告是由于 TensorFlow 中保存模型节点时的，不影响整体功能，可以不用管。

一般来讲，将训练好的模型作为识别后端，通过编写程序录音采集，将 WAV 文件传入进行解码，即可实现在线实时的语音识别了。

## 9.5.4 练习题

- (1) 试着按照前面例子中的样本形式，自己录制一些语音样本，并做出对应的文本文件，用该代码训练出适应自己声音的语音模型。
- (2) 实例 68 中的模型里，fc5 层使用的是全连接的方法，还可以使用全局平均池化层的方式代替，读者可以试着改写一下代码，看看效果。
- (3) 尝试在 BIRNN 部分加入更深的 RNN 层，来获得更好的识别率。

## 9.6 实例 69: 利用 RNN 训练语言模型

下面来做一个实验，用 RNN 预测语言模型，并让它输出一句话，具体业务描述如下。先让 RNN 学习一段文字，之后模型可以根据我们的输入再自动预测后面的文字。同时将模型预测出来的文字当成输入，再放到模型里，模型就会预测出下一个文字，这样循环下去，可以看到 RNN 能够输出一句话。

那么 RNN 是怎么样来学习这段文字呢？这里将整段文字都看成一个个的序列。在模型里预设值只关注连续的 4 个序列，这样在整段文字中，每次随意拿出 4 个连续的文字放到模型里进行训练，然后把第 5 个连续的值当成标签，与输出的预测值进行 loss 的计算，形成一个可训练的模型，通过优化器来迭代训练。

### 实例描述

通过让 RNN 网络对一段文字的训练学习来生成模型，最终可以使用机器生成的模型来表达自己的意思。

下面看看具体实现过程。

## 9.6.1 准备样本

这个环节很简单，随便复制一段话放到 txt 里即可。在例子中使用的样本如下：

> 在尘世的纷扰中，只要心头悬挂着远方的灯光，我们就会坚持不懈地走，理想为我们灌注了精神的蕴藉。所以，生活再平凡、再普通、再琐碎，我们都要坚持一种信念，默守一种精神，为自己积淀站立的信心，前行的气力。

这是笔者随意下载的一段文字，把该段文字放到代码同级目录下，起名为 wordstest.txt。

### 1. 定义基本工具函数

具体的基本工具函数与语音识别例子差不多，都是与文本处理相关的，首先引入头文件，然后定义相关函数，其中 get_ch_lable 函数从文件里获取文本，get_ch_lable_v 函数将文本数组转换成向量。具体如下。

代码9-25 rnnwordtest

```
01 import numpy as np
02 import tensorflow as tf
03 from tensorflow.contrib import rnn
04 import random
05 import time
06 from collections import Counter
07 start_time = time.time()
08 def elapsed(sec):
09     if sec<60:
10         return str(sec) + " sec"
11     elif sec<(60*60):
12         return str(sec/60) + " min"
13     else:
14         return str(sec/(60*60)) + " hr"
15 
16 tf.reset_default_graph()
17 training_file = 'wordtest.txt'

19 #处理多个中文文件
20 def readalltxt(txt_files):
21     labels = []
22     for txt_file in txt_files:

24         target = get_ch_lable(txt_file)
25         labels.append(target)
26     return labels

28 #处理汉字
29 def get_ch_lable(txt_file):
30     labels= ""
31     with open(txt_file, 'rb') as f:
32         for label in f:

34         labels = labels +label.decode( 'gb2312')

36     return labels

38 #优先转文件里的字符到向量
39 def get_ch_lable_v(txt_file,word_num_map,txt_label=None):

41     words_size = len(word_num_map)
42     to_num = lambda word: word_num_map.get(word, words_size)
43     if txt_file!= None:
44         txt_label = get_ch_lable(txt_file)

46     labels_vector = list(map(to_num, txt_label))
47     return labels_vector
```

### 2. 样本预处理

样本预处理工作主要是读取整体样本，并存放到 training_data 里，获取全部的字表 words，并生成样本向量 wordlabel 和与向量对应关系的 word_num_map。具体代码如下。

# 代码9-25 rnnwordtest (续)

```
48 training_data =get_ch_lable(training_file)
49 print("Loaded training_data...")
50 
51 counter = Counter(training_data)
52 words = sorted(counter)
53 words_size= len(words)
```
