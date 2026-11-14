subplots函数将Axes对象以二维NumPy数组的形式返回，如下图所示，每个子图的x轴和y轴上都有标签（将被删除）。

![](bbox=[0.233, 0.163, 0.766, 0.436])

# 显示每个图像并删除轴标签

接下来，使用带有内置zip函数的for语句对这24个Axes对象、digits.images中的前24个图像和digits.target中的前24个值并行迭代：

```
In [11]: for item in zip(axes.ravel(), digits.images, digits.target):
    ...:     axes, image, target = item
    ...:     axes.imshow(image, cmap=plt.cm.gray_r)
    ...:     axes.set_xticks([]) # remove x-axis tick marks
    ...:     axes.set_yticks([]) # remove y-axis tick marks
    ...:     axes.set_title(target)
    ...: plt.tight_layout()
    ...:
    ...:
```

回想一下，NumPy的数组方法ravel方法会为多维数组创建一维视图，zip函数会生成包含其中参数的元组，并且具有最少元素的参数决定了zip函数返回的元组数。

循环迭代：