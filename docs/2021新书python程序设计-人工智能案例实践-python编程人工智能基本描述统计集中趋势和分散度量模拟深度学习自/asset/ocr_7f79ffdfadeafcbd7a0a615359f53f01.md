```
In [3]: import imageio

In [4]: mask_image = imageio.imread('mask_heart.png')
```

此函数将掩膜图作为NumPy数组返回，以便WordCloud处理。

# # 配置WordCloud对象

接下来创建和配置WordCloud对象：

```
In [5]: from wordcloud import WordCloud

In [6]: wordcloud = WordCloud(colormap='prism', mask=mask_image,
...     background_color='white')
...
```

除非指定了width和height关键字参数或掩膜图，否则默认WordCloud对象的宽度和高度（以像素为单位）为400×200。如果指定了掩膜图，WordCloud对象的大小就是掩膜图的大小。WordCloud在底层使用Matplotlib，从颜色表中随机分配颜色，也可以通过colormap关键字参数指定颜色。有关颜色表中的颜色名及其对应颜色，请参阅https://matplotlib.org/examples/color/colormaps_reference.html。mask关键字参数指定使用先前加载的mask_image掩膜图。默认情况下，单词是在黑色背景上绘制的，但我们使用background_color关键字参数指定在白色（white）背景上绘制。有关WordCloud关键字参数的完整列表，请参阅http://amueller.github.io/word_cloud/generated/wordcloud.WordCloud.html。

# # 生成词云

WordCloud类的generate方法接收在词云中需使用的文本作为参数，并创建词云，它将作为WordCloud对象返回：

```
In [7]: wordcloud = wordcloud.generate(text)
```

在创建词云之前，generate方法首先通过wordcloud模块的内置停用词表将text中的停用词删除，然后计算剩余单词的词频。默认情况