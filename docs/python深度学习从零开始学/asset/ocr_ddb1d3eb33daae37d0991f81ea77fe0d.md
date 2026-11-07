如图 12-15 所示，将 RGB 图像转换为 BGR，加载 VGG16 网络模型。在 Windows 下，不管是摄像头制造者，还是软件开发者，当时流行的都是 BGR 格式的数据结构，后面 RBG 格式才逐渐开始流行。自己训练的时候完全可以使用 RGB，新库也不存在是 RGB 还是 GBR 这个问题。但如果使用的是别人训练好的模型，就要注意一下使用的是 RGB 还是 GBR。

```
# Data normalisation and reshaping from RGB to BGR
input_image_array = np.asarray(input_image, dtype="float32")
input_image_array = np.expand_dims(input_image_array, axis=0)
input_image_array[:, :, :, 0] -= IMAGENET_MEAN_RGB_VALUES[2]
input_image_array[:, :, :, 1] -= IMAGENET_MEAN_RGB_VALUES[1]
input_image_array[:, :, :, 2] -= IMAGENET_MEAN_RGB_VALUES[0]
input_image_array = input_image_array[:, :, :, ::-1]

style_image_array = np.asarray(style_image, dtype="float32")
style_image_array = np.expand_dims(style_image_array, axis=0)
style_image_array[:, :, :, 0] -= IMAGENET_MEAN_RGB_VALUES[2]
style_image_array[:, :, :, 1] -= IMAGENET_MEAN_RGB_VALUES[1]
style_image_array[:, :, :, 2] -= IMAGENET_MEAN_RGB_VALUES[0]
style_image_array = style_image_array[:, :, :, ::-1]

# Model
input_image = backend.variable(input_image_array)
style_image = backend.variable(style_image_array)
combination_image = backend.placeholder((1, IMAGE_HEIGHT, IMAGE_SIZE, 3))

input_tensor = backend.concatenate([input_image, style_image, combination_image], axis=0)
model = VGG16(input_tensor=input_tensor, include_top=False)
```

图 12-15

使用预训练的 ImageNet 的权重构建 VGG16 网络，参数 include_top=False 表示会载入 VGG16 的模型，不包括加在最后 3 层的全连接层，通常是取得特征通过命令 print(model.summary()) 输出网络模型，结果如下所示；

| Layer (type) | Output Shape | Param # |
|---|---|---|
| input_2 (InputLayer) | (None, None, None, 3) | 0 |
| block1_conv1 (Conv2D) | (None, None, None, 64) | 1792 |
| block1_conv2 (Conv2D) | (None, None, None, 64) | 36928 |
| block1_pool (MaxPooling2D) | (None, None, None, 64) | 0 |
| block2_conv1 (Conv2D) | (None, None, None, 128) | 73856 |
| block2_conv2 (Conv2D) | (None, None, None, 128) | 147584 |
| block2_pool (MaxPooling2D) | (None, None, None, 128) | 0 |
| block3_conv1 (Conv2D) | (None, None, None, 256) | 295168 |
| block3_conv2 (Conv2D) | (None, None, None, 256) | 590080 |