```
for i in range(downsampling_n):
    dim *= 2
    x = conv2d(dim, 3, strides = 2, padding="same")(x)
    x = batchnorm()(x, training = 1)
    x = Activation('relu')(x)
# 下采样部分
for i in range(block_n):
    x = res_block(x, dim)
# 残差网络部分
for i in range(upsampling_n):
    dim = dim // 2
    x = Conv2DTranspose(dim, 3, strides = 2, kernel_initializer = conv_
init, padding="same")(x)
    x = batchnorm()(x, training = 1)
    x = Activation('relu')(x)
# 上采样
dim = 3
x = conv2d(dim, 7, padding="same")(x)
x = Activation("tanh")(x)
# 最后一层
return Model(inputs=input_t, outputs=x)
```

# 13.4.4 判别网络

判别网络的结构由几层卷积叠加而成，它比生成网络更简单，其代码如下：

```
def NET_D(ndf=64, max_layers = 3, image_size = 256):
    """
    判别网络
    """
    input_t = Input(shape=(image_size, image_size, 3))
    x = input_t
    x = conv2d(ndf, 4, padding="same", strides=2)(x)
    x = LeakyReLU(alpha = 0.2)(x)
    dim = ndf
    for i in range(1, max_layers):
        dim *= 2
        x = conv2d(dim, 4, padding="same", strides=2, use_bias=False)(x)
        x = batchnorm()(x, training=1)
        x = LeakyReLU(alpha = 0.2)(x)
    x = conv2d(dim, 4, padding="same")(x)
    x = batchnorm()(x, training=1)
    x = LeakyReLU(alpha = 0.2)(x)
    x = conv2d(1, 4, padding="same", activation = "sigmoid")(x)
    return Model(inputs=input_t, outputs=x)
```