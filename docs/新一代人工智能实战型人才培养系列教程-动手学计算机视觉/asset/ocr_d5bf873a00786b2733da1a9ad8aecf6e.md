![](bbox=[0.42, 0.1, 0.58, 0.19])

![](bbox=[0.36, 0.21, 0.64, 0.41])

可以看出图像变得平滑了，但噪声依旧存在。我们增大均值滤波器的大小，并再次测试。

```
# 均值滤波器 size=5
size = 5
k5 = 1/size/size * np.ones((size,size))

conv = conv_2d(img_gaussian, k5)

# 展示卷积结果
conv.plot_conv()

# 均值滤波器 size=9
size = 9
k9 = 1/size/size * np.ones((size, size))

conv = conv_2d(img_gaussian, k9)
# 展示卷积结果
conv.plot_conv()
```

![](bbox=[0.36, 0.73, 0.64, 0.92])