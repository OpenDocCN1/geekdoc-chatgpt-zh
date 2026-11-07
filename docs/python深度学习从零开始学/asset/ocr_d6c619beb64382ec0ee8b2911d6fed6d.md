# Python 深度学习从零开始学

```
validation_dir = os.path.join(base_dir, 'validation_set')
train_cats_dir = os.path.join(train_dir, 'cats')
train_dogs_dir = os.path.join(train_dir, 'dogs')
validation_cats_dir = os.path.join(validation_dir, 'cats')
validation_dogs_dir = os.path.join(validation_dir, 'dogs')
train_cat_fnames = os.listdir(train_cats_dir)
train_dog_fnames = os.listdir(train_dogs_dir)
import matplotlib.pyplot as plt
import matplotlib.image as mpimg
# 输出图表的参数，将以 4×4 的配置输出猫狗数据集的部分图片
nrows = 4
ncols = 4
# 迭代图像的当前索引
pic_index = 0
# 设置 matplotlib（Python 的 2D 绘图库）图，并将其设置为适合 4×4 图片大小
fig = plt.gcf()
fig.set_size_inches(ncols * 4, nrows * 4)
pic_index += 8
next_cat_pix = [os.path.join(train_cats_dir, fname)
                for fname in train_cat_fnames[pic_index-8:pic_index]]
next_dog_pix = [os.path.join(train_dogs_dir, fname)
                for fname in train_dog_fnames[pic_index-8:pic_index]]
for i, img_path in enumerate(next_cat_pix+next_dog_pix):
    # 设置子图，子图的索引从 1 开始
    sp = plt.subplot(nrows, ncols, i + 1)
    # 不显示轴（网格线）
    sp.axis('Off')
    img = mpimg.imread(img_path)
    plt.imshow(img)
plt.show()
```

输出的实际效果如图 8-45 所示，每次重新运行都会查看到新的一批图片。

![](bbox=[0.19, 0.68, 0.81, 0.9])

图 8-45