
```
In [35]: import tensorflow as tf
         tf.random.set_seed(100)

In [36]: from keras.layers import Dense
         from keras.models import Sequential
         Using TensorFlow backend.

In [37]: model = Sequential()  ➊
         model.add(Dense(256, activation='relu', input_dim=1))  ➋
         model.add(Dense(1, activation='linear'))  ➌
         model.compile(loss='mse', optimizer='rmsprop')  ➍

In [38]: ((y - y_) ** 2).mean()
Out[38]: 0.021662355744355866

In [39]: plt.figure(figsize=(10, 6))
         plt.plot(x, y, 'ro', label='sample data')
         for _ in range(1, 6):
         model.fit(x, y, epochs=100, verbose=False)  ➎
         y_ =  model.predict(x)  ➏
         MSE = ((y - y_.flatten()) ** 2).mean()  ➐
         print(f'round={_} | MSE={MSE:.5f}')
         plt.plot(x, y_, '--', label=f'round={_}')  ➑
     plt.legend();
     round=1 | MSE=3.09714
     round=2 | MSE=0.75603
     round=3 | MSE=0.22814
     round=4 | MSE=0.11861
     round=5 | MSE=0.09029
```
➊ 实例化 `Sequential` 模型对象。
➋ 添加采用整流线性单元（`ReLU`）激活函数的全连接层作为隐藏层。
➌ 添加 `linear` 激活的输出层。
➍ 编译模型对象。
➎ 迭代训练神经网络指定次数。
➏ 预测近似值。
➐ 计算当前的 `MSE`。
➑ 绘制当前的近似结果。

![{%}](Images/image00346.jpeg)

**图 1-6：样本数据和多轮训练后得到的估计值**

粗略地讲，神经网络提供了与 OLS 回归几乎一样完美的估计性能，那么为什么还要使用神经网络呢？本书稍后会提供更全面的解答，但这里我们可以通过一个不同的示例得到些许提示。

假设我们的数据不是通过预定义好的数学函数生成的，而是随机产生的特征和标签。当然，该示例仅用于说明，并不具有解释性。

以下代码会生成随机样本数据集，并创建不同的多次项 OLS 回归进行逼近。从图 1-7 显示的结果可以看出，即使是最高次项的 OLS 回归，逼近结果仍然不是很好，`MSE` 值相对较高。

```
In [40]: np.random.seed(0)
         x = np.linspace(-1, 1)
         y = np.random.random(len(x)) * 2 - 1

In [41]: plt.figure(figsize=(10, 6))
         plt.plot(x, y, 'ro', label='sample data')
         for deg in [1, 5, 9, 11, 13, 15]:
             reg = np.polyfit(x, y, deg=deg)
             y_ = np.polyval(reg, x)
             MSE = ((y - y_) ** 2).mean()
             print(f'deg={deg:2d} | MSE={MSE:.5f}')
             plt.plot(x, np.polyval(reg, x), label=f'deg={deg}')
         plt.legend();
         deg= 1 | MSE=0.28153
         deg= 5 | MSE=0.27331
         deg= 9 | MSE=0.25442
         deg=11 | MSE=0.23458
         deg=13 | MSE=0.22989
         deg=15 | MSE=0.21672
```

![{%}](Images/image00347.jpeg)

**图 1-7：随机样本数据和 OLS 回归线**

不出所料，OLS 回归的效果并不理想。OLS 回归假设我们可以通过有限个（基于多项式的）基函数的组合来逼近目标函数。由于样本数据集是随机生成的，因此在这种情况下，OLS 回归效果不佳。

神经网络表现如何呢？它使用起来仍然像前面的示例一样简单，逼近的结果如图 1-8 所示。

![{%}](Images/image00348.jpeg)

**图 1-8：随机样本数据和神经网络估计**

尽管预测结果并不完美，但很明显，在根据随机特征值估计随机标签值时，神经网络的性能优于 OLS 回归。然而，神经网络架构有近 200 000 个可训练的参数（权重），这提供了相对较高的灵活性，尤其是与 OLS 回归（最多使用 15+1 个参数）相比。

```
In [42]: model = Sequential()
         model.add(Dense(256, activation='relu', input_dim=1))
         for _ in range(3):
             model.add(Dense(256, activation='relu'))  ➊
         model.add(Dense(1, activation='linear'))
         model.compile(loss='mse', optimizer='rmsprop')

In [43]: model.summary()  ➋
         Model: "sequential_2"
         _________________________________________________________________
         Layer (type)                  Output Shape             Param #
         =================================================================
         dense_3 (Dense)               (None, 256)              512
         _________________________________________________________________
         dense_4 (Dense)               (None, 256)              65792
         _________________________________________________________________
         dense_5 (Dense)               (None, 256)              65792
         _________________________________________________________________
         dense_6 (Dense)               (None, 256)              65792
         _________________________________________________________________
         dense_7 (Dense)               (None, 1)                 257
         =================================================================
         Total params: 198,145
         Trainable params: 198,145
         Non-trainable params: 0
         _________________________________________________________________

In [44]: %%time
         plt.figure(figsize=(10, 6))
         plt.plot(x, y, 'ro', label='sample data')
         for _ in range(1, 8):
             model.fit(x, y, epochs=500, verbose=False)
             y_ =  model.predict(x)
             MSE = ((y - y_.flatten()) ** 2).mean()
             print(f'round={_} | MSE={MSE:.5f}')
             plt.plot(x, y_, '--', label=f'round={_}')
         plt.legend();
         round=1 | MSE=0.13560
         round=2 | MSE=0.08337
         round=3 | MSE=0.06281
         round=4 | MSE=0.04419
         round=5 | MSE=0.03329
         round=6 | MSE=0.07676
         round=7 | MSE=0.00431
         CPU times: user 30.4 s, sys: 4.7 s, total: 35.1 s
         Wall time: 13.6 s
```
➊ 添加多个隐藏层。
➋ 显示神经网络架构以及可训练参数的数量。

4有关详细信息，请参阅 `sklearn.neural_network.MLPRegressor`。如需了解更多背景信息，请参阅 Goodfellow 等（2016）的第 6 章。

5有关详细信息，请参阅 Chollet（2017）的第 3 章。

### 1.2.3　神经网络分类

神经网络也可以很容易地用于分类任务。考虑以下基于 `Keras` 实现神经网络分类的 Python 代码。二元特征数据和二元标签数据是随机生成的。建模方面的主要调整是将输出层的激活函数从 `linear` 更改为 `sigmoid`，后面的章节会对此作更详细的介绍。虽然分类效果并不完美，但是也达到了很高的准确率。图 1-9 显示了准确率（表示为正确结果与所有标签值之间的关系）如何随着训练轮数而变化。准确率一开始很低，然后逐步提高，尽管不一定每一步都会有所提高。

```
In [45]: f = 5
         n = 10

In [46]: np.random.seed(100)

In [47]: x = np.random.randint(0, 2, (n, f))  ➊
         x  ➊
Out[47]: array([[0, 0, 1, 1, 1],
                [1, 0, 0, 0, 0],
                [0, 1, 0, 0, 0],
                [0, 1, 0, 0, 1],
                [0, 1, 0, 0, 0],
                [1, 1, 1, 0, 0],
                [1, 0, 0, 1, 1],
                [1, 1, 1, 0, 0],
                [1, 1, 1, 1, 1],
                [1, 1, 1, 0, 1]])

In [48]: y = np.random.randint(0, 2, n)  ➋
         y  ➋
Out[48]: array([1, 1, 0, 0, 1, 1, 0, 1, 0, 1])

In [49]: model = Sequential()
         model.add(Dense(256, activation='relu', input_dim=f))
         model.add(Dense(1, activation='sigmoid'))  ➌
         model.compile(loss='binary_crossentropy', optimizer='rmsprop',
                      metrics=['acc'])  ➍

In [50]: model.fit(x, y, epochs=50, verbose=False)
Out[50]: <keras.callbacks.callbacks.History at 0x7fde09dd1cd0>

In [51]: y_ = np.where(model.predict(x).flatten() > 0.5, 1, 0)
         y_
Out[51]: array([1, 1, 0, 0, 0, 1, 0, 1, 0, 1], dtype=int32)

In [52]: y == y_  ➎
Out[52]: array([ True,  True,  True,  True, False,  True,  True,  True,  True,
                 True])

In [53]: res = pd.DataFrame(model.history.history)  ➏

In [54]: res.plot(figsize=(10, 6));  ➏
```
➊ 创建随机特征数据。
➋ 创建随机标签数据。
➌ 定义输出层的激活函数为 `sigmoid`。
➍ 定义损失函数为 `binary_crossentropy`。
6**损失函数**用来计算和衡量神经网络（或其他机器学习算法）的预测错误。**二元交叉熵**（binary crossentropy）适用于二元分类问题的损失函数，而 `MSE` 则适用于估计问题。
➎ 将预测值与标签数据进行比较。
➏ 绘制每轮训练的损失函数和准确率值。

![{%}](Images/image00349.jpeg)

**图 1-9：分类准确率及损失与训练轮数的关系**

本节中的示例说明了与 OLS 回归相比，神经网络的一些基本特征。

**问题无关性**

在给定一组特征值的情况下，神经网络方法的性能与需要估计或者分类的具体标签值是无关的。而统计方法（比如 OLS 回归）可能对较小的一组问题表现良好，对其他问题则表现不太好或根本没有效果。

**增量学习**

给定一个用来度量成功的目标，神经网络中的最佳权重是基于随机初始化和增量改进而逐步学习得到的。这些增量改进是在考虑预测值和样本标签值之间的差异后，通过神经网络反向传播权重更新来实现的。

**通用函数逼近器**

有严格的数学定理表明神经网络（即使只有一个隐藏层）几乎可以逼近任何函数。7

7参阅 Kratsios（2019）。

> ![](Images/image00322.jpeg)　**神经网络**
>
> 神经网络擅长学习输入数据和输出数据之间的关系。它们可以应用于许多问题类型，比如在存在复杂关系的情况下进行估计，或者用于传统的统计方法并不适合的分类问题。

## 1.3　数据的重要性

上一节末尾的例子表明神经网络能够很好地解决分类问题。具有一个隐藏层的神经网络在给定的数据集或**样本内**达到了高度的准确率。然而，神经网络的预测能力如何呢？这在很大程度上取决于可用于训练神经网络的数据量和种类。另一个基于更大数据集的数值示例将说明这一点。

### 1.3.1　小数据集
