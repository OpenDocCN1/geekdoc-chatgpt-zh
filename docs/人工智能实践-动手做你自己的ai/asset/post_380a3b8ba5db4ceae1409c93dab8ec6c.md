
## 二、过去、现在与未来——循环神经网络

### 1. 出发点：将历史包含进来

> 在永生者之间，
> 每一个举动（以及每一个思想）都是遥远的过去已经发生过的举动和思想的回声，
> 或者是将在未来屡屡重复的举动和思想的准确的预兆。
> ——博尔赫斯《永生》

n元语法模型利用了马尔可夫性质，这也恰是其问题所在，因为在预测下一个词是什么的时候，只考虑固定长度的前若干个词明显是不够的。在传统的深度神经网络中，输入层接受输入，经过隐层的计算得到输出。循环神经网络则将考虑历史信息的想法与神经网络相结合，在每一个时刻 t，神经元接受的输入不只有原输入 x，还有上一个时刻的神经元的状态 s_{t-1}，这样某一个时刻 t 的信息就可以沿着时间线一直传播，在预测每个时刻的输出的时候，历史信息也自然而然地被包含进来了。

![](img/9b291315612c9c478ad54afa29381fce_114_0.png)

- 上图是一个典型的循环神经网络结构图，其中符号解释如下：
  - x_t 是 t 时刻的输入，在我们的场景中，它就是第 t 个词的词向量。
  - s_t 是 t 时刻的状态，在之前深度神经网络的学习中我们知道，s_t = f(Ux_t + b)，其中 f 是激活函数，U 是权重矩阵，b 是偏差，而循环神经网络在这里作了修改，即在循环神经网络中，s_t = f(Ux_t + W s_{t-1})，这样就把上一个时刻的状态 s_{t-1} 考虑进来了。
  - o_t 是 t 时刻的输出，这里和深度神经网络并没有区别，o_t = softmax(Vs_t)。

循环神经网络的想法并不复杂，在此基础上人们又对循环神经网络作了很多改进，现在人们使用的循环神经网络模型几乎全都是改进版本了，这其中就有大名鼎鼎的长短期记忆网络（Long-Short Term Memory，LSTM）。

### 2. 改进：长短期记忆网络

长短期记忆网络保持了循环神经网络的基本结构，只是在神经元内部引入了门控结构来控制神经网络对于信息的遗忘与记忆。

![](img/9b291315612c9c478ad54afa29381fce_115_0.png)

不同于传统循环神经网络只在不同时刻之间传递一个 h_t（隐状态，hidden state），长短期记忆网络在上一个时刻与下一个时刻传递的状态有两个，c_t（细胞状态，cell state）和 h_t。

简单来说，长短期记忆网络神经元的内部计算可以分为 3 个阶段。

首先是选择遗忘阶段，由门 f_t 进行控制，决定对 c_{t-1} 中的哪些信息进行遗忘。具体来说，先由 f_t = σ(W_f [h_{t-1}, x_t] + b_t) 产生门控信号，再通过 f_t ∘ c_{t-1} 得到选择遗忘结果（其中 σ 指 sigmoid 运算，[] 指向量的拼接，∘ 运算指哈达玛积，下同）。可以看到，经由 sigmoid 运算，f_t 是一个各个维度的值都在 (0, 1) 之间的向量，从而在与 c_{t-1} 进行哈达玛积运算时，可以对 c_{t-1} 各个维度的数值进行强度控制，这就是 f_t 称为门控信号的原因，之后的其他门也是一样的。

### 概念解析

### 哈达玛积

哈达玛积（Hadamard product），又称舒尔积或逐项积。

若两个矩阵 A 和 B 具有相同的维度 m × n，则它们的哈达玛积 A ∘ B 是一个具有相同维度的矩阵，其元素值为：

(A ∘ B)_{ij} = (A)_{ij}(B)_{ij}

对于维度不相同的矩阵，哈达玛积没有定义。

哈达玛积被应用于有损图片压缩，同时也被应用于门控循环神经网络的结构描述。

其次是选择记忆阶段，由门 i_t 进行控制，决定对输入 x_t 中的信息的记忆情况，其输出与经过选择遗忘的 c_{t-1} 相结合得到这个时刻的输出状态 c_t。具体来说，由 i_t = σ(W_i [h_{t-1}, x_t] + b_i) 产生门控信号，同时用 c̄ = tanh(W_c [h_{t-1}, x_t] + b_c) 产生输入信号，再通过 i_t ⊙ c̄ 得到选择记忆结果，并与遗忘门的结果通过 c_t = f_t ⊙ c_{t-1} + i_t ⊙ c̄ 得到下一个时刻的细胞状态。需要注意的是，这里不是采用 sigmoid 而是取值范围为 (-1, 1) 的 tanh 运算，是因为 c̄ 并不是门控信号。

最后是输出阶段，由门 o_t 进行控制，将输入 x_t 与 c_t 结合得到输出 h_t。具体来说，由 o_t = σ(W_o [h_{t-1}, x_t] + b_o) 产生门控信号，接着通过 h_t = o_t ⊙ tanh(c_t) 得到下一个时刻的隐状态。

长短期记忆网络的计算比传统循环神经网络要复杂许多，现在主流的机器学习框架都已经封装好了它的实现，我们可以利用其方便地搭建一个长短期记忆网络。

### 3. 使用 Keras 搭建一个简单的长短期记忆网络

这一节中我们用 Keras 框架搭建一个简单的长短期记忆网络。

#### (1) 加载必要的库。

```python
# 如果没有安装 keras 和 tensorflow 库
# 请使用 pip install keras tensorflow 安装
import itertools
import jieba
import numpy as np
from collections import Counter
from keras.models import Model
from keras.layers import Input, Embedding, LSTM, Dense, TimeDistributed
from keras.optimizers import SGD, Adam, Adadelta
```

#### (2) 定义一些必要的工具函数，用来处理输入文本，生成训练数据。

在这里我们并不利用网络每一个时刻的输出，而只是用最后时刻的输出作为对一个句子下一个词的预测，所以训练数据是由（前 n 个词，第 n+1 个词）这样的二元组构成的，下一节会简要介绍循环神经网络的几种不同结构。

```python
# 建立词汇表，为每种字赋予唯一的索引
def build_vocab(text, vocab_lim):
    word_cnt = Counter(itertools.chain(*text))
    vocab_inv = [x[0] for x in word_cnt.most_common(vocab_lim)]
    vocab_inv = list(sorted(vocab_inv))
    vocab = {x: index for index, x in enumerate(vocab_inv)}
    return vocab, vocab_inv
```

```python
# 处理输入文本文件
def process_file(file_name, use_char_based_model):
    raw_text = []
    with open(file_name, "r") as f:
        for line in f:
            if (use_char_based_model):
                raw_text.extend([str(ch) for ch in line])
            else:
                raw_text.extend([word for word in jieba.cut(line)])
    return raw_text

# 格式化文本，建立词矩阵
def build_matrix(text, vocab, length, step):
    M = []
    for word in text:
        index = vocab.get(word)
        if index is None:
            M.append(len(vocab))
        else:
            M.append(index)
    num_sentences = len(M) // length
    M = M[: num_sentences * length]
    M = np.array(M)

    X = []
    Y = []
    for i in range(0, len(M) - length, step):
        X.append(M[i : i + length])
        Y.append(M[i + length])
    return np.array(X), np.array(Y)
```

- (3) 读取语料，用之前写好的工具函数得到训练数据。

```python
seq_length = 5
raw_text = process_file("../input/poetry.txt", True)
vocab, vocab_inv = build_vocab(raw_text, 4000)
X, Y = build_matrix(raw_text, vocab, seq_length, 1)
```

- (4) 搭建长短期记忆网络模型。

注意，Embedding 层就是之前所说的词向量嵌入层。在使用词向量的时候，可以先使用之前介绍的词到向量方法先训练得到词向量，再训练神经网络；也可以不作预先的训练，把词向量视作神经网络的参数，让神经网络在训练过程中同步调整词向量。这里我们为了简化采用后面一种方式。

```python
# 构建模型
inputs = Input(shape=(None, ))
embedding = Embedding(input_dim=len(vocab) + 1, output_dim=128,
    trainable=True)(inputs)
lstm1 = LSTM(units=128, return_sequences=False)(embedding)
outputs = Dense(units=len(vocab) + 1, activation='softmax')(lstm1)
model = Model(inputs=inputs, outputs=outputs)

# 编译模型
model.compile(optimizer=Adam(lr=0.001),
    loss='sparse_categorical_crossentropy')

# 输出模型报告
model.summary()
```

(5) 进行 batch 训练，并在训练完毕后保存模型。

```python
model.fit(X, Y, batch_size=512, epochs=60, verbose=1)
model.save('lstm-poetry.h5')
```

### 4. 结果

我们在五言诗数据集上训练，并且让模型在给出第一句诗后生成任意长度的诗歌。下面所示的是以“明月松间照”作为开头生成的诗。

![](img/9b291315612c9c478ad54afa29381fce_118_0.png)

![](img/9b291315612c9c478ad54afa29381fce_118_1.png)

## 第十一章 编码与解码——seq2seq

在本章中，我们将要学习循环神经网络的不同结构、seq2seq 的基本架构，并使用 Keras 搭建一个 seq2seq 模型。通过本章学习，要求掌握循环神经网络不同变体的不同应用，了解编码器—解码器架构的思想，并学会使用 seq2seq 模型进行文本生成。

在长短期记忆网络模型完成后，可以进行任意语料训练和文本生成了。接下来更进一步，解决给出上句诗，输出下句诗这样一个特殊的问题。在这一节中，我们将学习使用 seq2seq（序列对序列）模型。

### 一、引入：循环神经网络的不同结构

![](img/9b291315612c9c478ad54afa29381fce_119_0.png)

首先我们了解一下循环神经网络的几种结构，如上图所示。

- (1) 一对一：这种模型其实算不上是循环神经网络，而是典型的固定长度输入和输出的前馈神经网络。
- (2) 一对多：应用于给图片配字幕、机器看图说话等。即在开始处输入图片的信息，然后让机器自由输出句子。
- (3) 多对一：应用于情感分析、文本分类、文本生成等。即输入一个句子的信息，输出一次预判。之前的例子，使用的就是这种结构。
- (4) 变长多对多：应用于机器翻译、机器问答等。
- (5) 定长多对多：应用于文本生成、语言模型等。seq2seq 就是一种类似于变长多对多的结构。

### 二、编码器-解码器

![](img/9b291315612c9c478ad54afa29381fce_120_0.png)

最基础的 seq2seq 模型包含了 3 个主要的组成部分：编码器（encoder）、解码器（decoder）和连接它们的中间状态向量。编码器通过学习输入语句，将语句编码成一个固定大小的中间状态向量 S（这个中间状态向量中包含了整个输入语句的语义信息），然后将 S 传给解码器，解码器再对状态向量进行解码并产生输出。

举个例子，如果编码器和解码器都采用长短期记忆网络，那么 S 就是编码器最后一个时刻的隐状态和细胞状态，我们把它作为解码器第一个时刻神经元的初始隐状态和初始细胞状态，然后通过解码器对这个信号进行解码。

### 三、使用 Keras 搭建一个简单的 seq2seq 模型

Keras 官方有一个 seq2seq 的公开实现，以下代码基于 Keras 的公开实现作了一点改动。

- (1) 导入必要的库。

```python
from keras.models import Model
from keras.layers import Input, LSTM, Dense, Embedding
from keras.optimizers import Adam
import numpy as np
```

- (2) 定义一些参数——batch 大小，epoch 数，状态向量维数，词嵌入维数和语料路径。

```python
batch_size = 64
epochs = 100
latent_dim = 256
embedding_size = 128
file_name = '../input/poetry.txt'
```

- (3) 从语料构建编码器和解码器的训练数据。

```python
line_sp = line.strip().split(',')
if len(line_sp) < 2:
    continue
input_text, target_text = line_sp[0], line_sp[1]
target_text = '\t' + target_text[:-1] + '\n'
input_texts.append(input_text)
target_texts.append(target_text)
for ch in input_text:
    if ch not in input_vocab:
        input_vocab.add(ch)
for ch in target_text:
    if ch not in target_vocab:
        target_vocab.add(ch)
# 确定编码器、解码器网络长度
input_vocab = sorted(list(input_vocab))
encoder_vocab_size = len(input_vocab)
encoder_len = max([len(sentence) for sentence in input_texts])
target_vocab = sorted(list(target_vocab))
decoder_vocab_size = len(target_vocab)
decoder_len = max([len(sentence) for sentence in target_texts])
# 初始化输入和输出词汇反向词典
reverse_input_char_index = dict(
    (i, char) for char, i in input_vocab.items())
reverse_target_char_index = dict(
    (i, char) for char, i in target_vocab.items())

input_vocab = dict([(char, i) for i, char in enumerate(input_vocab)])
target_vocab = dict([(char, i) for i, char in enumerate(target_vocab)])
```

```python
# 利用词典向量化输入和目标
encoder_input_data = np.zeros((len(input_texts), encoder_len), dtype='int')
decoder_input_data = np.zeros((len(input_texts), decoder_len), dtype='int')
decoder_target_data = np.zeros((len(input_texts), decoder_len,
1), dtype='int')
for i, (input_text, target_text) in enumerate(zip(input_texts, target_texts)):
    for t, char in enumerate(input_text):
        encoder_input_data[i, t] = input_vocab[char]
    for t, char in enumerate(target_text):
        decoder_input_data[i, t] = target_vocab[char]
        if t > 0:
            decoder_target_data[i, t - 1, 0] = target_vocab[char]
```

#### (4) 构建 seq2seq 模型。

```python
# 编码器网络建构

# 编码器输入层
encoder_inputs = Input(shape=(None,))
# 编码器词嵌入层
encoder_embedding = Embedding(input_dim=encoder_vocab_size,
    output_dim=embedding_size, trainable=True)(encoder_inputs)
# 编码器长短期记忆网络层
encoder = LSTM(latent_dim, return_state=True)
# 编码器长短期记忆网络输出是一个三元组(encoder_outputs, state_h, state_c)
# encoder_outputs 是长短期记忆网络每个时刻的输出构成的序列
# state_h 和 state_c 是长短期记忆网络最后一个时刻的隐状态和细胞状态
encoder_outputs, state_h, state_c = encoder(encoder_embedding)
# 我们会把 state_h 和 state_c 作为解码器长短期记忆网络的初始状态，之前我们所说的状态向量的传递就
# 是这样实现的
encoder_states = [state_h, state_c]

# 解码器网络建构

# 解码器输入层
decoder_inputs = Input(shape=(None,))
# 解码器词嵌入层
decoder_embedding = Embedding(input_dim=decoder_vocab_size,
    output_dim=embedding_size, trainable=True)(decoder_inputs)
# 解码器长短期记忆网络层
decoder_lstm = LSTM(latent_dim, return_sequences=True, return_state=True)
# 解码器长短期记忆网络的输出也是三元组，但我们只关心三元组的第一维，同时我们在这里设置了解码器
# 长短期记忆网络的初始状态
```
