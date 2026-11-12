# 7.2.1 数据集的准备

数据的收集和清洗是首先要做的任务，为了将数据以正确的形式表示出来，需要预先针对数据做一些处理。首先针对下载完成的数据文件提取其中的单词数据，然后对这些数据进行出现次数的统计，构建出一个词汇表，表中包含了最常见的一些单词，这些单词对应着指定 ID 的数值，最后，使用构建出的词汇表来对提取的页面内容进行编码。如果将下载的数据资料中的所有单词都放入词汇表中，就会造成词汇表过大，从而影响到最终的训练速度，所以仅仅将最常见的一些单词放入到词汇表中。而那些不常使用的单词内容，使用 UNK 进行标记，并使得它们对应一个 id 数值。由于为各个单词都进行了编码，因此可以动态地形成训练样本。众所周知，Skip-Gram 模型会依据当前单词来对上下文单词进行预测，在对所有单词进行遍历时，首先将当前单词作为基准数据，将这个单词周围的词作为目标来创建训练样本。

另外，在对下载的数据资料内容进行读取的过程中，很难一次性将所有的词汇都放入到内容中，因此往往会采用逐行读取的方式来对文件内容进行提取，然后将结果写入到磁盘存储空间内。在此过程中，每一个步骤之间都会设置检查点并加以保存，以便在程序代码出现错误或崩溃时，能够以最快捷方便的方式重新开始工作。代码如下。

```
vocabulary_size = 50000
def build_dataset(words, n_words):
    count = [['UNK', -1]]
    count.extend(collections.Counter(words).most_common(n_words - 1))
    dictionary = dict()
    for word, _ in count:
        dictionary[word] = len(dictionary)
    data = list()
    unk_count = 0
    for word in words:
        if word in dictionary:
            index = dictionary[word]
        else:
            index = 0
            unk_count += 1
        data.append(index)
    count[0][1] = unk_count
    reversed_dictionary = dict(zip(dictionary.values(), dictionary.keys()))
    return data, count, dictionary, reversed_dictionary
data, count, dictionary, reverse_dictionary=build_dataset(vocabulary, vocabulary_size)
del vocabulary
print('Most common words (+UNK)', count[:5])
print('Sample data', data[:10], [reverse_dictionary[i] for i in data[:10]])
data_index = 0
```

源程序代码内容中，vocabulary_size = 50000 表示所构建的词汇表的大小为 50 000，即将最常出现的 50 000 个单词放入到词汇表中，在此过程中，一个名词的单数形式和复数形式表示为两个不同的单词，动词的一般时态形式和过去时态形式以及进行时态形式等也都分别代表了不同的单词，最终，词汇表从一个单词的字符串列表形式变成了一个单词 id 的数值列表形式；del vocabulary 表示程