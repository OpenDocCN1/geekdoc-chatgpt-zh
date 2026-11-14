# 10.6.4 利用Matplotlib显示扑克牌图像

到目前为止，我们都是以文本形式显示扑克牌的。现在，我们来看一下如何以图像形式显示。为了展示程序，我们需要从Wikimedia Commons (https://commons.wikimedia.org/wiki/Category:SVG_English_pattern_playing_cards) 下载public-domain扑克牌图像。

这些扑克牌图像放在了本书ch10文件夹的card_images子文件夹中。首先，我们创建一个DeckOfCards对象：

```
In [1]: from deck import DeckOfCards
In [2]: deck_of_cards = DeckOfCards()
```

# 在IPython中使用Matplotlib

下面，在IPython中通过使用%matplotlib魔术命令启动Matplotlib支持：

```
In [3]: %matplotlib
Using matplotlib backend: Qt5Agg
```

# 为每幅图像创建基路径

在显示每幅图像前，必须从card_images文件夹中加载图像。我们使用pathlib模块的Path类构造每幅图像在我们系统中的完整路径。代码段[5]为当前文件夹（即ch10文件夹）创建了一个Path对象，其表示为'.'。然后使用Path的joinpath方法追加包含扑克牌图像的子文件夹：

```
In [4]: from pathlib import Path
In [5]: path = Path('.').joinpath('card_images')
```

# 导入Matplotlib功能