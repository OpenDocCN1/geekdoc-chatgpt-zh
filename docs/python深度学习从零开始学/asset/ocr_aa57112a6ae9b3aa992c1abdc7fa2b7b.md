这些处理步骤可能看起来有点多，但幸运的是，Keras 中的 keras.preprocessing.image 包含图像处理辅助工具 ImageDataGenerator 类，它可以快速创建 Python 生成器，能够将硬盘上的图像文件自动转换为预处理好的张量批量。程序代码如图 8-49 所示。

```
from keras.preprocessing.image import ImageDataGenerator
#图像像素的值域以255、缩放之0到1之间
train_datagen = ImageDataGenerator(rescale = 1. / 255)
test_datagen = ImageDataGenerator(rescale = 1. / 255)
#generator 实际上是将数据批量载入内存,被代码类似for in 的方式方便的访问
#使用flow_from_directory()方法可以实现化一个针对图像batch的生成器
train_generator = train_datagen.flow_from_directory(
    train_dir, target_size=(150, 150), # 将所有图像大小调整为150*150
    batch_size=20,
    class_mode = 'binary') #因为使用了binary_crossentropy损失，所以需要使用二进制标签
validation_generator = test_datagen.flow_from_directory(
    validation_dir, target_size = (150, 150), batch_size = 20,
    class_mode = 'binary')

Found 3000 images belonging to 2 classes.
Found 1000 images belonging to 2 classes.
```

# 图 8-49

执行代码生成了由 150×150 的 RGB 图像 [形状为 (20, 150, 150, 3)] 与二进制标签 [形状为 (20, )] 组成的批量，每个批量中包含 20 个样本（批量大小）。

# 8.4.4 进行训练

搭建完卷积神经网络模型，就可以使用反向传播法进行训练。在训练模型之前，我们需要用 compile 方法对训练模型进行设置。如图 8-50 所示，使用交叉熵损失函数（binary_crossentropy）训练我们的模型，优化器算法使用 adam，评估指标使用 accuracy。

```
# 对训练模型进行设置
#使用adam优化器，使用交叉熵为损失函数
model.compile(loss='categorical_crossentropy', optimizer='adam', metrics=['accuracy'])
```

# 图 8-50

如图 8-51 所示，使用 fit_generator 方法来拟合数据（在生成器上的效果和 fit 函数一样），它的第一个参数应该是 Python 生成器。因为数据是不断生成的，所以 Keras 模型需要知道每一轮需要从生成器中抽取多少个样本，这就用到了 step_per_epoch 参数。从生成器中抽取 steps_per_epoch 个批量后（即运行了 steps_per_epoch 次梯度下降），拟合过程将进入下一个轮次。我们还可以向里面传入一个 validation_data 参数，这个参数可以是一个生成器，也可以是 NumPy 数组组成的元组，如果是生成器的话，还需要指定 validation_steps 参数用来说明需要从验证的生成器中抽取多少个批次用于评估。

```
train_history = model.fit_generator(train_generator, steps_per_epoch = 150,
    epochs = 30, validation_data = validation_generator,
    verbose=2, validation_steps = 100)
```

# 图 8-51

部分训练结果如图 8-52 所示。