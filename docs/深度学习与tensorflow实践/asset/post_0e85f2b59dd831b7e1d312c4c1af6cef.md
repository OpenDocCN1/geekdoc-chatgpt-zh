
class自定义模型类名（继承tf.keras.Model）：

构造函数__init__() 初始化模型所需的层
网络层1 网络层2 网络层3

模型调用方法call() 描述数据流动的逻辑拓扑结构，即数据如何通过网络层而得到输出
输入1 网络层1 网络层3 输出
输入2 网络层2

### 图 8-31 自定义 Keras 模型的类构造示意图

下面我们来说明是如何实现自定义模型 MyRNN 的。首先我们使用简易版本的 RNN，这种神经单元在 Keras 中就称为 SimpleRNN（ ）。

```
In [14]:

14 from tensorflow.keras import layers, losses, optimizers, Sequential
15 embedding_len = 100
16 class MyRNN(keras.Model):
17     def __init__(self, units):
18         super(MyRNN, self).__init__()
19         # 嵌入层的维度变化: [b, 80] => [b, 80, 100]
20         self.embedding = layers.Embedding(total_words, embedding_len,
21                                          input_length = max_review_len)
22         # 循环神经网络部分的维度: [b, 80, 100] , h_dim: 64
23         self.rnn = keras.Sequential([
24             layers.SimpleRNN(units, dropout=0.5,   #隐含层 1
25                              return_sequences=True, unroll = True),
26             layers.SimpleRNN(units, dropout=0.5, unroll= True) #隐含层 2
27         ])
28         # 全连接层的维度变化: [b, 80, 100] => [b, 64] => [b, 1]
29         self.outlayer = layers.Dense(1)   #输出层（全连接层）

30     def call(self, inputs, training=None):
31         # 输入张量的维度: [b, 80]
32         x = inputs
33         # 嵌入层向量的维度变化: [b, 80] => [b, 80, 100]
34         x = self.embedding(x)      #嵌入层，可视为预处理之后的输入层
35         # RNN 神经元计算
36         # 张量的维度变化：[b, 80, 100] => [b, 64]
37         x = self.rnn(x)            #RNN 层，含 2 个隐含层
38         # 输出张量的维度变化：[b, 64] => [b, 1]
39         x = self.outlayer(x)       #输出层
40         # 二分类，用 sigmoid 模拟概率
41         prob = tf.sigmoid(x)       #输出归一化处理
42         return prob
```

简单解释一下上述代码。类 MyRNN 就是按如图 8-31 所示的结构建立的。第 16 行括号内的 keras.Model 表明了继承关系，keras.Model 是父类。然后，先重写了构造器方法__init__()。由于父类的变量初始化需要父类自己完成，因此先需要调用父类的构造器（第 18 行）。在这个子类的构造器中，我们先构造一个词向量的嵌入层，然后用序贯模式定义一个复合网络层，它包含两个简易 RNN 层。最后，由于该神经网络是用于分类的，因此需要对接一个全连接层。对于一个二分类而言（输出概率>0.5 表示正类，反之表示负类），输出层只需要一个神经元即可（第 28 行）。

对高度封装的 Keras 而言，我们只需要宏观的了解各种层的调用 API，神经元内部的计算就交给 Keras 来协调和处理（通常交由 TensorFlow 和 Caffe 等后端来做幕后工作）。自然，如果你对循环神经网络的原理熟稔，那么也可手动构造各个层（参看本章习题部分）。

在构造器中构造完各个层后，我们要做的工作就是在 call()方法中，定义各个层的数据流动。我们可以看到数据（x）经过了不同层的加工。

在网络模型搭建完毕后，下面的工作就比较常规了，与前面讲到的范例处理流程没有区别，即模型编译（配置各种模型参数）、模型训练和模型评估。

```
In [15]:

def main():
    units = 64      # RNN 状态向量长度
    epochs = 10     # 训练 epochs
    training = True # 设定训练模型
    model = MyRNN(units)
    if training:
        model.compile(optimizer = optimizers.RMSprop(0.001),
                      loss = losses.BinaryCrossentropy(),
                      metrics=['accuracy'],
                      experimental_run_tf_function = False)
    else:
        model.compile(optimizer = optimizers.RMSprop(0.001),
                      loss = losses.BinaryCrossentropy(),
                      metrics=['accuracy'])
# 训练和验证
model.fit(db_train, epochs=epochs, validation_data= db_test)
# 模型测试
model.evaluate(db_test)
if __name__ == '__main__':
    main()
```

### 运行结果

```
Epoch 1/10
193/195 [============================>..] - ETA: 0s - loss: 0.6286 - accuracy: 0.5446
Epoch 1/10
195/195 [==============================] - 17s 87ms/step - loss: 0.6276 - accuracy: 0.5459 - val_loss: 0.4794 - val_accuracy: 0.7704
...(省略大部分输出)
Epoch 10/10
195/195 [==============================] - 2s 10ms/step - loss: 0.7337 - accuracy: 0.8121
```

### 代码分析

从输出可以看到，经过 10 轮训练，基于简易 RNN 的情感分类准确率在 81%左右。在代码层面上，我们来说明这个训练方法有一个值得注意的细节。我们知道，模型的训练和训练后的模型使用，二者的逻辑是有差别的。例如，Dropout 机制，在模型训练阶段，需要随机失活一定比例的神经元（本范例占比 50%，见代码第 23 行和第 25 行）。但模型训练完毕后，为了提高模型的表达能力，进而提高模型预测的准确率，我们希望这些神经元“全员上阵”。

因此，Keras 模型分为两个模型。若是训练模式，则需要在模型编译（装配模型参数）时，显式设置 experimental_run_tf_function = False；否则模型训练过程会报错。

## 8.12.5 基于 LSTM 的优化

如前所述，简易 RNN 容易出现梯度弥散或梯度爆炸。有个直观的想法就是，如果我们利用更为高阶的模型 LSTM，性能是不是会好一些？在范例 8-2 中，我们将测试这个想法。

由于范例 8-2 仅在定义模型的类 MyRNN 有所不同，其他代码完全雷同于范例 8-1，所以下面仅给出这个类的代码。

### 范例 8-2 利用 LSTM 实施文本情绪分类（lstm.py）

```
01 class MyRNN(keras.Model):
02     def __init__(self, units):
03         super(MyRNN, self).__init__()
04         self.embedding = layers.Embedding(total_words, embedding_len,
05             input_length = max_review_len)
06         self.rnn = keras.Sequential([
07             layers.LSTM(units, dropout=0.5, return_sequences=True, unroll=True),
08             layers.LSTM(units, dropout=0.5, unroll=True)
09         ])
10         self.outlayer = layers.Dense(1)

11     def call(self, inputs, training: bool = True):
12         x = inputs
13         x = self.embedding(x)
14         x = self.rnn(x)
15         x = self.outlayer(x)
16         prob = tf.sigmoid(x)
17         return prob
```

### 运行结果

```
......（省略部分输出）
Epoch 10/10
194/195 [==============================>..] - ETA: 0s - loss: 0.1897 - accuracy: 0.9269
Epoch 1/10
195/195 [==============================] - 6s 31ms/step - loss: 0.4479 - accuracy: 0.8372
195/195 [==============================] - 21s 107ms/step - loss: 0.1895 - accuracy: 0.9269 - val_loss: 0.4479 - val_accuracy: 0.8372
195/195 [==============================] - 6s 29ms/step - loss: 0.4479 - accuracy: 0.8372
```

### 代码分析

从输出结果看，分类准确率从简易 RNN 的 81%，提升到了 LSTM 的 83.7%。而我们所做的仅仅是将第 07 ~ 08 行，由范例 8-1 的 layers.SimpleRNN 换成范例 8-2 的 layers.LSTM，即将两个 RNN 神经元换成了 LSTM 神经元。

由于 Keras 模块提供的高度封装性，让我们无须关注 LSTM 的实现细节。如此小小的变动，换来大于 2%性能的提升，还是非常可取的。

## 8.12.6 基于 GRU 的优化

通过前面的学习可知，GRU 是 LSTM 的又一个升级版本。我们可能会有这样的想法，若将 LSTM 神经元换成 GRU 神经元，则虽然分类准确率稍有降低，但运行时间缩短了，有时候也是值得考虑的。

类似的原因，由于范例 8-3 仅在定义模型的那个类 MyRNN 有所不同（就两行代码有差异），其他代码完全雷同于范例 8-2，所以下面仅给出这个类的代码。

### 范例 8-3 利用 GRU 进行优化（gru.py）

```
01 class MyRNN(keras.Model):
02     def __init__(self, units):
03         super(MyRNN, self).__init__()
04         self.embedding = layers.Embedding(total_words, embedding_len,
05             input_length=max_review_len)
06         self.rnn = keras.Sequential([
07             layers.GRU(units, dropout=0.5, return_sequences=True, unroll=True),
08             layers.GRU(units, dropout=0.5, unroll=True)
09         ])
10         self.outlayer = layers.Dense(1)

11     def call(self, inputs, training: bool = True):
12         x = inputs
13         x = self.embedding(x)
14         x = self.rnn(x)
15         x = self.outlayer(x)
16         prob = tf.sigmoid(x)
17         return prob
```

### 运行结果

```
.....（省略部分输出）
194/195 [==============================>..] - ETA: 0s - loss: 0.1922 - accuracy: 0.9265
Epoch 1/10
195/195 [==============================] - 5s 25ms/step - loss: 0.4525 - accuracy: 0.8356
195/195 [==============================] - 20s 100ms/step - loss: 0.1917 - accuracy: 0.9265 - val_loss: 0.4525 - val_accuracy: 0.8356
195/195 [==============================] - 5s 24ms/step - loss: 0.4525 - accuracy: 0.8356
```

### 代码分析

在代码层面，仅是第 07 ~ 08 行代码与范例 8-2 有所不同。二者的差别就在于，把两个 LSTM 神经元换成了两个 GRU 神经元。从上述几个例子可以感知到，利用高度封装的 Keras 更换模型，就如同更换汽车上的零部件一般，简单易行。

从运行结果可以看到，本范例的分类准确率与范例 8-2 的分类准确率基本一致。那 GRU 号称的节省计算资源呢？是不是节省了计算时间呢？我们可以利用两个简单的 time()时间戳差值即可完成二者的比较。我们在训练代码添加 4 行代码即可。

```
01 def main():
02     units = 64 # RNN 状态向量长度
03     epochs = 10 # 训练 epochs
04     training = True
05
06     import time
07     t0 = time.time()
08
09     model = MyRNN(units)
10     if training:
11         model.compile(optimizer = optimizers.RMSprop(0.001),
12             loss = losses.BinaryCrossentropy(),
13             metrics = ['accuracy'],
14             experimental_run_tf_function = False)
15     else:
16         model.compile(optimizer = optimizers.RMSprop(0.001),
17             loss = losses.BinaryCrossentropy(),
18             metrics = ['accuracy'])
19     model.fit(db_train, epochs = epochs, validation_data = db_test)
20     model.evaluate(db_test)
21
22     t1 = time.time()
23     print("运行的时间为: {0}秒".format(t1 - t0))
24
25 if __name__ == '__main__':
26     main()
```

上述代码仅在第 06 ~ 07 行导入 time 模块，并添加运行前的时间戳，第 22 行添加运行后的时间戳，第 23 行输出运行时间，这个修改适用于上述的三个范例。

我们在如下环境（MacBook Pro、2.9 GHz 六核 Intel Core i9、32 GB 2400 MHz DDR4）下做了简单测试：GRU 耗时 168.53s，LSTM 耗时 190.68s。实验发现，GRU 在性能（分类准确率）近似的情况下，相比 LSTM 耗时较少，符合实验预期。

此外，如果我们熟稔于算法和编程，那么可以尝试使用 NumPy 手工打造各个网络层的实现，有时获得性能的提升也是客观的。因为使用深度学习框架的好处在于简便，劣势在于，计算框架照顾的是大多数场景，其各种参数的配置未必是最优的，而在计算框架裹挟之下，我们对框架的内部实现通常是无法“染指”的。正所谓“凡事有利就有弊”。

最后，从测试的时长可以看到，仅利用 CPU 来训练深度学习模型，由于是串行训练，在模型并不复杂的情况下，因此训练速度还是比较慢的。有条件的读者，可以尝试使用 GPU 来训练深度学习模型。由于 GPU 通常有成千上万个 CUDA core，在高并发线程的加持下，预期的训练速度会快很多。

训练深度学习的模型，非常耗时。为了加速训练流程，人们先后使用了多核 CPU（中央处理器）、众核 GPU（图形处理器）、TPU（张量处理器）和 NPU（Neural network Processing Unit，神经网络处理器）。NPU 在电路层模拟人类神经元和突触，并利用专用指令集直接处理大规模的神经元权值更新，大幅提高了计算效率并降低了能耗。

## 8.13 本章小结

在本章，我们学习了循环神经网络（RNN），其最大的特点是，网络的输出结果不仅与当前的输入相关，还与过往的输出相关。由于利用了历史信息，当任务涉及时序或与上下文相关时（如语音识别、自然语言处理等），RNN 要比其他人工神经网络（如 CNN）的性能好很多。

但读者需要注意如下两点：

- （1）RNN 中的“深度”，不同于传统的深度神经网络，它主要是指时间和空间（如网络中的隐含层个数）特性上的深度。
- （2）通过对第 7 章的学习，我们知道传统 CNN（卷积神经网络）的主要特点是“局部连接”“权值共享”和“局部平移不变性”，其中“权值共享”意味着“计算共享”，它节省了大量计算开销。而 RNN 则不同，它是随着“时间”深度的加深，通过对参数实施“平流移植”来实现“计算共享”的。

由于传统的 RNN 存在梯度弥散或梯度爆炸，导致第一代 RNN 很难将神经网络层数提上去，因此其表征能力非常有限，应用性能上也有所欠缺。于是，胡伯提出了 LSTM，通过改造神经元，添加了遗忘门、输入门和输出门等结构，让梯度能够长时间地在路径上流动，从而有效提升了深度 RNN 的性能。

LSTM 对历史信息具有良好的记忆能力，这个特征非常适用于自然语言处理，因此，接下来，我们先简单介绍了自然语言处理的词向量表示
