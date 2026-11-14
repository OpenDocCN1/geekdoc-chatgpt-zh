
模型，并将其用作创建更强大模型的基础，这个过程称为迁移学习 — 将现有模型的知识转换为新模型。Keras模型中的save方法以HDF5 (Hierarchical Data Format) 的格式存储模型的体系结构和状态信息。默认情况下，此类文件使用.h5文件扩展名：

```
[51]: cnn.save('mnist_cnn.h5')
```

可以使用tensorflow.keras.models模块中的load_model函数加载已保存的模型：

```
from tensorflow.keras.models import load_model
cnn = load_model('mnist_cnn.h5')
```

然后，可以调用其fit函数。例如，如果已获得更多数据，则可以调用predict函数对新数据额外进行预测，或者可以调用fit函数使用额外的数据对该模型开始训练。

Keras还提供了几个附加功能来保存和加载模型的各个方面，更多信息，请参阅https://keras.io/getting-started/faq/#how-can-i-save-a-keras-model。

## 15.7 用TensorBoard可视化神经网络的训练过程

深度学习网络非常复杂，并且存在很多隐藏的内容，因此很难了解并完全理解其所有细节。这为测试、调试和更新模型和算法带来了挑战。深度学习可以学习特征，但可能会有非常多的特征，而且并没有展示给我们。

Google提供了TensorBoard工具 来可视化在TensorFlow和Keras中执行的神经网络，就像用汽车仪表板可视化汽车传感器的数据一样，速度、发动机温度和剩余汽油量都可以显示出来。TensorBoard仪表板可以直观地显示来自深度学习模型的数据，使我们能够深入地了解模型的学习效果，并帮助我们调整其超参数。接下来将介绍TensorBoard。

### 执行TensorBoard

TensorBoard可以监视系统上的文件夹，查找将在Web浏览器中显示的数据文件。我们需要先创建该文件夹，执行TensorBoard服务器，然后通过Web浏览器访问它。执行步骤如下：

- 1. 切换到Terminal、shell或Anaconda命令提示符中的ch15文件夹。
- 2. 确保自定义Anaconda环境tf_env已激活：

```
conda activate tf_env
```

- 3. 执行以下命令来创建名为logs的子文件夹，深度学习模型将在其中写入TensorBoard可视化的信息：

```
mkdir logs
```

- 4. 执行TensorBoard：

```
tensorboard --logdir=logs
```

- 5. 在Web浏览器中访问TensorBoard，网址如下：

```
http://localhost:6006
```

如果在执行任何模型之前连接到TensorBoard，它将首先显示一个页面，并显示No dashboards are active for the current data set。

### TensorBoard仪表板

TensorBoard会监视指定的文件夹，查找模型在训练期间输出的文件。当它看到更新时，会将数据加载到仪表板中。

![](img/bf43b6caad271ba92b343a48c79c2155_746_0.png)

可以在训练时或训练完成后查看数据，上面的仪表板显示的是TensorBoard的SCALARS选项卡，该选项卡显示随时间变化的各个值的变化曲线，例如第一行中显示的是训练准确性（acc）和训练损失（loss），第二行显示的是验证准确性（val_acc）和验证损失（val_loss）。这些图显示了MNIST卷积神经网络的10个epoch，MNIST卷积神经网络位于Jupyter Notebook中的MNIST_CNN_TensorBoard.ipynb笔记本中。这些epoch从0开始沿x轴显示，准确性和损失值显示在y轴上。通过查看训练和验证的准确性，可以在前5个epoch中看到与前一节中的5个epoch类似的运行结果。

对于10-epoch的运行，训练准确性在第9个epoch继续提高，然后略有下降。这可能是开始过拟合的时候，但我们可能需要更长时间的训练才能找到答案。对于验证准确性，可以看到它快速跳跃，然后在稳定了5个epoch之后又迎来了一次上升后下降。对于训练损失，可以看到它迅速下降，然后在第9个epoch持续下降，然后略有增加。验证损失迅速下降然后反弹。我们可以执行更多次的epoch，以查看结果是否有所改善，但基于这些图表，似乎在第6个epoch处得到了训练和验证准确性的最佳组合，并且验证损失最小。

通常，这些图表垂直堆叠在仪表板中，我们可以使用搜索框（在图表上方）显示文件夹名称中包含 “mnist” 的任何文件的内容—稍后将对其进行配置。TensorBoard可以同时从多个模型加载数据，可以选择需要可视化的数据，这样可以轻松比较多个不同模型或对模型进行多个运行。

### 复制MNIST CNN的笔记本

为此示例创建新的笔记本：

- 1. 在JupyterLab的 “File Browser” 选项卡中右键单击 MNIST_CNN.ipynb笔记本，然后选择 “Duplicate” 以复制笔记本。
- 2. 右键单击名为MNIST_CNN-Copy1.ipynb的新笔记本，然后选择 “Rename”，输入 “MNIST_CNN_TensorBoard.ipynb” 并按Enter键。
- 双击名称打开笔记本。

### 配置Keras以写入TensorBoard日志文件

要使用TensorBoard，在fit模型之前，需要配置一个TensorBoard对象（tensorflow.keras.callbacks模块），模型将使用该对象将数据写入TensorBoard监视的指定文件夹，此对象称为Keras中的回调。在笔记本中，单击调用模型fit方法的代码的左侧，然后键入 “a”，这是在当前单元格上方添加新代码单元格的快捷方式（在下方添加时使用 “b” ），在新单元格中，输入以下代码来创建TensorBoard对象：

```
from tensorflow.keras.callbacks import TensorBoard
import time
tensorboard_callback = TensorBoard(log_dir=f'./logs/mnist{time.time()}',
histogram_freq=1, write_graph=True)
```

其中，参数包括：

- log_dir—指定写入此模型的日志文件的文件夹。符号'./logs/'表示在之前创建的日志文件夹中创建了一个新文件夹，按照'mnist'和当前时间进行操作。这可确保Notebook的每次新的执行都有自己的日志文件夹，以便比较TensorBoard中的多个执行。
- histogram_freq—Keras输出到模型日志文件的epoch频率。这里，我们将在每个epoch为日志进行数据写入。
- write_graph—如果是“write_graph=True”，那么将输出模型图，可以在TensorBoard的GRAPHS选项卡中查看图表。

### 更新对fit函数的调用

最后，我们需要在代码段[37]中修改原始的fit函数调用。在这个例子中，我们将epoch的数量设置为10，并且添加了callbacks参数，它是一个回调对象的列表：

```
cnn.fit(X_train, y_train, epochs=10, batch_size=64,
validation_split=0.1, callbacks=[tensorboard_callback])
```

现在可以通过选择Kernel> Restart Kernel and Run All Cells 重新在JupyterLab里执行Notebook。第一个epoch结束后，就可以开始在TensorBoard中查看数据。

## 15.8 ConvnetJS: 基于浏览器的深度学习训练和可视化

在本节中，我们将回顾Andrej Karpathy的基于JavaScript的ConvnetJS工具，用于在Web浏览器（网址见https://cs.stanford.edu/people/karpathy/convnetjs/）中训练和可视化卷积神经网络。

可以运行ConvnetJS的示例卷积神经网络或创建自己的卷积神经网络。我们已经在多个台式机、平板电脑和手机浏览器上使用了该工具。

ConvnetJS的MNIST演示使用15.6节介绍的MNIST数据集训练卷积神经网络。该演示提供了一个可滚动的仪表板，其可在模型训练时动态更新。

### 训练统计

此部分包含一个“Pause”按钮，其可用于停止学习并“冻结”当前仪表板的可视化，暂停演示后，按钮上的文字将更改为“resume”，再次单击该按钮可继续训练。本节还介绍了训练过程的统计数据，包括训练和验证的准确性以及训练损失图。

### 实例化网络和训练器

在本节中，我们将找到创建卷积神经网络的JavaScript代码。默认的网络与15.6节中的CNN有类似的层结构。ConvNetJS文档显示了所支持的神经网络层的类型，以及如何配置它们，可以在提供的文本框中尝试不同的层配置，然后单击“change network”按钮开始训练更新的网络。

### 网络可视化

这个环节一次显示一幅训练图像以及网络如何通过每层处理该图像。单击 “Pause” 按钮可检查给定数字的所有层输出，以了解网络在学习时 “看到” 的内容。网络的最后一层产生概率分类，它显示10个方块—9个黑色和1个白色，用于表示当前数字图像的预测类别。

### 测试集的示例预测

最后一部分显示了测试集图像的随机选择以及每个数字的前三种可能类别，概率最高的显示在绿色条上，另外两个显示在红色条上。每个条的长度是该类概率的视觉指示。

## 15.9 针对序列的递归神经网络：使用 IMDb数据集进行情感分析

在MNIST CNN网络中，我们专注于按顺序应用的堆叠层。非顺序模型也是有可能存在的，正如将在这里看到的递归神经网络。在本节中，我们使用与Keras捆绑的IMDb（互联网电影数据库）影评数据集来进行二分类，预测给定评论的情绪是积极的还是消极的。

这里使用递归神经网络（RNN）来处理序列数据，例如句子中的时间序列或文本。术语“递归”来自这样的事实：神经网络包含循环，其中给定层的输出在下一个时间步长中成为同一层的输入。在时间序列中，时间步长是下一个时间点。在文本序列中，“时间步长”是单词序列中的下一个单词。

RNN中的循环使它们能够学习并记住序列中数据之间的关系。例如，考虑第11章中使用的以下句子

```
The food is not good.
```

显然有负面情绪。同样，这句话

```
The movie was good.
```

有积极的情绪，虽然没有下面这句话积极：

```
The movie was excellent!
```

在第一句中，“good”这个词本身就具有积极的情绪，然而，当句子中较早出现“not”时，情绪就变为负面。RNN可以考虑句子中前部和后部之间的关系。

在前面的例子中，确定情绪的词是相邻的。但是，在确定文本的含义时，可以考虑很多单词，而且在确定情绪的词之间可以有任意数量的单词。在本节中，我们将使用长短时记忆（LSTM）层，使神经网络循环并从上面描述的句子中学习以进行优化。

RNN已被用于很多任务，包括：

- 预测文本输入—在键入单词时显示可能的下一个单词；
- 情感分析；
- 基于语料库，用预测出的最佳答案回答问题；
- 跨语言翻译；
- 视频中的可隐藏字幕。

### 15.9.1 加载IMDb影评数据集

Keras捆绑的IMDb影评数据集包含25,000个训练样本和25,000个测试样本，每个样本都标有正（1）或负（0）的情绪标签。下面导入tensorflow.keras.datasets.imdb模块来加载数据集：

```
[1]: from tensorflow.keras.datasets import imdb
```

imdb模块的load_data函数返回IMDb训练集和测试集，数据集中有超过88,000个独一无二的单词。使用load_data函数可以指定要作为训练数据和测试数据的样本数目。这里，由于系统的内存限制以及我们（有意）在CPU而不是GPU上进行训练这一事实，因此只加载了最常出现的10,000个单词（因为大多数读者都无法访问到具有GPU和TPU的系统），加载的数据越多，训练时间就越长，但更多数据可能会有助于生成更好的模型：

```
[2]: number_of_words = 10000
[3]: (X_train, y_train), (X_test, y_test) = imdb.load_data(num_words=number_of_words)
```

load_data函数返回包含训练集和测试集两个元素的元组，每个元素本身也是一个包含样本和标签的元组。在给定的评论中，load_data用占位符替换了前10,000个单词之外的所有单词，稍后将讨论。

### 15.9.2 数据探索

下面来检查训练集样本（x_train）、训练集标签（y_train）、测试集样本（x_test）和测试集标签（y_test）的维度：

```
[4]: X_train.shape
[4]: (25000,)
[5]: y_train.shape
[5]: (25000,)
[6]: X_test.shape
[6]: (25000,)
[7]: y_test.shape
[7]: (25000,)
```

数组y_train和y_test是包含“1”和“0”的一维数组，表示每个评论是正面的还是负面的。基于前面的输出结果，X_train和X_test看起来也是一维的，但其实它们的元素是整数列表，每个列表代表一条评论的内容，如代码段[9]所示：

```
[8]: %pprint
[8]: Pretty printing has been turned OFF
[9]: X_train[123]
9: [1, 307, 5, 1301, 20, 1026, 2511, 87, 2775, 52, 116, 5, 31, 7, 4,
91, 1220, 102, 13, 28, 110, 11, 6, 137, 13, 115, 219, 141, 35, 221, 956,
54, 13, 16, 11, 2714, 61, 322, 423, 12, 38, 76, 59, 1803, 72, 8, 2, 23,
5, 967, 12, 38, 85, 62, 358, 99]
```

Keras深度学习模型需要用数值数据，因此Keras对IMDb数据集进行了预处理。

### 影评编码

由于影评是数值编码的，要查看其原始文本，需要知道每个数值对应的单词。Keras的IMDb数据集提供了一个字典，其可以将单词映射到自身的索引，每个单词的对应值是其在整个评论集中所有单词里的出现频率排名，因此，排名为1的单词是最常出现的单词（由Keras从数据集中计算得出），排名为2的单词是第二个最常出现的单词，以此类推。

尽管字典值以1开头并将其作为最常出现的单词，但在每个编码的评论中（如前面所示的X_train[123]），排名值会偏移3，因此，不管是出现在评论中的哪个地方，任何包含最常出现的单词的评论中都将包含值“4”。Keras在每个编码后的评论中保留0、1和2，用于以下目的：

- 评论中的值0表示填充。Keras深度学习算法希望所有的训练样本具有相同的维度，因此一些评论可能需要扩展到给定长度并且一些评论需要缩短到该长度，需要扩展的评论就用0填充。
- 值1表示Keras的内部指令，指示用于学习为目的的文本序列的开始。
- 评论中的值“2”表示一个未知单词，通常是未加载的单词，通过num_words参数调用load_data就会出现这种情况，任何包含出现频率排名大于num_words的单词的评论，其数值都会被替换为2，这些都是由Keras在加载数据时处理的。

由于每个评论的数值都偏移了3，在解码评论时必须考虑到这一点。

### 影评解码

下面来解码一个评论。首先，通过从tensorflow.keras.datasets.imdb模块中调用get_word_index函数来获取word-to-index字典：

```
[10]: word_to_index = imdb.get_word_index()
```

“great” 这个词可能出现在正面的电影评论中，所以我们来看看它是否在字典中：

```
[11]: word_to_index['great']
[11]: 84
```

根据输出结果，“great” 是数据集的第84个最常用词。如果查找一个不在字典中的单词，会得到一个例外。

为了将频率等级转换为单词，首先反转word_to_index字典的映射，这样就可以通过频率等级查找每个单词。以下字典理解反转了映射：

```
[12]: index_to_word = \
[index: word for (word, index) in word_to_index.items()]
```

回想一下，字典的items方法能够迭代键-值对的元组。我们将每个元组解压到变量word和index中，然后在新字典中使用表达式index: word创建一个条目。

以下列表理解从新词典获取前50个单词—回想一下，最常见的单词的值为1：

```
[13]: [index_to_word[i] for i in range(1, 51)]
[13]: ['the', 'and', 'a', 'of', 'to', 'is', 'br', 'in', 'it', 'i',
 'this', 'that', 'was', 'as', 'for', 'with', 'movie', 'but', 'film', 'on',
 'not', 'you', 'are', 'his', 'have', 'he', 'be', 'one', 'all', 'at', 'by',
 'an', 'they', 'who', 'so', 'from', 'like', 'her', 'or', 'just', 'about',
 'it’s', 'out', 'has', 'if', 'some', 'there', 'what', 'good', 'more']
```

请注意，其中大部分都是停用词。根据应用的不同，可能希望删除或保留停用词。例如，如果正在创建一个预测文本应用，为用户正在键入的句子建议下一个单词，这时候就会想保留停用词以便把它们显示为预测结果。

现在，我们可以来解码评论，使用index_to_word字典的双参数方法get，而不是用[]运算符来获取每个键的值。如果某个值不在字典中，则get方法返回其第二个参数，而不是引发异常。参数i-3解释了每个评论的频率等级的编码偏移。当Keras的保留值0～2出现在评论中时，get返回'？'；否则，get在index_to_word字典中使用键i-3返回单词：

```
[14]: ' '.join([index_to_word.get(i - 3, '?') for i in X_train[123]])
[14]: '? beautiful and touching movie rich colors great settings good acting and one of the most charming movies i have seen in a while i never saw such an interesting setting when i was in china my wife liked it so much she asked me to ? on and rate it so other would enjoy too'
```

可以从y_train数组中看到此评论被归类为正面：

```
[15]: y_train[123]
[15]: 1
```

### 15.9.3 数据准备

每个评论中的单词数量各不相同，但Keras要求所有样本具有相同的尺寸。因此，需要执行一些数据准备，将每个评论限制为相同数量的单词。有些评论需要填充其他数据，而有些评论则需要被截断。pad_sequences实用函数（模块tensorflow.keras.preprocessing.sequence）将X_train的样本（即其列数）变换为maxlen参数（200）指定的特征数，并返回一个二维数组：
