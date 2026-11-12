```
随机变换
plt.show()
```

![](bbox=[0.43, 0.15, 0.58, 0.26])

原始图像

![](bbox=[0.11, 0.29, 0.91, 0.41])

五次随机变换后的图像

在上一节中，我们使用model.fit函数指定训练数据对模型进行训练。要使用增强后的数据代替原数据进行训练，只需要更改这行代码。

```
model.fit(x_train, y_train, batch_size=32, epochs=10)
```

启用图像数据增强时，使用Keras提供的fit_generator函数。它的用法如下。

```
model.fit_generator(datagen.flow(x_train, y_train, batch_size=32), epochs=10)
```

fit_generator接收的第一个参数是datagen.flow(x_train, y_train, batch_size=32),这个参数的意思是使用定义好的datagen对原始输入数据进行变换，生成新的训练数据，每批次生成32个。对生成的训练数据预处理后，我们就可以像之前一样训练模型了。

```
y_train = keras.utils.to_categorical(y_train)
y_test = keras.utils.to_categorical(y_test)
x_train = x_train / 255
x_test = x_test / 255
```