
```python
# 创建生成网络
self.GA = NET_G(image_size = image_size, ngf = ngf, block_n = resnet_blocks)
self.GB = NET_G(image_size = image_size, ngf = ngf, block_n = resnet_blocks)
```

```python
# 创建判别网络
self.DA = NET_D(image_size = image_size, ndf = ndf)
self.DB = NET_D(image_size = image_size, ndf = ndf)
```

```python
# 获取真实、伪造和复原的A类图和B类图变量
realA, realB = self.GB.inputs[0], self.GA.inputs[0]
fakeB, fakeA = self.GB.outputs[0], self.GA.outputs[0]
recA, recB = self.GA([fakeB]), self.GB([fakeA])
```

```python
# 获取由真实图片生成伪造图片和复原图片的函数
self.cycleA = K.function([realA], [fakeB, recA])
self.cycleB = K.function([realB], [fakeA, recB])
```

```python
# 得到判别网络判别真实图片和伪造图片的结果
DrealA, DrealB = self.DA([realA]), self.DB([realB])
DfakeA, DfakeB = self.DA([fakeA]), self.DB([fakeB])
```

```python
# 用生成网络和判别网络的结果计算损失函数
lossDA, lossGA, lossCycA = self.get_loss(DrealA, DfakeA, realA, recA)
lossDB, lossGB, lossCycB = self.get_loss(DrealB, DfakeB, realB, recB)
lossG = lossGA + lossGB + lambda_cyc * (lossCycA + lossCycB)
lossD = lossDA + lossDB
```

```python
# 获取参数更新器
updaterG = Adam(lr = lrG, beta_1=0.5).get_updates(self.GA.trainable_weights + self.GB.trainable_weights, [], lossG)
updaterD = Adam(lr = lrD, beta_1=0.5).get_updates(self.DA.trainable_weights + self.DB.trainable_weights, [], lossD)
```

```python
# 创建训练函数，可以通过调用这两个函数来训练网络
self.trainG = K.function([realA, realB], [lossGA, lossGB, lossCycA, lossCycB], updaterG)
self.trainD = K.function([realA, realB], [lossDA, lossDB], updaterD)
```

```python
def get_loss(self, Dreal, Dfake, real, rec):
    """
    获取网络中的损失函数
    """
    lossD = loss_func(Dreal, K.ones_like(Dreal)) + loss_func(Dfake, K.zeros_like(Dfake))
    lossG = loss_func(Dfake, K.ones_like(Dfake))
    lossCyc = K.mean(K.abs(real - rec))
    return lossD, lossG, lossCyc

def save(self, path="./models/model"):
    """
    保存模型
    """
    self.GA.save("{} - GA.h5".format(path))
    self.GB.save("{} - GB.h5".format(path))
    self.DA.save("{} - DA.h5".format(path))
    self.DB.save("{} - DB.h5".format(path))

def train(self, A, B):
    """
    用于训练的接口，输入A和B两张图片，返回训练之后得到的误差
    """
    errDA, errDB = self.trainD([A, B])
    errGA, errGB, errCycA, errCycB = self.trainG([A, B])
    return errDA, errDB, errGA, errGB, errCycA, errCycB
```

### (五) 训练代码

这里，我们使用snapshot 函数，用于在训练的过程中生成预览效果。

```python
def gen(generator, X):
    """
    用于生成效果图
    """
    r = np.array([generator([np.array([x])]) for x in X])
    g = r[:, 0, 0]
    rec = r[:, 1, 0]
    return g, rec

def snapshot(cycleA, cycleB, A, B):
    """
    产生一个快照

    A、B是两个图像列表
    cycleA是 A->B->A的一个循环
    cycleB是 B->A->B的一个循环
    """
```

```
输出一张图像：
+-----+ +-----+
| X (in A) | ... | Y (in B) | ...
+-----+ +-----+
| GB(X) | ... | GA(Y) | ...
+-----+ +-----+
| GA(GB(X)) | ... | GB(GA(Y)) | ...
+-----+ +-----+
...
```

```python
gA, recA = gen(cycleA, A)
gB, recB = gen(cycleB, B)

lines = [
    np.concatenate(A.tolist()+B.tolist(), axis = 1),
    np.concatenate(gA.tolist()+gB.tolist(), axis = 1),
    np.concatenate(recA.tolist()+recB.tolist(), axis = 1)
]

arr = np.concatenate(lines)
return arr2image(arr)
```

```python
# 创建模型
model = CycleGAN(image_size = IMG_SIZE)
```

```python
# 训练代码
```

```python
# 先记下开始时间
start_t = time.time()
```

```python
# 训练轮数
EPOCH_NUM = 100
```

```python
# 已经训练的轮数
epoch = 0
```

```python
# 迭代几次输出一次训练信息（误差）
DISPLAY_INTERVAL = 5
```

```python
# 迭代几次保存一个快照
SNAPSHOT_INTERVAL = 50
```

```python
# 迭代几次保存一次模型
SAVE_INTERVAL = 200
```

```python
# 批大小
BATCH_SIZE = 1
```

```python
# 已经迭代的次数
iter_cnt = 0
```

```python
# 用于记录误差的变量
err_sum = np.zeros(6)

while epoch < EPOCH_NUM:
    # 获取数据
    epoch, A, B = train_batch(BATCH_SIZE)

    # 训练
    err = model.train(A, B)

    # 累计误差
    err_sum += np.array(err)

    iter_cnt += 1

    # 输出训练信息
    if (iter_cnt % DISPLAY_INTERVAL == 0):
        err_avg = err_sum / DISPLAY_INTERVAL
        print('[迭代%d] 判别损失: A %f B %f 生成损失: A %f B %f 循环损失: A %f B %f' % (iter_cnt,
        err_avg[0], err_avg[1], err_avg[2], err_avg[3], err_avg[4],
        err_avg[5]))
    err_sum = np.zeros_like(err_sum)

    # 产生快照
    if (iter_cnt % SNAPSHOT_INTERVAL == 0):
        A = train_A.get_pics(4)
        B = train_B.get_pics(4)
        snapshot(model.cycleA, model.cycleB, A,
        B).save("./snapshot/{}.png".format(iter_cnt))

    # 保存模型
    if (iter_cnt % SAVE_INTERVAL == 0):
        model.save(path = "./models/model-{}".format(iter_cnt))
```

### 四、结果展示

以莫奈的画为A类图像,以真实的照片为B类图像进行训练(数据集可以在https://people.eecs.berkeley.edu/~taesung_park/CycleGAN/datasets/下载)。经过训练之后,使用训练集之外的图像进行测试，结果如下：

![](img/9b291315612c9c478ad54afa29381fce_96_0.png)

CycleGAN效果图

对比莫奈的原作，你会发现AI绘制出来的图像确实有几分莫奈的神韵。

![](img/9b291315612c9c478ad54afa29381fce_96_1.png)

莫奈的作品

### 思考与实践

7.1 在 CycleGAN 的训练过程中，风格图像哪些特征（例如颜色、纹理、几何形状等）是最容易被学习到的，哪些特征是较难被学习到的？

7.2 使用 CycleGAN 可以完成许多类型图像的转换，思考 CycleGAN 是否适合这些任务。你如果感兴趣可以自行寻找数据集训练。

- a. 马和斑马的照片之间的转换。

![](img/9b291315612c9c478ad54afa29381fce_97_0.png)

可能出现的反色现象

- b. 一处景点冬季和夏季照片的转换。
- c. 苹果和橘子的转换。
- d. 猫和狗的转换。
- e. 把照片转换为毕加索风格的绘画。

7.3 (*) 在训练 CycleGAN 的过程中，我们发现有时候会出现“反色”现象，思考造成这种现象的原因。

### 本部分小结

在本部分中，我们了解了什么是图像风格迁移，并尝试使用K近邻算法模仿图像的色彩风格。然后学习了如何使用卷积神经网络来提取图像中的内容表示和风格表示，并使用梯度下降法融合不同图像的风格。最后我们介绍了一种生成对抗网络——CycleGAN。在此过程中，我们对神经网络有了更深的理解。

图像风格迁移是一个很有趣的问题。本部分介绍的算法还有很大的提升空间，你如果有兴趣可以作进一步的思考探究。

## 第 3 部分 文本生成

在本部分中，我们将要学习一些基本的文本生成技术。与之前的图像识别、图像风格迁移不同，本部分要处理的问题属于自然语言处理（Natural Language Processing）领域，处理的基本对象不是图片，而是文本。

文本生成是比较学术的说法，通常人们所说的“人工智能写作”“人工智能作诗”“自动对话系统”等，都属于文本生成的范畴。简单来说，文本生成就是给定一段初始的文本，然后自动生成一段与之相关的文本。当然，具体生成什么样的文本需要视需求而定。

文本生成是自然语言处理领域的一个核心问题，在现实生活中也有着许多应用。2017 年 5 月，微软推出了微软小冰原创诗集《阳光失了玻璃窗》，这是人类历史上第一部 100% 由人工智能创作的诗集。

![](img/9b291315612c9c478ad54afa29381fce_98_0.png)

在本部分中，我们将了解解决文本生成问题的大致步骤，学习文本生成问题是如何转化为一个可计算的分类问题的，然后逐步深入，学习几种常见的文本生成算法——n 元语法语言模型（n-gram Language Model），长短期记忆网络（Long Short-Term Memory）和 seq2seq（序列对序列）。目标是逐步建立一个能够“吟诗作对”的文本生成模型，给出上句诗，它能够输出下句诗。当然，它能做的事情不止于此，如输入开头的一句话，可以让它生成一段小说、一段影评。

人们在广场上游戏
太阳不知疲倦
我再三脚踢
想象却敛起了眉
她飞进天空的树影
便迷路在人群里了
那是梦的翅膀
正如旧时的安逸

而人生是萍水相逢
在不提防的时候降临
你和我一同住在我的梦中
偶然的梦
这样的肆意并不常见
用一天经历一世的欢喜
——微软小冰原创诗歌，发表于《华西都市报·浣花溪》，2017 年 12 月 16 日

## 第八章 文本生成基础知识

在本章中，我们将要学习文本生成的大致步骤和统计语言模型。通过本章学习，掌握文本生成的基本流程和将文本生成转化为分类问题的基本策略，体会对实际问题进行数学建模的重要意义。

### 一、文本生成的大致步骤

（1）任何计算机学习任务，首要解决的问题就是数据。如前面所说，文本生成是一个很宽泛的问题，因此我们需要根据自己的需求选定相关数据。以古诗生成的具体问题为例，我们从各种资料库里获取的有关古诗的信息，通常都会包含一些对于诗歌创作来说无效的信息，如诗名、作者、朝代等，需要将其删除。另外，我们的任务是实现上句对下句，因此要把所有的上下句整理成对，形成格式化文本，这样便得到了初步的数据。

| 原始文本 | 格式化文本 |
| :--- | :--- |
| 登鹳雀楼【作者】王之涣<br>白日依山尽，黄河入海流。欲穷千里目，更上一层楼。 | [[“白日依山尽”， “黄河入海流”],<br>[“欲穷千里目”， “更上一层楼”]] |
| 江雪【作者】柳宗元<br>千山鸟飞绝，万径人踪灭。孤舟蓑笠翁，独钓寒江雪。 | [[“千山鸟飞绝”， “万径人踪灭”],<br>[“孤舟蓑笠翁”， “独钓寒江雪”]] |
| 静夜思【作者】李白<br>床前明月光，疑是地上霜。举头望明月，低头思故乡。 | [[“床前明月光”， “疑是地上霜”],<br>[“举头望明月”， “低头思故乡”]] |

（2）在数据整理完毕后，根据具体的问题选择合适的算法，进行训练与参数的调整（具体的模型与算法将在后面章节学习）。下表是seq2seq模型的训练过程。

| 输入 | 未训练结果 | 训练1/3结果 | 训练2/3结果 | 训练3/3结果 |
| :--- | :--- | :--- | :--- | :--- |
| 白日依山尽 | 流泼据披辟缙宥己承承 | 山风满水深 | 青山一夜深 | 青山去路长 |
| 孤舟蓑笠翁 | 葵璜瓒墨蜴峙许蜴麛焦 | 日日照江水 | 一叶落花花 | 独立江南月 |
| 床前明月光 | 简筒壮袂郐刀枚邱霾塞 | 日日满江阳 | 日暮云中雨 | 水下清风流 |

（3）使用所得模型或算法生成文本，并进行评估。

### 二、问题的转化——分类问题

> 科学并不尝试去解释，它们甚至不怎么去说明，它们主要做的就是建立模型。
> ——冯·诺依曼

人工智能技术的高度发展，使计算机好像有了“视觉”，有了“听觉”，甚至能够在某些看起来只有人能完成的任务上体现出超越人类的水平，但事实上，并不是计算机真的能听会看了，而是计算机科学家们对现实世界的各种实体进行数学建模，利用计算机的计算能力处理了这些问题。“给出上句诗，输出下句诗”这一问题是很抽象的，计算机无法理解，只有对其进行数学建模，才能让计算机帮助我们解决这一问题。

在之前的图像识别、图像风格迁移任务中，为了能够让计算机处理图片，我们利用了像素矩阵和RGB色彩模型将一张图片转化为一个浮点数矩阵后进行计算，这种矩阵格式对于图片来说是自然的。现在我们面临的原始数据是文本，同样需要对文本进行数学建模，使文本能够被计算，从而将文本生成问题转化为计算机可解决的问题。

这里我们引入一个重要的模型——统计语言模型(Statistical Language Model)，通过这个模型可将文本生成的问题转化为一个分类问题。

#### 1. 统计语言模型与分类问题

> 在终极的分析中，一切知识都是历史；
> 在抽象的意义上，一切科学都是数学；
> 在理性的世界里，所有的判断都是统计学。
> ——C. R. Rao《统计与真理》

如果把在数据中出现过的所有字都放到词汇集合 V 中，这样任何一句话都可以表示成词汇集合中若干词构成的序列。

首先假设，词汇集合大小是有限的，即 V 是一个有限集合，这当然是符合实际的。所谓统计语言模型，就是估计给定前 m 个词以后，第 m+1 个词在整个词汇集合 V 上的概率分布，即 P ( w_{m+1} ∈ V | w_1, w_2, …, w_n )，这是一个条件概率。

假如已经通过某种算法计算出了 P ( w_{m+1} ∈ V | w_1, w_2, …, w_m )，那文本生成就自然地转化成了一个分类问题，即先把每个词汇视为一种类别，于是整个词汇集合就成了类别标签的全集，我们给出关于第 m+1 个词的已知信息（即前 m 个词分别是什么），然后预估第 m+1 个词属于哪一类（即哪一个词），只要选出使得条件概率最大（即可能性最大）的那个 w_{m+1} 作为我们的预测结果就可以了。

#### 条件概率

设$A$与$B$为样本空间 $\Omega$ 中的两个事件，其中$P(B) > 0$。那么在事件$B$发生的条件下，事件$A$发生的条件概率为：

\(P(A \mid B) = \frac{P(A \cap B)}{P(B)}\)

条件概率有时也称作后验概率。

这就像下图所示的使用智能文字输入法软件输入文字一样，用户输入了几个词，软件会猜测并显示接下来最有可能输入的词是什么，供用户选择。

![](img/9b291315612c9c478ad54afa29381fce_102_0.png)

输入法对于下一个词的提示

#### 2. “读书破万卷，下笔如有神”

用什么方法可以计算$P(w_{m+1} \in V \mid w_1, w_2, \dots, w_m)$ 呢？

我们通过让计算机“阅读”大量的文本，应用统计的方法学习语料中词汇的分布模式去估计这个条件概率。这些文本我们称之为语料（corpus）。在语料中，计算机可以“认识到”，当某个词出现的时候，哪些与之关联的词也会有很高的频率出现、某个固定的位置上总是出现若干个固定的词等等如此类的经验规律，从而能够生成接近真实语料的文本。

举个例子，想要让计算机学会“吟诗作对”，就让计算机阅读《唐诗宋词鉴赏》《唐诗三百首》等大量文本，学习人类写诗的方式和规则（比如平仄相对、词性相对、意境相合）。杜甫诗云：“读书破万卷，下笔如有神。”人写好诗需要大量读诗，计算机其实也是这个道理。

之前所说的输入法软件，一方面会根据所有用户的输入记录来对词汇的概率分布有一个预先的认识，一方面也会不断地学习单个用户本身的输入习惯来适应不同的用户。
