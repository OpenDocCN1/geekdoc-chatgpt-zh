```
# 获取内容图像和风格图像的张量表示
content_image = K.variable(preprocess_image(content_image_path))
style_image = K.variable(preprocess_image(style_image_path))

# output_image用于存放生成的图像
if K.image_data_format() == 'channels_first':
    output_image = K.placeholder((1, 3, length, width))
else:
    output_image = K.placeholder((1, length, width, 3))

# 将三个张量拼接为单个张量
input_tensor = K.concatenate([content_image, style_image, output_image],
                             axis=0)
```

加载VGG16网络模型。

```
# 使用预训练的ImageNet的权重构建VGG16网络
model = VGG16(input_tensor=input_tensor,
              weights='imagenet', include_top=False)
print('Model has been loaded.')

# 将模型每一层的名字和输出放到一个字典里
output_dict = dict([(layer.name, layer.output) for layer in model.layers])
```

# （二） 计算损失函数

如果损失函数中只有内容损失和风格损失，那么生成图像有可能会过度拟合原图的内容或者风格，使得生成图像的相邻像素之间差异较大，看起来不自然，这种现象被称为“过拟合”。为了防止这种现象，保证图像局部的一致性，我们引入总变化损失(total variation loss)的概念。因此，最终的损失函数是内容损失、风格损失和总变化损失的加权平均。

设置各损失函数的权重。

```
# 设置内容损失、风格损失和总变化损失各自的权重
content_weight = 0.5
style_weight = 1.0
total_variation_weight = 1.0

# 设置总损失
loss = K.variable(0.0)
```