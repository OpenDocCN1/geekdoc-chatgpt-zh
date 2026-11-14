
Flatten层之前的层学习了数字特征。现在需要汇集所有这些特征并学习它们之间的关系，这样模型就可以对每个图像代表的数字分类。学习特征之间的关系和执行分类是通过全连接的Dense层完成的，就像本章前面的神经网络图所示。以下Dense层创建了128个神经元（单位），可以从上一层的3200个输出中学习：

```
[32]: cnn.add(Dense(units=128, activation='relu'))
```

很多CNN包含至少一个这样的Dense层，用于处理更复杂的具有更高分辨率的像ImageNet那样的图像数据集的网络，通常有好几个Dense层，每层有4096个神经元（ImageNet数据集有超过1400万张图像），可以在Keras的几个预训练的ImageNet CNN中看到这样的配置——将在15.11节中列出。

## 添加另一个Dense层以产生最终输出

我们的最后一层是一个Dense层，它将输入分类为代表0~9类的神经元，softmax激活函数将这10个神经元的值转换为分类概率，产生最高概率的神经元代表了对给定数字图像的预测：

```
[33]: cnn.add(Dense(units=10, activation='softmax'))
```

## 打印模型摘要

模型的summary方法可以显示模型的层，需要注意各层的输出形式和参数的数量。参数是网络在训练期间学习的权重，我们创建的只是一个相对较小的网络，但它需要学习近500,000个参数！这仅适用于大多数智能手机上不到图标分辨率四分之一的微小图像。想象一下，网络必须学习多少特征来处理高分辨率4K视频帧或当今数码相机产生的超高分辨率图像。在Output Shape中，None表示模型事先不知道将提供多少训练样本——仅在开始训练时才知道。

```
[34]: cnn.summary()
```

| Layer (type) | Output Shape | Param # |
|---|---|---|
| conv2d_1 (Conv2D) | (None, 26, 26, 64) | 640 |
| max_pooling2d_1 (MaxPooling2D) | (None, 13, 13, 64) | 0 |
| conv2d_2 (Conv2D) | (None, 11, 11, 128) | 73856 |
| max_pooling2d_2 (MaxPooling2D) | (None, 5, 5, 128) | 0 |
| flatten_1 (Flatten) | (None, 3200) | 0 |
| dense_1 (Dense) | (None, 128) | 409728 |
| dense_2 (Dense) | (None, 10) | 1290 |

Total params: 485,514
Trainable params: 485,514
Non-trainable params: 0

另请注意，没有“不可训练”的参数。默认情况下，Keras会训练所有的参数，但可以阻止对特定层的训练，这通常发生在调整网络或在新模型中使用其他模型学习的参数时（这个过程称为迁移学习）。

## 可视化模型结构

可以使用tensorflow.keras.utils模块中的plot_model函数可视化模型摘要：

```
[35]: from tensorflow.keras.utils import plot_model
from IPython.display import Image
plot_model(cnn, to_file='convnet.png', show_shapes=True,
           show_layer_names=True)
Image(filename='convnet.png')
```

在convnet.png中存储可视化后，我们使用IPython.display模块的Image函数在Jupyter Notebook中显示图像。Keras在图像中指定了网络层的名称。

![](img/bf43b6caad271ba92b343a48c79c2155_737_0.png)

## 编译模型

添加完所有的层后，通过调用compile方法使模型完整：

```
[36]: cnn.compile(optimizer='adam',
loss='categorical_crossentropy',
metrics=['accuracy'])
```

其中的参数如下：

- optimizer='adam'—该优化器将用于在学习时调整整个神经网络的权重。优化器有很多，但'adam'在很多模型中都表现良好。
- loss='categorical_crossentropy'—这是优化器在多分类网络中使用的损失函数，例如我们的预测网络，它将预测10个类别。当神经网络学习时，优化器会尝试最小化损失函数返回的值，损失越低，神经网络预测每个图像的效果越好。对于二分类问题（将在本章后面介绍），Keras提供了'binary_crossentropy'损失函数，并为回归问题提供了'mean_squared_error'损失函数。有关其他损失函数，请参阅https://keras.io/loss/。
- metrics=['accuracy']—这是帮助我们评估模型效果的指标列表。准确性是分类模型中常用的指标。在此示例中，我们使用 `accuracy`指标来检查正确预测的百分比。有关其他指标的列表，请参阅https://keras.io/metrics/。

## 15.6.5 训练和评价模型

与scikit-learn的模型类似，我们通过调用fit方法来训练Keras模型：

- 与scikit-learn一样，前两个参数是训练数据和分类目标标签。
- epochs指定模型应处理整组训练数据的次数。正如前面提到的，神经网络是迭代训练的。
- batch_size指定每个epoch期间单次处理的样本数。大多数模型指定为32～512的2的幂次。若batch_size太大，则会降低模型准确性，所以选择了64，也可以尝试不同的值来查看它们是如何影响模型性能的。
- 通常需要使用一些样本来验证模型，如果指定了验证数据，则每个epoch结束之后，将使用模型对验证集进行预测并显示验证损失和准确性，可以通过研究这些值来调整网络层和fit方法的超参数，或者更改模型的层组成。在这里，我们使用`validation_split`参数来指示保留最后10%（0.1）的训练样本进行模型验证，共6,000个验证样本。如果有单独的验证数据，则可以使用`validation_data`参数（如15.9节所示）来指定包含这些样本和目标标签数组的元组。通常，最好随机选择验证样本，利用scikit-learn的`train_test_split`函数（将在本章稍后介绍），然后使用`validation_data`参数传递随机选择的数据。

在以下输出中，我们以粗体突出显示了训练准确性（acc）和验证准确性（val_acc）：

```
[37]: cnn.fit(X_train, y_train, epochs=5, batch_size=64,
              validation_split=0.1)
Train on 54000 samples, validate on 6000 samples
Epoch 1/5
54000/54000 [==============================] - 68s 1ms/step - loss:
0.1407 - acc: 0.9580 - val_loss: 0.0452 - val_acc: 0.9867
Epoch 2/5
54000/54000 [==============================] - 64s 1ms/step - loss:
0.0426 - acc: 0.9867 - val_loss: 0.0409 - val_acc: 0.9878
Epoch 3/5
54000/54000 [==============================] - 69s 1ms/step - loss:
0.0299 - acc: 0.9902 - val_loss: 0.0325 - val_acc: 0.9912
Epoch 4/5
54000/54000 [==============================] - 70s 1ms/step - loss:
0.0197 - acc: 0.9935 - val_loss: 0.0335 - val_acc: 0.9903
Epoch 5/5
54000/54000 [==============================] - 63s 1ms/step - loss:
0.0155 - acc: 0.9948 - val_loss: 0.0297 - val_acc: 0.9927
[37]: <tensorflow.python.keras.callbacks.History at 0x7f105ba0ada0>
```

在15.7节中，我们将介绍TensorBoard——一个TensorFlow工具，用于从深度学习模型中可视化数据，特别是，我们将查看训练和验证准确性以及损失值是如何通过每个epoch发生变化的。在15.8节中，我们将演示Andrej Karpathy的ConvnetJS工具，它可以在Web浏览器中训练网络并动态显示网络层的输出，包括每个卷积层在学习时“看到”的内容，我们还将运行其中的MNIST和CIFAR10模型，这会帮助我们更好地理解神经网络的复杂操作。

随着训练的进行，fit方法不断输出信息来显示每个epoch的进展、执行的时间（在这种情况下，每个epoch的执行需花费63~70秒），以及相应的评价指标。在该模型的最后一个epoch中，训练样本的准确性（acc）达到99.48%，验证样本的准确性（val_acc）达到99.27%。这个结果非常好，考虑到还没有尝试调整超参数或网络层的数量和类型，再进行这些调整可能还会带来更好的结果。像机器学习一样，深度学习是一种经验科学，可以从大量实验中获益。

## 评价模型

现在可以检查模型在未知数据上的准确性。为此，我们调用了evaluate方法，该方法可输出处理测试样本所需的时间（这里为4秒和366微秒）：

```
[38]: loss, accuracy = cnn.evaluate(X_test, y_test)
10000/10000 [==============================] - 4s 366us/step
[39]: loss
[39]: 0.026809450998473768
[40]: accuracy
[40]: 0.9917
```

根据前面的输出，我们的预测模型在预测未知数据的标签时准确性为99.17%，而且此时我们还没有尝试调整模型。通过一些在线研究，可以找到能够以近100%的准确性预测MNIST数据集的模型。可以自己尝试不同数量的层、不同的层类型和层参数，并观察这些尝试如何影响结果。

## 做出预测

模型的predict方法在其参数数组（X_test）中预测数字图像的类别：

```
[41]: predictions = cnn.predict(X_test)
```

可以通过查看y_test[0]来检查第一个样本的真实目标值是什么：

```
[42]: y_test[0]
array([0., 0., 0., 0., 0., 0., 0., 1., 0., 0.], dtype=float32)
```

根据输出结果，第一个样本是数字 “7”，因为测试样本标签的分类表示在索引7中指定了1.0，回想一下，我们之前通过独热编码创建了这种分类表示。

来检查一下predict方法在第一个测试样本上返回的概率：

```
[43]: for index, probability in enumerate(predictions[0]):
    print(f'{index}: {probability:.10%}')
0: 0.0000000201%
1: 0.0000001355%
2: 0.000186951%
3: 0.0000015494%
4: 0.000000003%
5: 0.0000000012%
6: 0.000000000%
7: 99.9999761581%
8: 0.000005577%
9: 0.000011416%
```

根据输出结果，predictions[0]表明我们的模型认为这个数字是“7”，且具有近100%的确定性，但是并非所有的预测都具有这种确定性。

## 找出不正确的预测

接下来，我们想要查看一些被错误预测的图像，以了解我们的模型在哪些数字上遇到了问题。例如，如果它总是把数字 “8” 预测错误，那么训练数据中可能需要加更多的数字 “8” 的样本。

在查看不正确的预测之前，我们需要先找到它们。考虑上面的 predictions[0]，为了确定预测是否正确，我们必须将predictions[0]中的最大概率的索引与y_test[0]中为1.0的元素索引进行比较，如果索引值相同，那么预测是正确的，反之就是不正确的。NumPy的argmax函数可以确定其数组参数中最大值元素的索引，我们用它来定位不正确的预测。在下面的代码段中，p是预测值数组，e是期望值数组（期望值是数据集中测试图像的真实标签）：

```
[44]: images = X_test.reshape((10000, 28, 28))
incorrect_predictions = []
for i, (p, e) in enumerate(zip(predictions, y_test)):
    predicted, expected = np.argmax(p), np.argmax(e)
    if predicted != expected:
        incorrect_predictions.append(
            (i, images[i], predicted, expected))
```

这段代码中，我们首先将数据从Keras需要的 (28, 28, 1) 尺寸变换为 (28, 28) 尺寸，因为Matplotlib需要显示出图像。接下来，我们使用for语句逐渐填充incorrect_predictions列表，对数组predictions和y_test中代表每个样本的行进行zip处理，然后枚举这些行，以便可以捕获它们的索引。如果p和e的argmax操作结果不同，那么预测是不正确的，我们将一个元组添加到包含该样本索引、图像、预测值和期望值的incorrect_predictions列表中。可以通过以下方式确认不正确预测的总数（在测试集中的10,000个图像内）：

```
[45]: len(incorrect_predictions)
[45]: 83
```

## 可视化不正确的预测

以下代码显示了被不正确预测的24幅图像，并标示出了它们的索引、预测值（p）和期望值（e）：

```
[46]: figure, axes = plt.subplots(nrows=4, ncols=6, figsize=(16, 12))
for axes, item in zip(axes.ravel(), incorrect_predictions):
    index, image, predicted, expected = item
    axes.imshow(image, cmap=plt.cm.gray_r)
    axes.set_xticks([]) # remove x-axis tick marks.
    axes.set_yticks([]) # remove y-axis tick marks
    axes.set_title(
    f'index: {index}\np: {predicted}; e: {expected}')
plt.tight_layout()
```

在读期望值之前，请查看每个数字并记下你认为可能的数字，这也是了解数据的重要环节：

![](img/bf43b6caad271ba92b343a48c79c2155_742_0.png)

## 显示几个不正确预测的概率

下面来看一些不正确预测的概率，以下函数显示了指定预测数组的概率：

```
[47]: def display_probabilities(prediction):
    for index, probability in enumerate(prediction):
        print(f"{index}: {probability:.10%}")
```

虽然上图的第一行中的 “8” （在索引495处）看起来像 “8” ，但我们的模型在预测它时却遇到了问题，从下面的输出中可以看到，模型预测此图像为数字 “0” ，但也认为它有16%的可能性是 “6” 和有23%的可能性是 “8” ：

```
[48]: display_probabilities(predictions[495])
0: 59.7235262394%
1: 0.0000015465%
2: 0.8047289215%
3: 0.0001740813%
4: 0.0016636326%
5: 0.0030567855%
6: 16.1390662193%
7: 0.0000017818%
8: 23.3022540808%
9: 0.0255270657%
```

第一行中的 “2” （索引583）被预测为 “7” ，确定性为62.7%，但该模型还认为该数字为 “2” 的可能性是36.4%：

```
[49]: display_probabilities(predictions[583])
0: 0.0000003016%
1: 0.0000005715%
2: 36.4056706429%
3: 0.0176281916%
4: 0.0000561930%
5: 0.0000000003%
6: 0.0000000019%
7: 62.7455413342%
8: 0.8310816251%
9: 0.0000114385%
```

第二排开始时的 “6” （在索引625处）被预测为 “4” ，但远非确定，因为它被预测为数字 “4” （51.6%）的概率仅略高于 “6” （48.38%）：

```
[50]: display_probabilities(predictions[625])
0: 0.008245181%
1: 0.0000441209%
2: 0.0012774357%
3: 0.0000000009%
4: 51.6223073006%
5: 0.0000001779%
6: 48.3754962683%
7: 0.0000000085%
8: 0.0000048182%
9: 0.0000785786%
```

## 15.6.6 保存和加载模型

神经网络模型可能需要用大量的时间来训练，一旦设计并测试了适合需求的模型，就可以保存它的状态，以便稍后加载它以进行更多预测，因为有时会加载模型并进一步对其训练来解决新的问题。例如，模型中的层已经知道如何识别线条和曲线等特征，这些特征在手写字符识别中也很有用（如在MNIST数据集中），因此，可以加载现有
