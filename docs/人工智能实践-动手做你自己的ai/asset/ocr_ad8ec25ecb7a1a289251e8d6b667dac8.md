```
(x_train, y_train), (x_test, y_test) = cifar10.load_data()
y_train = keras.utils.to_categorical(y_train)
y_test = keras.utils.to_categorical(y_test)
print("独热化后的输出形状")
print(y_train.shape)
print(y_test.shape)
print("处理前的最大值为%f " % x_train.max())
x_train = x_train / 255
x_test = x_test / 255
print("处理后的最大值为%f " % x_train.max())
```

在 ImageDataGenerator 中定义图像数据增强的方式。

+   - rotation_range=30, 随机旋转不超过 30 度。
+   - horizontal_flip=True, 随机水平翻转。
+   - vertical_flip=True, 随机竖直翻转。
+   - width_shift_range=5, 随机水平平移不超过 5 像素。
+   - height_shift_range=5, 随机竖直平移不超过 5 像素。

```
datagen = ImageDataGenerator(
    rotation_range=30,
    horizontal_flip=True,
    vertical_flip=True,
    width_shift_range=5,
    height_shift_range=5
)
```

选取训练集中一张卡车的图像，用 ImageDataGenerator 对图像进行五次随机变换，并画出原图及五次随机变换后的图像。

```
origin_image = x_train[1] # 选取原图
# 将原图画出来
plt.imshow(origin_image)
plt.show()
# 对图像作五次随机变换并画出来
fig, ax = plt.subplots(1, 5, figsize=(15, 3))
ax = ax.flatten()
for i in range(5):
    ax[i].imshow(datagen.random_transform(origin_image)) # 使用datagen对图像作
```