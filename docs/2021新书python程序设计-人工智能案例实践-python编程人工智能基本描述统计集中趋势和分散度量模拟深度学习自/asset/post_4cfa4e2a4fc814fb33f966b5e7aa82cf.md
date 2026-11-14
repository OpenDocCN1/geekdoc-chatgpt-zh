
```python
[16]: words_per_review = 200
[17]: from tensorflow.keras.preprocessing.sequence import pad_sequences
[18]: X_train = pad_sequences(X_train, maxlen=words_per_review)
```

如果样本有更多的特征，则`pad_sequences`将其截断为指定的长度。如果样本具有较少的特征，则`pad_sequences`将0添加到序列的开头以将其填充到指定的长度。下面来确认`X_train`的新形式：

```python
[19]: X_train.shape
[19]: (25000, 200)
```

我们在评估模型时，还必须重塑`X_test`以便稍后评价模型：

```python
[20]: X_test = pad_sequences(X_test, maxlen=words_per_review)
[21]: X_test.shape
[21]: (25000, 200)
```

### 将测试数据拆分为验证数据和测试数据

在我们的网络中，我们使用了`fit`方法的`validation_split`参数来指示留出10%的训练数据，以便在训练时验证模型。对于此示例，我们手动将25,000个测试样本分成20,000个测试样本和5,000个验证样本。然后，通过参数`validation_data`将5,000个验证样本传递给模型的`fit`方法。下面使用前一章中`scikit-learn`的`train_test_split`函数来拆分测试集：

```python
[22]: from sklearn.model_selection import train_test_split
X_test, X_val, y_test, y_val = train_test_split(
X_test, y_test, random_state=11, test_size=0.20)
```

下面通过检查`X_test`和`X_val`的形式来确认拆分结果：

```python
[23]: X_test.shape
[23]: (20000, 200)
[24]: X_val.shape
[24]: (5000, 200)
```

## 15.9.4 创建神经网络

接下来，配置RNN，再次从一个Sequential模型开始，在其上添加构成我们网络的各层：

```python
[25]: from tensorflow.keras.models import Sequential
[26]: rnn = Sequential()
```

接下来，导入将在此模型中使用的层：

```python
[27]: from tensorflow.keras.layers import Dense, LSTM
[28]: from tensorflow.keras.layers.embeddings import Embedding
```

### 添加嵌入层

之前使用独热编码将MNIST数据集的整数标签转换为分类数据，每个标签的结果是一个向量，其中除了一个 "1" 元素之外的所有元素都是 "0" 。我们也可以针对表示单词的索引值执行此操作，但是，此示例处理10,000个单词，这意味着我们需要一个10,000×10,000的数组来代表所有的单词，那是100,000,000个元素，几乎所有的数组元素都是0，这不是对数据编码的有效方法。如果要处理数据集中所有88,000多个独特的单词，就需要一个有近80亿个元素的数组！

为了降低维度，处理文本序列的RNN通常以嵌入层开始，嵌入层以更紧凑的密集向量对每个字进行编码。嵌入层产生的向量也可以捕获单词的上下文—给定单词如何与其周围的单词相关。因此，嵌入层使得RNN能够学习训练数据之间的单词关系。

还有预定义的单词嵌入，例如Word2Vec和GloVe，可以将这些加载到神经网络中以节省训练时间。当只有少量训练数据可用时，它们有时也用于向模型添加基本单词关系。可以将其建立在先前学习的单词关系上来提高模型的准确性，而不是在数据量不足的情况下学习这种关系。

我们来创建一个Embedding层（模块tensorflow.keras.layers）：

```python
[29]: rnn.add(Embedding(input_dim=number_of_words, output_dim=128, input_length=words_per_review))
```

其中，参数包括：

- input_dim—单词的数量。
- output_dim—每个单词嵌入的大小。如果加载预先存在的嵌入，如Word2Vec和GloVe，则必须将其设置为与所加载单词嵌入的大小相匹配。
- input_length=words_per_review—每个输入样本中的单词数。

### 添加LSTM层

接下来，我们将添加一个LSTM层：

```python
[30]: rnn.add(LSTM(units=128, dropout=0.2, recurrent_dropout=0.2))
```

其中，参数包括：

- units—网络层中的神经元数量。神经元越多，网络的记忆力就越好。你可以从正在处理的句子长度（本例中为200）和尝试预测的类别数量（本例中为2）之间的值开始。
- dropout—处理层输入和输出时随机禁用的神经元百分比。就像网络中的池化层一样，dropout是一种经过验证的技术，可以缓解过拟合。Keras提供了一个Dropout层，可以将其添加到模型中。
- recurrent_dropout—当某一层的输出再次反馈到该层时，随机禁用的神经元百分比，以允许网络从之前看到的内容中学习。

> LSTM层执行任务的机制超出了本书的范围。Chollet曾说：“使用者不需要了解LSTM单元的特定结构。作为人类，理解它不应该是你的工作。你只需记住LSTM单元的用途，即允许以后重新注入过去的信息。”

### 添加Dense输出层

最后，我们需要获取LSTM层的输出并将其减少为一个结果，指示评论是正面的还是负面的，因此units参数的值为1。这里使用sigmoid激活函数，它是二分类的首选，用于将任意值压缩到0.0～1.0的范围，产生一个概率：

```python
[31]: rnn.add(Dense(units=1, activation='sigmoid'))
```

### 编译模型并显示摘要

下面来编译模型，这里只有两个可能的输出，所以使用 binary_crossentropy损失函数：

```python
[32]: rnn.compile(optimizer='adam',
loss='binary_crossentropy',
metrics=['accuracy'])
```

以下是模型的摘要。请注意，即使层数少于之前的CNN网络，RNN的可训练参数（网络权重）几乎是CNN的三倍，参数越多意味着训练时间越长。大量参数主要来自词汇表中的单词数量（加载了10,000个）乘以Embedding层输出的神经元数量（128）：

| Layer (type) | Output Shape | Param # |
| :--- | :--- | :--- |
| embedding_1 (Embedding) | (None, 200, 128) | 1280000 |
| lstm_1 (LSTM) | (None, 128) | 131584 |
| dense_1 (Dense) | (None, 1) | 129 |

Total params: 1,411,713
Trainable params: 1,411,713
Non-trainable params: 0

## 15.9.5 训练和评价模型

下面来训练模型。注意模型对每个epoch的训练时间比对卷积神经网络的训练时间更长，这是由于我们的RNN模型需要学习的参数（权重）数量较多。为了便于阅读，对训练准确性（acc）和验证准确性（val_acc）的值加粗—这些值表示训练样本和validation_data中样本预测的正确率。

```python
[34]: rnn.fit(X_train, y_train, epochs=10, batch_size=32,
            validation_data=(X_test, y_test))
Train on 25000 samples, validate on 5000 samples
Epoch 1/5
25000/25000 [==============================] - 299s 12ms/step - loss:
0.6574 - acc: 0.5868 - val_loss: 0.5582 - val_acc: 0.6964
Epoch 2/5
25000/25000 [==============================] - 298s 12ms/step - loss:
0.4577 - acc: 0.7786 - val_loss: 0.3546 - val_acc: 0.8448
Epoch 3/5
25000/25000 [==============================] - 296s 12ms/step - loss:
0.3277 - acc: 0.8594 - val_loss: 0.3207 - val_acc: 0.8614
Epoch 4/5
25000/25000 [==============================] - 307s 12ms/step - loss:
0.2675 - acc: 0.8864 - val_loss: 0.3056 - val_acc: 0.8700
Epoch 5/5
25000/25000 [==============================] - 310s 12ms/step - loss:
0.2217 - acc: 0.9083 - val_loss: 0.3264 - val_acc: 0.8704
[34]: <tensorflow.python.keras.callbacks.History object at 0xb3ba8828E>
```

最后，可以使用测试数据评价预测结果。`evaluate`函数返回测试损失和测试准确性。在这里，该模型准确性为85.99%：

```python
[35]: results = rnn.evaluate(X_test, y_test)
20000/20000 [==============================] - 42s 2ms/step
[36]: results
[36]: [0.3415240607559681, 0.8599]
```

请注意，与CNN结果相比，该模型的准确性似乎较低，但这是一个处理起来更加困难的问题。如果在线搜索其他IMDb情感分析二分类研究，可以找到排名前80的预测结果，这里用只有三层的小型递归神经网络做得已经相当不错了，当然，我们也可以研究一些在线模型并尝试生成更好的模型。

## 15.10 调整深度学习模型

在15.9.5节中，在fit方法的输出中注意到，测试准确性（85.99%）和验证准确性（87.04%）显著低于90.83%的训练准确性，这种差异通常是过拟合的结果，因此我们的模型还有很大的改进空间。如果看一下每个epoch的输出，就会发现训练和验证的准确性都在不断提高。回想一下，训练太多次epoch可能会导致过拟合，但我们现在可能还没有训练充分。对于该模型，可能调整一下某个超参数就可以增加epoch数。

影响模型性能的一些变量包括：

- 更多或更少的训练数据；
- 更多或更少的测试数据；
- 更多或更少的验证数据；
- 更多或更少的网络层；
- 网络层类型；
- 网络层顺序。

在我们的IMDb RNN示例中，可以调整的一些内容包括：

- 尝试不同数量的训练数据—我们只使用了前10,000个单词；
- 设置每条评论的不同单词数量—我们只用了200；
- 网络层中不同数量的神经元；
- 更多层；
- 加载预先训练过的单词向量，而不是让嵌入层从头开始学习它们。

多次训练模型所需的计算时间非常重要，因此，在深度学习中，通常不会使用k折交叉验证或网格搜索等技术来调整超参数。目前已经有各种调整方法，但是一个特别有希望的领域是自动机器学习（AutoML），例如，Auto-Keras库专门用于自动为各种Keras模型选择最佳配置，其他自动机器学习工具包括Google的Cloud AutoML和百度的EZDL。

## 15.11 在ImageNet上预训练的CNN模型

利用深度学习，我们则不需要通过昂贵的训练、验证和测试开始每个新的项目，而是可以使用预训练的深度神经网络模型来

- 做出新的预测；
- 继续使用新数据或进一步训练模型；
- 将类似问题的模型所学习的权重转移到新模型中—迁移学习。

### Keras预训练的CNN模型

Keras捆绑了以下预训练的CNN模型，每个模型都在ImageNet上进行了预训练（ImageNet是一个包含1400多万张图像的不断增长的数据集）：

- Xception
- VGG16
- VGG19
- ResNet50
- Inception v3
- Inception-ResNet v2
- MobileNet v1
- DenseNet
- NASNet
- MobileNet v2

### 重用预训练模型

ImageNet对于大多数计算机来说都太大了，因此大多数对它感兴趣的人都是从一个较小的预训练模型开始使用的。

我们可以仅重用每个模型的体系结构并使用新数据进行训练，也可以重用预训练的权重。有关一些简单示例，请参阅：https://keras.io/applications/。

### ImageNet挑战赛

本章未将研究用于评价目标检测和图像识别模型的“ImageNet Large Scale Visual Recognition Challenge”。此竞赛从2010年到2017年一直在进行，ImageNet现在在Kaggle竞赛网站上一直保持着运行，称为“ImageNet Object Localization Challenge”，目标是识别“图像中的所有对象，然后对这些图像进行分类和注释”，ImageNet每季度会发布一次当前参与者的排行榜。

我们在机器学习和深度学习章节中看到的很多内容都是Kaggle竞赛网站上的。对于很多机器学习和深度学习任务而言，没有明显的最佳解决方案，人们的创造力确实是唯一的限制。很多公司和组织为Kaggle平台提供资金支持，以鼓励全世界的人开发出更好的解决方案。有些公司会在著名的Netflix竞赛中资助高达100万美元的奖金，Netflix希望参赛的模型可带来10%或更高的改进，以确定人们是否会喜欢一部电影，这取决于人们如何评价以前的电影，他们使用这些结果来向会员提供更好的观影建议。即使没有赢得Kaggle竞赛，这个参与的过程也是为当前感兴趣的问题获取解决经验的好方法。

## 15.12 小结

在第15章中，我们了解了AI的未来。深度学习已经吸引了计算机科学和数据科学领域的想象力，这可能是本书中最重要的AI章节。

我们提到了关键的深度学习平台，表明了Google的TensorFlow是使用最广泛的工具库。我们讨论了为什么使用Keras，因为它提供了一个友好的TensorFlow界面，而且已经非常受欢迎。

我们为TensorFlow、Keras和JupyterLab设置了一个自定义的Anaconda环境，然后使用该环境来实现Keras示例。

我们解释了什么是张量，以及为什么它对深度学习至关重要。我们讨论了构建Keras深度学习模型的神经元和多层神经网络的基础知识，考虑了一些流行的网络层类型以及如何布局它们。

我们引入了卷积神经网络并指出它们特别适用于计算机视觉应用。然后，使用手写数字的MNIST数据库构建、训练、验证和测试了一个网络，我们的预测准确性达到了99.17%。这是非常了不起的，因为我们是通过基本模型并且没进行任何超参数调整来实现的。你也可以尝试更复杂的模型并调整超参数来获得更好的性能。我们还列出了各种有趣的计算机视觉任务。

我们引入了TensorBoard来可视化TensorFlow和Keras神经网络的训练和验证过程，还讨论了ConvnetJS，一种基于浏览器的CNN训练和可视化工具，使我们可以窥探训练过程。

接下来，我们提出了用于处理序列数据（例如时间序列或句子中的文本）的递归神经网络（RNN）。我们使用基于IMDb影评数据集的RNN来执行二分类，预测每个评论的情感是正面的还是负面的。我们还讨论了如何调整深度学习模型，以及像NVIDIA GPU和Google TPU这样的高性能硬件是如何让更多人可以处理更实质性深度学习研究的。

考虑到训练深度学习模型的成本和耗时，我们解释了使用预训练模型的策略。我们列出了在大量ImageNet数据集上训练的各种Keras CNN图像处理模型，并讨论了迁移学习如何使我们能够快速有效地使用这些模型创建新模型。深度学习是一个庞大而复杂的主题，我们在本章中更专注于基础知识。

在下一章中，我们将介绍支持第12～15章中讨论过的各种AI技术的大数据基础设施。我们将考虑用于大数据批处理和实时流应用的Hadoop和Spark平台，查看关系数据库和用于查询它们的SQL语言—这些已经在数据库领域占据了数十年的主导地位。我们将讨论大数据如何应对关系数据库无法处理的挑战，并考虑如何设计NoSQL数据库来应对这些挑战。我们将通过对物联网的讨论来结束本书。物联网将成为世界上最大的大数据来源，并为企业家提供很多机会，帮助他们开发出能够真正改变人们生活的前沿商业。

## 第16章

## 大数据：Hadoop、Spark、NoSQL和IoT

### 目标

- 了解与大数据及其增长速度有关的概念。
- 使用结构化查询语言（SQL）操作SQLite关系数据库。
- 了解NoSQL数据库的四种主要类型。
- 将推文存储在MongoDB NoSQL JSON文档数据库中，并在Folium地图上进行可视化。
- 了解Apache Hadoop及其在大数据批处理应用程序中的使用方法。
- 在Microsoft的Azure HDInsight云服务上构建Hadoop MapReduce应用程序。
- 了解Apache Spark及其在高性能、实时大数据应用程序中的使用方法。
- 使用Spark流处理小批量数据。
- 了解物联网（IoT）和发布/订阅模型。
- 发布来自模拟互联网连接设备的消息，并在仪表板中进行消息可视化。
- 订阅PubNub的实时Twitter和IoT流并进行数据可视化。
