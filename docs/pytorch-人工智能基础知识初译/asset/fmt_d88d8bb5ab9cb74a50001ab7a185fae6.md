
# DCGAN 模型摘要与训练

## 生成器模型摘要

```
----------------------------------------------------------------
        Layer (type)               Output Shape         Param #
================================================================
            Linear-1                [-1, 12544]       1,266,944
   ConvTranspose2d-2          [-1, 128, 14, 14]         524,416
       BatchNorm2d-3          [-1, 128, 14, 14]             256
         LeakyReLU-4          [-1, 128, 14, 14]               0
   ConvTranspose2d-5           [-1, 64, 14, 14]          73,792
       BatchNorm2d-6           [-1, 64, 14, 14]             128
         LeakyReLU-7           [-1, 64, 14, 14]               0
   ConvTranspose2d-8            [-1, 1, 28, 28]           1,025
              Tanh-9            [-1, 1, 28, 28]               0
================================================================
Total params: 1,866,561
Trainable params: 1,866,561
Non-trainable params: 0
----------------------------------------------------------------
Input size (MB): 0.00
Forward/backward pass size (MB): 0.97
Params size (MB): 7.12
Estimated Total Size (MB): 8.09
----------------------------------------------------------------
```

## 判别器模型摘要与训练步骤

**4. 然后，我们将打印判别器摘要：**

```
>>summary(discriminator, (1, 28, 28))
```

这给我们以下输出：

```
----------------------------------------------------------------
        Layer (type)               Output Shape         Param #
================================================================
            Conv2d-1           [-1, 32, 14, 14]             320
         LeakyReLU-2           [-1, 32, 14, 14]               0
            Conv2d-3             [-1, 64, 7, 7]          18,496
       BatchNorm2d-4             [-1, 64, 7, 7]             128
         LeakyReLU-5             [-1, 64, 7, 7]               0
            Conv2d-6            [-1, 128, 4, 4]          73,856
       BatchNorm2d-7            [-1, 128, 4, 4]             256
         LeakyReLU-8            [-1, 128, 4, 4]               0
            Linear-9                    [-1, 1]           2,049
================================================================
Total params: 95,105
Trainable params: 95,105
Non-trainable params: 0
----------------------------------------------------------------
Input size (MB): 0.00
Forward/backward pass size (MB): 0.21
Params size (MB): 0.36
Estimated Total Size (MB): 0.58
----------------------------------------------------------------
```

**5. 接下来，我们将定义损失函数：**

```
>>criterion = nn.BCELoss()
```

**6. 我们还将创建一个固定的噪声：**

```
>>fixed_noise = torch.randn(64, z_dim, device=device)
```

**7. 现在，我们将定义优化器函数：**

```
>>doptimizer = optim.Adam(discriminator.parameters())
>>goptimizer = optim.Adam(generator.parameters())
```

**8. 然后，我们将为判别器设置标签：**

```
>>real_label, fake_label = 1, 0
```

**9. 我们还将准备存储我们训练中的指标：**

```
>>image_list = []
>>g_losses = []
>>d_losses = []
>>iterations = 0
>>num_epochs = 50
```

**0. 现在，我们开始训练循环：**

```
>>for epoch in range(num_epochs):
```

**1. 然后我们遍历数据：**

```
print(f'Epoch : | {epoch+1:03} / {num_epochs:03} |')
for i, data in enumerate(train_loader):
```

**2. 然后，我们通过清除梯度来开始训练判别器：**

```
discriminator.zero_grad()
```

**3. 然后我们获取图像：**

```
real_images = data[0].to(device)
size = real_images.size(0)
```

**4. 然后，我们为这些图像创建标签：**

```
label = torch.full((size,), real_label, device=device)
```

**5. 接下来，我们得到判别器输出：**

```
d_output = discriminator(real_images).view(-1)
```

**6. 然后我们计算判别器误差：**

```
derror_real = criterion(d_output, label)
```

**7. 接下来，我们计算梯度：**

```
derror_real.backward()
```

**8. 现在我们将创建一个噪声向量：**

```
noise = torch.randn(size, z_dim, device=device)
```

**9. 接下来，我们将噪声向量传递给生成器：**

```
fake_images = generator(noise)
```

**0. 然后，我们为生成的图像创建标签：**

```
label.fill_(0)
```

**1. 然后我们将它们传递给判别器：**

```
d_output = discriminator(fake_images.detach()).view(-1)
```

**2. 接下来，我们得到误差和梯度：**

```
derror_fake = criterion(d_output, label)
derror_fake.backward()
derror_total = derror_real + derror_fake
```

**3. 然后，我们更新判别器权重：**

```
doptimizer.step()
```

**4. 我们通过清除梯度开始训练生成器：**

```
generator.zero_grad()
```

**5. 然后，我们将标签从假更改为真：**

```
label.fill_(1)
```

**6. 接下来，我们得到判别器输出：**

```
d_output = discriminator(fake_images).view(-1)
```

**7. 然后我们计算生成器损失和梯度并更新生成器权重：**

```
gerror = criterion(d_output, label)
gerror.backward()
goptimizer.step()
```

**8. 然后我们保存了损失：**

```
if i % 50 == 0:
    print(f'| {i:03} / {len(train_loader):03} | G Loss: {gerror.item():.3f} | D Loss: {derror_total.item():.3f} |')
    g_losses.append(gerror.item())
    d_losses.append(derror_total.item())
```

**9. 然后，我们从固定噪声中保存图像：**

```
if (iterations % 500 == 0) or ((epoch == num_epochs-1) and (i == len(train_loader)-1)):
    with torch.no_grad():
        fake_images = generator(fixed_noise).detach().cpu()
        image_list.append(vutils.make_grid(fake_images, padding=2, normalize=True))

iterations += 1
```

以下是示例输出：

```
Epoch : | 001 / 050 |
| 000 / 469 | G Loss: 1.939 | D Loss: 1.432 |
| 050 / 469 | G Loss: 3.920 | D Loss: 0.266 |
| 100 / 469 | G Loss: 3.900 | D Loss: 0.406 |
| 150 / 469 | G Loss: 3.260 | D Loss: 0.230 |
| 200 / 469 | G Loss: 3.856 | D Loss: 0.556 |
| 250 / 469 | G Loss: 4.097 | D Loss: 0.123 |
| 300 / 469 | G Loss: 2.377 | D Loss: 0.416 |
| 350 / 469 | G Loss: 2.984 | D Loss: 0.416 |
| 400 / 469 | G Loss: 3.262 | D Loss: 0.140 |
| 450 / 469 | G Loss: 3.469 | D Loss: 0.849 |
Epoch : | 002 / 050 |
| 000 / 469 | G Loss: 2.057 | D Loss: 0.484 |
| 050 / 469 | G Loss: 2.108 | D Loss: 0.435 |
| 100 / 469 | G Loss: 1.714 | D Loss: 0.862 |
| 150 / 469 | G Loss: 3.902 | D Loss: 0.199 |
| 200 / 469 | G Loss: 3.869 | D Loss: 0.086 |
| 250 / 469 | G Loss: 2.390 | D Loss: 0.208 |
| 300 / 469 | G Loss: 3.008 | D Loss: 0.586 |
| 350 / 469 | G Loss: 4.662 | D Loss: 0.074 |
| 400 / 469 | G Loss: 3.353 | D Loss: 0.368 |
| 450 / 469 | G Loss: 5.080 | D Loss: 0.110 |
Epoch : | 003 / 050 |
| 000 / 469 | G Loss: 7.159 | D Loss: 0.008 |
| 050 / 469 | G Loss: 5.087 | D Loss: 0.056 |
| 100 / 469 | G Loss: 4.232 | D Loss: 0.184 |
| 150 / 469 | G Loss: 5.037 | D Loss: 0.141 |
| 200 / 469 | G Loss: 5.636 | D Loss: 0.570 |
| 250 / 469 | G Loss: 3.624 | D Loss: 0.304 |
| 300 / 469 | G Loss: 4.291 | D Loss: 0.214 |
| 350 / 469 | G Loss: 2.901 | D Loss: 0.247 |
| 400 / 469 | G Loss: 3.703 | D Loss: 0.643 |
| 450 / 469 | G Loss: 1.149 | D Loss: 1.035 |
Epoch : | 004 / 050 |
| 000 / 469 | G Loss: 3.317 | D Loss: 0.202 |
| 050 / 469 | G Loss: 2.990 | D Loss: 0.350 |
| 100 / 469 | G Loss: 2.680 | D Loss: 0.162 |
| 150 / 469 | G Loss: 2.934 | D Loss: 0.391 |
| 200 / 469 | G Loss: 3.736 | D Loss: 0.215 |
| 250 / 469 | G Loss: 3.601 | D Loss: 0.199 |
| 300 / 469 | G Loss: 4.288 | D Loss: 0.164 |
| 350 / 469 | G Loss: 2.978 | D Loss: 0.086 |
| 400 / 469 | G Loss: 3.827 | D Loss: 0.189 |
| 450 / 469 | G Loss: 4.283 | D Loss: 0.216 |
Epoch : | 005 / 050 |
| 000 / 469 | G Loss: 4.456 | D Loss: 0.250 |
| 050 / 469 | G Loss: 4.886 | D Loss: 0.160 |
| 100 / 469 | G Loss: 1.844 | D Loss: 0.447 |
| 150 / 469 | G Loss: 3.680 | D Loss: 0.505 |
| 200 / 469 | G Loss: 4.428 | D Loss: 0.200 |
| 250 / 469 | G Loss: 4.270 | D Loss: 0.222 |
| 300 / 469 | G Loss: 4.617 | D Loss: 0.102 |
| 350 / 469 | G Loss: 3.920 | D Loss: 0.092 |
| 400 / 469 | G Loss: 4.010 | D Loss: 0.392 |
| 450 / 469 | G Loss: 1.705 | D Loss: 0.651 |
```

至此，我们已经完成了 `DCGAN` 的训练。

## 工作原理

我们从`weights_init`函数开始，该函数用于从均值 0 和标准差 0.02 的正态分布中随机初始化所有权重。初始化模型后，函数将模型作为输入，并重新初始化所有卷积、卷积转置和批归一化层。

然后，我们使用`torchsummary`库打印模型的摘要；为此，我们将模型与输入维一起传递。方便地查看我们所有的输出尺寸是否正确，并检查每一层中参数的数量和大小。接下来，我们定义损失函数，并使用二进制交叉熵损失，因为只有一个输出具有两个可能的状态，分别表示图像是真实的 1 还是伪造的 0。

我们还创建了固定噪声，用于可视化 GAN 模型在迭代过程中的改进。我们使用`Adam`优化器分别更新了生成器和判别器`goptimizer`和`doptimizer`的权重。然后，我们进行了准备以存储一些模型指标，以查看模型在迭代过程中的变化，然后开始训练循环。

我们遍历了每个小批量并开始训练判别器。我们仅从`MNIST`数据集中获取图像，然后使用`real_images = data[0].to(device)`将其移动到设备中；由于图像全部来自`MNIST`数据集，因此我们知道它们是真实的，因此我们创建了与小批量相同大小的标签向量，并用真实图像标签 1 进行填充。然后，将这些真实图像传递到判别器来预测，然后使用此预测从准则中得出误差`derror_real`并计算梯度。然后，我们创建了相等数量的噪声向量，并将它们传递给生成器以生成图像，然后将这些生成的图像传递给判别器以获取预测，然后从准则`derror_fake`中获取误差。然后，我们再次进行此操作以计算和累积梯度。然后，我们从真实图像和伪图像中获得了误差之和，以得出总的判别器误差，并更新判别器的权重。
