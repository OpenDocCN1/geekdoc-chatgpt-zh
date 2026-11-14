
![](img/aa01495b859013c80481c58ec0894987_299_0.png)

图 8-29 基于 RNN 的语言模型的展开形式

在图 8-29 中，上文信息(w1, w2, ⋯, w(t−1))通过 RNN 编码为 s(t−2)，它是上一个隐含层，代表着对上文的历史记忆。s(t−1)与当前 w(t)相结合，得到(w1, w2, ⋯, w(t−1), w(t))的表示形式 s(t)，s(t)再通过 RNN 的编码得到预测的输出 y(t)。

从图 8-29 可以看出，只要我们愿意，基于 RNN 的语言模型就可以无限展开历史信息。这意味着，这类模型彻底打破了 N-gram 模型对词窗口大小的限制，从而能更为充分地利用完整的上文信息。相应地，将获得比其他语言模型更好的性能。

Mikolov 的研究显示，即使采用最为基础的 RNN 和最普通的截断 BPTT 优化算法（这里的“截断”表明 RNN 并非无限展开，而是仅展开若干个时间步，这样做的目的在于大幅度减小训练的开销），其性能也比 N-gram 模型的性能好。

从前面的分析可知，RNNLM 可捕获更长的历史信息，从而获得更好的性能，这自然是其优势所在。但 RNNLM 也有其不足之处。因为它使用了 RNN，而我们知道，原生态的 RNN 很容易产生梯度弥散。当然，这个问题已有比较好的解决方案了，那就是前文提到的 RNN 的升级版——LSTM 或 LSTM 的简化版本——GRU。

## 8.12 实战：基于 RNN 的文本情感分类问题

文本分类是自然语言处理的一个常见任务，它把一段不定长的文本序列变换为文本的类别。本书我们尝试完成它的一个子问题，即使用文本情感分类来分析文本背后作者透露的情绪（正面情绪或负面情绪）。该问题也称为情感分析（Sentiment Analysis），它有着广泛的应用。例如，我们可以分析用户对产品（如对电影的评价、对电商的商品评价）的评价，据此统计用户的满意度，或分析用户对股市行情的情绪，并用以预测接下来的股市行情。

官方简称为 IMDb，但很多文献为了方便，全部大写这些字母，即为 IMDB。本书也遵循惯例，采用全部大写模式。

在范例 8-1 中，我们使用经典的数据集 IMDb（Internet Movie Database，互联网电影资料库）作为文本情感分类模型训练所用的数据集[14]，然后训练 RNN 网络，让其判断用户对某一部电影评论的情绪类型（如积极情绪或消极情绪）。下面我们逐步讲解范例的实现过程。

### 8.12.1 数据读取

诸如 IMDB 之类经典的数据集通常都已内置于深度学习框架中，如果没有指定加载路径，只需要使用专门的 API，那么框架会帮助我们自动加载。IMDB 影评数据集包含 50 000 条用户评价，分别包含 25 000 条从 IMDB 下载的关于电影的“积极”和“消极”的评论。标签为“积极”和“消极”的评论数量相等。

在 IMDB 中，评价体系为：其中 IMDB 评级小于 5 的用户评价标注为 0，即消极；IMDB 评价大于或等于 7 的用户评价标注为 1，即积极。数据集分为训练集和测试集，二者各占 50%，25 000 条影评用于训练集，另外 25 000 条影评用于测试集。

为了便于讲解，我们还是采用 Jupyter 的交互模式来逐步讲解代码（参见随书源文件 chap08-IMDB.ipynb。源代码参见范例 8-1 Python 文件 rnn.py）[15]。通过 tf.keras 模块提供的数据集 datasets 工具便可加载 IMDB 数据集，代码如下所示（需要说明的是，给予编号的属于正式代码，没有编号的代码属于解释性或测试性代码）。

### 范例 8-1 利用 RNN 实施文本情绪分类（rnn.py）

```
In [1]:

01    import tensorflow as tf
02    from tensorflow.keras import datasets
03    total_words = 10000   # 设定最常用的单词数量
04    (x_train, y_train), (x_test, y_test) = datasets.imdb.load_data(num_words = total_words)

Downloading data from https://storage.googleapis.com/tensorflow/tf-keras-datasets/imdb.npz
17465344/17464789 [==============================] - 2s 0us/step
```

从上面的输出可知，如果第一次加载这个数据集，那么 datasets.imdb.load_data()会联网下载，所以需要读者具备必要的联网条件才行。如果已经从互联网上下载好这个文件 imdb.npz，那么可以指定加载路径（path）。例如，上述第 04 行代码，可修改为如下代码。

```
04   (x_train, y_train), (x_test, y_test) = datasets.imdb.load_data(
            path = './imdb.npz',
            num_words = total_words)
```

现在，我们着重说明第 04 行的参数 num_words，它被设置为 total_words，其值为 10 000。我们知道英文单词有几十万个，但大部分都是低频词汇，其实使用文档中的前 10 000 个高频词汇已经足够判断文本的情感了。如果保留所有词汇，那么编码空间将会非常大，而性能（情感分类准确率）的提升却非常有限，性价比并不高。最后需要说明的是，参数 num_words 的默认值为 None，即表示所有单词都需要进行编码。

### 8.12.2 感性认知数据

在将数据读入内存后，我们就可以来感性地认识一下数据。首先我们来检查训练集与测试集的维度（样本数量）。

```
In [2]:   #此处代码仅为解释所用，非必需代码，不予编号，下同
print(x_train.shape, y_train.shape)
print(x_test.shape, y_test.shape)
(25000,) (25000,)
(25000,) (25000,)
```

可以看到，x_train 与 x_test 是长度为 25 000 的一维数组，数组的每个元素都是一个列表，用以存储用户的评价语句。但有所不同的是，这些句子并不是我们能直接阅读的英文，而是为了便于计算机处理，对每一个英文单词给予独一无二的编号。这就好比我们每个个体都有一个唯一的身份证号码一样。

例如，训练集的第一个句子共有 218 个单词，测试集的第一个句子共有 68 个单词，每个句子都包含了句子起始标志 ID。空口无凭，如果我们想感性认识这些语句，那么不妨将其打印出来查看，如打印训练集的第一个标签。

```
In [3]: print(x_train[0])  #输出第 1 个用户的评论句子
Out[3]: [1, 14, 22, 16, 43, 530, 973, 1622, 1385, 65, 458, 4468, 66, 3941, 4,
173, 36, 256, 5, 25, 100, 43, 838, 112, 50, 670, 2, 9, 35, 480, 284, 5, 150, 4,
172, 112, 167, 2, 336, 385, 39, 4, 172, ... (省略部分输出) , 22, 21, 134, 476, 26, 480,
5, 144, 30, 5535, 18, 51, 36, 28, 224, 92, 25, 104, 4, 226, 65, 16, 38, 1334, 88,
12, 16, 283, 5, 16, 4472, 113, 103, 32, 15, 16, 5345, 19, 178, 32]
```

如前面分析可知，标签为 “1” 表示 “积极” 情绪。那么这个标签对应的语句到底是什么呢？我们也把它打印出来，即

```
In [4]: y_train[0]   #输出第一个评论的情感标签
Out[4]: 1
```

这样的评论句子输出，可能会令人一头雾水。事实上，这就是一个长度为 218 的句子（用 len(x_train[0])可获得其长度，每个句子都包含了句子起始标志 ID）。

```
In [5]: len(x_train[0])   #输出第 1 条评论的长度
Out[5]: 218
```

那么这些单词是如何编码为数字的呢？我们可以通过查看它的编码表来获得编码方案，代码如下。

```
word_index = datasets.imdb.get_word_index()
Downloading data from https://storage.googleapis.com/tensorflow/tf-keras-datasets/imdb_word_index.json
1646592/1641221 [===============================================] - 1s 1us/step
```

从输出可以看到，我们将获得一个名为 imdb_word_index.json 的文档。JSON 是一种常用的用于数据交换的有特定格式文档。

有了这个文档，我们就可以像查字典一般，罗列出单词和其编码之间的对应关系。

```
In [6]:
for word, code in word_index.items():
    print(word, code)
Out[6]:
fawn 34701
tsukino 52006
nunnery 52007
...(省略大部分输出)
```

为了操作方便，在编码/解码时，还需预留 4 个特殊的 ID，它们表示填充标志、起始标志、未知单词标志和未用标志。由于编码表的键（key）为单词，值（value）为 ID，因此为了构建翻转编码字典，通常先把前 4 个编号保留，分配给这 4 个特殊的标志位，代码如下所示。

```
In [7]:
#利用字典推导式构建单词与 ID 之间的对应表
word_index = {word : (code + 3) for word, code in word_index.items()}
word_index["<PAD>"] = 0 #填充标志
word_index["<START>"] = 1 #起始标志
word_index["<UNK>"] = 2 #未知单词标志
word_index["<UNUSED>"] = 3 #预留，以备后用
# 利用列表推导式构建 ID 与单词之间的翻转编码表
reverse_word_index = dict([(value, key) for (key, value) in word_index.items()])
```

有了翻转编码表 reverse_word_index，我们就可以很容易设计一个函数，将 Out[3]处的数字 ID 转换为字符串，代码如下。

```
In [8]:
def decode_review(text):
    return ' '.join([reverse_word_index.get(i, '?') for i in text])
```

上述代码利用了字典的内置方法 get()，它用于返回指定键（key）的值（value）。若值不在字典中，则返回默认值，这里设置的默认值为“？”。有了上面的铺垫工作，我们可以把前面的数字编码逆向转换为单词。

```
In [9]: decode_review(x_train[0])
Out[9]:
"<START> this film was just brilliant casting location scenery story direction everyone's really suited the part they played and you could just imagine being there robert <UNK> is an amazing actor···（省略大部分输出）···,the whole story was so lovely because it was true and was someone's life after all that was shared with us all"
```

从上面的输出字样 “Amazing” 和 “lovely” 可以大致看出，这句话的情感是积极的。按照约定，我们把积极情感标记为 “1”，消极情感标记为 “0”，这就是为什么 Out[4]输出为 1。此外，我们也看到了<UNK>字样，这表明该单词超过了前面设定的 10 000 个单词之列，属于生僻字。

### 8.12.3 数据预处理

有时，为了便于处理，我们会人为地对参差不齐的文本设置一个阈值，对于大于此长度的句子，选择截断部分单词，即可以选择截去句首单词，也可截去句末单词；对于小于此长度的句子，可选择在句首或句尾填充，这种常用的文本处理功能，Keras 早已替我们考虑到了。我们只需调用函数 keras.preprocessing.sequence.pad_sequences()，即可实现将多个序列截断的功能或将序列补齐为相同长度的功能，代码如下所示。

```
In [10]:
05 from tensorflow import keras #导入Keras 模块
06 max_review_len = 80
07 x_train = keras.preprocessing.sequence.pad_sequences(x_train, maxlen = max_review_len)
08 x_test = keras.preprocessing.sequence.pad_sequences(x_test, maxlen = max_review_len)
```

该函数的原型如下所示。

```
pad_sequences(sequences, maxlen=None, dtype='int32',
padding= 'pre', truncating= 'pre', value=0.0)
```

将一个 num_samples 的序列（整数列表）转化为一个二维 Numpy 矩阵，其尺寸为（num_samples，num_timesteps）。num_timesteps 要么是给定的 maxlen 参数，要么是最长序列的长度（相当于 RNN 展开的时间步）。

具体来说，如果输入序列实际长度比 num_timesteps 所设置的长度短，则将在末端用 value 值补齐；如果输入序列的长度比 num_timesteps 所设置的值长，则将会直接被截断，以满足所需要的长度。补齐或截断发生的位置分别由参数 padding 和 truncating 决定（取值选项为'pre' 或 'post'）。其中向前补齐为默认操作。

文本被截断或填充为相同长度后，它们的类型都是 NumPy 数组，不能被 TensorFlow 直接使用，因此需要将其转换为 TensorFlow 中的 Dataset 对象。之所以使用 Dataset 对象，是因为 Dataset 内置了很多好用的数据预处理方法。

```
In [11]:

09    batchsz = 128
10    db_train = tf.data.Dataset.from_tensor_slices((x_train, y_train))
11    db_train = db_train.shuffle(1000).batch(batchsz, drop_remainder = True)
12    db_test = tf.data.Dataset.from_tensor_slices((x_test, y_test))
13    db_test = db_test.batch(batchsz, drop_remainder = True)
```

我们来简单解释上述代码。第 10 行和第 12 行利用 from_tensor_slices() 的目的在于，分别将 NumPy 类型的张量切分成数据块，这些数据切片可通过 for 循环迭代取出。第 11 行和第 13 行分别利用 batch()将前面生成的可迭代对象打包成小批量，然后分批送给模型训练，而不是一股脑地全部参与训练。这里参数 drop_remainder 值得注意，若将其设置为 True，则表示抛弃分块的零头。例如，数据样本有 307 个，每个批次为 100，经过 batch()处理后，得到 3 个批次，那么剩余的这 7 个样本就抛弃不用了。标准化处理流程很容易为了追求效率，而抛弃一些不是十分重要的细节，可谓“大行不顾细谱”。

下面我们来验证一下截断后和补齐后，训练集和测试集的尺寸，即

```
In [12]: print('x_train shape:', x_train.shape)
Out[12]: x_train shape: (25000, 80)

In [13]: print('x_test shape:', x_test.shape)
Out[13]: x_test shape: (25000, 80)
```

从输出可以看到，因为长度太长而被截断或因为不足而补齐后的句子，其长度都统一为 80，即为前面我们设定的句子长度的阈值。

### 8.12.4 搭建简易 RNN

下面，利用基础的 RNN 来解决情感分类问题。用于文本情感分类的 RNN 拓扑结构如图 8-30 所示。第一层其实是输入数据的预处理层，用作把原始的单词（实际上是单词对应的编码）转化为词向量，有时这类数据转换也称为嵌入层。

![](img/aa01495b859013c80481c58ec0894987_305_0.png)

图 8-30 文本用于情感分类的 RNN 拓扑结构

嵌入层之后的部分才是真正的神经网络。下面我们先尝试设计将这个 RNN 网络的拓扑结果。初步尝试将隐含层数量设置为 2 层。随后看性能如何，再考虑添加更多的层。根据以往经验，网络层数越深，其表达能力越强，故此数据的拟合能力也就越强。但“过犹不及”，网络层数过深也会产生问题，容易导致计算负担过大，且亦可能导致梯度弥散或梯度爆炸。

经过 RNN 的训练后，若想做情感分类，最后一定要有一个全连接层（也称稠密层 Dense）。由于这是一个二分类问题，因此全连接层的输出只需要一个神经元即可。

这里需要再次强调的是，图 8-30 的 RNN 看起来很“胖”，实际上并非如此，它只不过是按照时间训练展开的逻辑上的网络拓扑图而已。事实上，图 8-30 中的 t1 和 t2 是一个网络，不过是在时间维度上展开而已。

一旦训练完毕后，网络的输入权值 U 和输出权值 V 都是相同的，这就是 RNN 参数共享的含义。所不同的是，包含历史信息的隐含层权值 W，会随着时间的推进而不同（可重温图 8-4 获得更全面的理解）。

下面我们用代码实现如图 8-30 所示的 RNN 拓扑结构。在实现代码前，我们需要一些预备知识的铺垫。在前面章节中，我们已经提到，Keras 有三种模型搭建方式，其中第三种方式就是子类模型（Subclassing Model）。即把 tf.keras.Model 当作模型的父类，通过继承，子类可以共享部分父类模型的特征，达到代码复用的目的，然后，我们可以在子类中定义自己个性化的那部分操作。

在继承类中，我们需要重写（Override）两个父类方法：__init__() 和 call(input)。前者为构造器，主要初始化模型所需的层和父类的变量；后者描述数据流动的逻辑拓扑结构，即数据如何通过网络层得到输出，这类似于 TensorFlow 1.x 时代的数据流图。

自然，我们也可以根据需要添加一些父类没有定义的方法。自定义 Keras 模型的类构造示意图如图 8-31 所示。
