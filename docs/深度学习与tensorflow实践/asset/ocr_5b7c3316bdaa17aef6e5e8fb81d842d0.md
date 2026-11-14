```
x = self.embedding(x)      #嵌入层，可视为预处理之后的输入层
# RNN 神经元计算
# 张量的维度变化：[b, 80, 100] => [b, 64]
x = self.rnn(x)            #RNN 层，含 2 个隐含层
# 输出张量的维度变化：[b, 64] => [b, 1]
x = self.outlayer(x)       #输出层
# 二分类，用 sigmoid 模拟概率
prob = tf.sigmoid(x)       #输出归一化处理
return prob
```

简单解释一下上述代码。类 MyRNN 就是按如图 8-31 所示的结构建立的。第 16 行括号内的 keras.Model 表明了继承关系，keras.Model 是父类。然后，先重写了构造器方法__init__()。由于父类的变量初始化需要父类自己完成，因此先需要调用父类的构造器（第 18 行）。在这个子类的构造器中，我们先构造一个词向量的嵌入层，然后用续贯模式定义一个复合网络层，它包含两个简易 RNN 层。最后，由于该神经网络是用于分类的，因此需要对接一个全连接层。对于一个二分类而言（输出概率>0.5 表示正类，反之表示负类），输出层只需要一个神经元即可（第 28 行）。

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
```