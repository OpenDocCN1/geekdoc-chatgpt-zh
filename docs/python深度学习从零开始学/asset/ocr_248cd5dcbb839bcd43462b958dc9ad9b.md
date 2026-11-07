这些开关用门（gate）来实现，在现实生活中，门就是用来控制进出的，关上门，我们就进不去房子，打开门我们就能进去。同理，LSTM 的门用来控制每一时刻信息的记忆与遗忘。门实际上就是一层全连接层，输入是一个向量，输出是一个 0 到 1 之间的实数向量。门如何进行控制？方法是 用门的输出向量按元素乘以我们需要控制的那个向量，其原理是：门的输出是 0 到 1 之间的实数向量，当门输出为 0 时，任何向量与之相乘都会得到 0 向量，这就相当于什么都不能通过；输出为 1 时，任何向量与之相乘都不会有任何改变，这就相当于什么都可以通过。

# 9.4.2 使用 LTSM 模型进行影评情感分析

完整的程序代码参考范例 keras_imdb_lstm.ipynb，这里摘出使用 LTSM 模型的代码，其他代码如下载 IMDB 数据集、数据预处理等都与之前的模型相同。

```
from keras.models import Sequential
from keras.layers.core import Dense, Dropout, Activation
from keras.layers.embeddings import Embedding
from keras.layers.recurrent import LSTM
model = Sequential()
model.add(Embedding(output_dim=32,
                    input_dim=3800,
                    input_length=380))
model.add(Dropout(0.2))
model.add(LSTM(units=32))
model.add(Dense(units=256,activation='relu'))
model.add(Dropout(0.2))
model.add(Dense(units=1,activation='sigmoid'))
```

以上代码使用 LTSM(32) 建立具有 32 个神经元的 LTSM 层，使用 print(model.summary) 指令查看模型，如图 9-17 所示。

| Layer (type) | Output Shape | Param # |
|---|---|---|
| embedding_1 (Embedding) | (None, 380, 32) | 121600 |
| dropout_1 (Dropout) | (None, 380, 32) | 0 |
| lstm_1 (LSTM) | (None, 32) | 8320 |
| dense_1 (Dense) | (None, 256) | 8448 |
| dropout_2 (Dropout) | (None, 256) | 0 |
| dense_2 (Dense) | (None, 1) | 257 |
| Total params: 138,625 |  |  |
| Trainable params: 138,625 |  |  |
| Non-trainable params: 0 |  |  |

图 9-17

训练模型，评估模型的准确率，我们会发现使用 LSTM 模型之后，模型准确率得以提高。多层感知器模型运用于文本情感分析时，其效果不如 RNN 和 LSTM 模型的效果好。