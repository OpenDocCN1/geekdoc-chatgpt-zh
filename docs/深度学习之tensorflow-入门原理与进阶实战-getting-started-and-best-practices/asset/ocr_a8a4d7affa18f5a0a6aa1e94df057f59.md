```
102
103 # 注意防止越界
104 data_index = (data_index + len(data) - span) % len(data)
105 return batch, labels
106
107 batch, labels = generate_batch(training_label,batch_size=8, num_
    skips=2, skip_window=1)
108
109 for i in range(8):# 先循环 8 次，然后将组合好的样本与标签打印出来
110 print(batch[i], words[batch[i]], '->', labels[i, 0], words[labels[i,
    0]])
```

generate_batch 函数中使用 CBOW 模型来构建样本，是从开始位置的一个一个字作为输入，然后将其前面和后面的字作为标签，再分别组合在一起变成 2 组数据。运行当前代码，输出如下：

```
31 人体 -> 38 与
31 人体 -> 263 阴阳
38 与 -> 31 人体
38 与 -> 30 电能
30 电能 -> 27 阴
30 电能 -> 38 与
27 阴 -> 0 UNK
27 阴 -> 30 电能
```

如果是 Skip-Gram 方法，根据字取标签的方法正好相反，输出会变成以下这样：

```
263 阴阳 -> 31 人体
38 与 -> 31 人体
31 人体 -> 38 与
31 人体 -> 263 阴阳
30 电能 -> 38 与
......
```

# 4. 定义取样参数

下面代码中每批次取 128 个，每个词向量的维度为 128，前后取词窗口为 1，num_skips 表示一个 input 生成 2 个标签，nce 中负采样的个数为 num_sampled。接下来是验证模型的相关参数，valid_size 表示在 0- words_size/2 中的数取随机不能重复的 16 个字来验证模型。

# 代码9-26 word2vect（续）

```
111 batch_size = 128
112 embedding_size = 128          # embedding vector 的维度
113 skip_window = 1               # 左右的词数量
114 num_skips = 2                 # 一个 input 生成 2 个标签
115
116 valid_size = 16
117 valid_window = words_size/2   # 取样数据的分布范围
```