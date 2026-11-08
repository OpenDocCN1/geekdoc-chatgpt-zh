

# 实体提取与评估

## `tidy_crf_format` 函数实现

```
def tidy_crf_format(filename, col):  
    ''' 
    提取crf_test命令预测出的文件中匹配到的实体并返回 
    :param filename: crf_test预测文本 
    :param col: 序列标注的列号，0为文本，1为标准答案，2为预测结果
    :return: 返回匹配到的实体 
    '''  
    matched = []  
    one = [0, -1, '', '']  # 行号、开始位置、实体字符串list、实体标签  
    line_num = 0  # 记录行号  
    index = 0  # 记录当前字符在句子中的下标  
    with open(filename, encoding='utf8') as infile:  
        for line in infile:  
            line = line.rstrip()  # 仅去掉右端的空格和换行  
            if line:  
                combo = line.split('\t')  
                ch = combo[0]  
                tag = combo[col]  
                if tag.startswith('B'):  # 表示一个实体的开始  
                    one[0] = line_num  # 实体所属行号  
                    one[1] = index  # 开始位置  
                    one[2] += ch  # 当前字符添加到实体字符串  
                    one[3] = tag.split('-')[1]  # 添加实体标签  
                elif tag.startswith('I'):  # 实体中间，实体字符串添加当前字符  
                    one[2] += ch  
                elif tag.startswith('E'):  # 实体结束  
                    one[2] += ch  
                    matched.append(one)  
                    one = [0, -1, [], '']  # 刷新one  
                elif tag.startswith('S'):  # 单字符表示一个实体，既是实体开始又是实体结束
                    one[0] = line_num  
                    one[1] = index  # 开始位置  
                    one[2] += ch  
                    one[3] = tag.split('-')[1]  
                    matched.append(one)  
                    one = [0, -1, '', '']  
                index += 1  
            else:  
                index = 0  
                line_num += 1  
  
    return matched  
```

## 函数核心思路

`tidy_crf_format`函数核心思路：记录每一行数据所匹配到的实体内容、实体在句中的位置以及实体的标签，将其表示成一个四元组的列表形式。以“我要听周杰伦的歌。”为例，假设这句话是测试用例中的第一条，那么行号就是0（物理下标），匹配到了一个歌手实体“周杰伦”，“周杰伦”在句中的开始下标为3（同样是物理下标），实体标签为“AR”，那么这样一个实体提取出的四元组结果`one`组成的列表就是`[0, 3, '周杰伦', 'AR']`。

## 标注示例

```
我    O       O
要    O       O
听    O       O
周    B-AR    B-AR
杰    I-AR    I-AR
伦    E-AR    E-AR
的    O       O
歌    O       O
。    O       O
```

其中输入是`test_out_file`模型对测试集的预测结果，待解析的列号；输出是解析列匹配到的实体信息，格式为`list`，存放多个四元组`one`。

由于预测数据是存在文本中的，并且一个字符的标签结果为一行，每个测试句子之间用换行符分隔，因此按行读取处理。首先去掉末尾的换行符，查看该行文本`line`是否非空，若非空，则包含字符，若为空，则表示句子和句子间的分隔符，行号计数加1。当非空时，可以通过分隔符`\t`将该行数据拆分为：字符、标准答案标签、模型预测标签。根据传入参数`col`处理对应列的标签（`col=1`时表示处理标准答案标签列，`col=2`时表示处理预测标签列），标签的具体含义如下：

* 以‘B’开头说明是一个实体的开始，记录实体所在的句子行号`line_num`，实体开始位置`index`，实体字符，实体标签；
* 以‘I’开头，表示处于一个实体中间，则只要添加当前字符到实体字符串中即可；
* 以‘E’开头，表示处于实体结束为止，添加字符，再将记录的信息添加到`matched`列表，并初始化`one`；
* 以‘S’开头，说明该字符单字成实体，该字符既是实体的开始，也是实体的结束，需要同时记录`one`的每一项信息后，添加到匹配列表，并重新初始化`one`。

判断完当前字符以后，字符的下标加1。

下面分别调用转换标注答案标签列和预测标签列的信息。

```
y_true = tidy_crf_format('test_out_file', 1)  
y_pred = tidy_crf_format('test_out_file', 2) 
```

分别对标注答案标签列和预测标签列做处理，得到匹配的信息`y_true`和`y_pred`列表。对比匹配到的信息即可计算出P、R、F1值，具体计算方法参考`compute_p_r_f1`函数。

## 评估函数 `compute_p_r_f1` 与 `evaluate_entity`

```
def compute_p_r_f1(y_true, y_pred):  
    y_true = [str(e) for e in y_true]  
    y_pred = [str(e) for e in y_pred]  
    p = len(set(y_true).intersection(y_pred)) / len(y_pred)  
    r = len(set(y_true).intersection(y_pred)) / len(y_true)  
    f1 = 2 * p * r / (p + r)  
    return p, r, f1  
  
def evaluate_entity(y_true, y_pred):  
    so_true = list(filter(lambda x: x[3] == 'SO', y_true))  
    ar_true = list(filter(lambda x: x[3] == 'AR', y_true))  
    ge_true = list(filter(lambda x: x[3] == 'GE', y_true))  
  
    so_pred = list(filter(lambda x: x[3] == 'SO', y_pred))  
    ar_pred = list(filter(lambda x: x[3] == 'AR', y_pred))  
    ge_pred = list(filter(lambda x: x[3] == 'GE', y_pred))  
  
    so_p, so_r, so_f1 = compute_p_r_f1(so_true, so_pred)  
    ar_p, ar_r, ar_f1 = compute_p_r_f1(ar_true, ar_pred)  
    ge_p, ge_r, ge_f1 = compute_p_r_f1(ge_true, ge_pred)  
  
    print('歌曲：', so_p, so_r, so_f1)  
    print('歌手：', ar_p, ar_r, ar_f1)  
    print('曲风：', ge_p, ge_r, ge_f1) 
```

调用`evaluate_entity()`函数评估模型效果。

```
evaluate_entity(y_true, y_pred)  
```

`evaluate_entity`函数解读如下。按照三类实体，分别计算出对应的P、R、F1值，调用功能函数`compute_p_r_f1`，具体计算公式如下：

![000](images/00133.jpeg)

其中，`E`为某一特定实体类型，可以是`SO`、`AR`、`GE`之一。一条实体匹配信息包含句子行数、在句中的位置、实体字符串和实体标签，通过`filter`函数可以根据实体类型将数据划分开，分别将每个类型实体的正确结果和预测结果输入`compute_p_r_f1`函数中，利用集合运算中的交集运算，根据公式分别获得P和R值，再由P和R计算得出F1值。

## 模型预测

模型预测用于对未标注的数据实现自动标注。为了方便在Python程序中调用，本文提供了Python版预测代码。

### `MusicNER` 类实现

```
import CRFPP  
  
class MusicNER():  
    '''
    CRF做音乐歌曲名、歌手名、曲风的识别 
    '''  
    def __init__(self):  
        self.crf_model = 'model_music_ner'  # 模型路径
        self.tagger = CRFPP.Tagger("-m " + self.crf_model)  # 创建一个标注器
  
    def ner_predict(self, sent):  
        ''' 
        程序入口，预测一个句子的音乐实体
        :param sent: 输入一个句子 
        :return: 返回句子中匹配到的实体列表 
        '''  
        sent = sent.replace(' ', '')  # 去空字符  
        self.tagger.clear()  # 清空标注器 
        a = [self.tagger.add((w)) for w in sent]  # 将句子按字符加入标注器 
        self.tagger.parse()  # 调用标注模型预测
        size = self.tagger.size()  
        xsize = self.tagger.xsize()  
        entity_list = []  
        entity = ''  
        start = 0  
        label = ''  
     # 记录标注结果
        for i in range(0, size):  
            for j in range(0, xsize):  
                char = self.tagger.x(i, j)  
                tag = self.tagger.y2(i)  
                if tag.startswith('B'):  
                    entity += char  
                    start = i  
                    label = tag.split('-')[1]  
                elif tag.startswith('I'):  
                    entity += char  
                elif tag.startswith('E'):  
                    entity += char  
                    entity_list.append((entity, start, label))  
                    entity = ''  
                elif tag.startswith('S'): 
                    label = tag.split('-')[1]  
                    start = i 
                    entity_list.append((char, start, label))  
                    entity = ''  
  
        return entity_list 
```

调用模型进行预测：

输入句子：我想听邓紫棋的喜欢你
预测结果：[('邓紫棋', 3, 'AR'), ('喜欢你', 7, 'SO')]

构建一个`MusicNER`类，将模型加载函数写入`__init__`函数，创建一个预测函数`ner_predict`。之所以特地将模型加载写入初始化函数，是希望实现加载一次模型就可以持续进行句子的预测，而不用每一次预测时都重新加载模型。其余逻辑与上文`evaluate_entity`函数类似，分别对B、I、E、S集中标签做对应数据的记录，最后将结果存入`entity_list`，结果以实体三元组列表的格式返回，三元组的第一位是实体内容，第二位是实体在句中的开始位置，第三位是实体的类别标签。

# 聊天话题体系构建

上一小节详细介绍了音乐推荐应用下知识抽取的方法，本小节将讨论用户画像图谱构建中另一个重要的应用，即用户聊天话题的喜好抽取。人们日常聊天是一个开放域的对话任务，内容广泛且没有边界，很难准确把握用户的聊天喜好。例如用户A是一个“篮球迷”，而用户B是一个“二次元控”，如何从用户的聊天内容中提取出这样的信息呢？这就需要我们能对用户聊天话题进行抽象和总结，将相关话题进行关联，对相似话题进行归类，从而形成一套聊天话题的体系，对用户输入的句子进行话题的识别，并对用户触发的话题进行统计分析进而得到用户的聊天喜好。聊天话题体系的构建，一方面可以辅助用户聊天喜好的抽取，另一方面可以为话题的跳转、相关话题的联想提供方向。聊天话题之间本身会存在一定的上下位关系，例如休闲娱乐包含旅游、摄影、K歌、游戏、看书等多个不同的下位话题，当用户谈论旅游话题时，可以知道用户在谈论休闲娱乐领域的内容，此时可以联想到其他相关的话题进行答句生成，如图5-15所示。

![000](images/00134.jpeg)

图5-15　聊天话题联想示例

下面介绍根据真实数据构建聊天话题体系的方法思路。首先，需要从用户日志中抽取出一些潜在话题候选词，其次对话题词进行聚类，获得基本的类别特征，然后借助外部知识库等信息获得话题词的领域概念信息，最后构建一个话题的体系。这个话题体系本质上就是一个话题领域图谱，图谱中每一个实体便是一个话题，话题和话题之间存在上下位关系，同级话题之间保持着一定的相关度，并且每一个话题都可以看作一个概念，通过外接知识库，可以做话题的扩展、联想和跳转。每一次当用户输入一句聊天内容时，先对其做话题识别，然后可以生成围绕该话题的回复，也可以联想跳转到其他相关话题做回复。具体步骤分析如下。

* 数据筛选：搜集大量用户日志，去掉无关信息，如唤醒词、功能指令等。
* 关键词筛选。
  * 对语料先做分词、词性标注，可以采用多个分词工具和词性标注工具，以获得多个答案，从多角度进行筛选。
  * 为句中每个词计算TF-IDF权重、训练LDA主题模型，获取每个主题下包含的关键词列表。
  * 结合词性、TF-IDF权重，以及是否在主题模型关键词列表等信息对候选词进行打分，从话题抽取角度来说，通常一个句子中动词、名词相对重要，形容词、副词次之，助词、代词、介词、数词等词往往不太重要，可以过滤掉。
* 话题领域映射。
  * 借助通用百科知识图谱，可将关键词映射到图谱中以寻找对应的标签、属性和所属概念，可以抽象出很多类别的话题信息，并进行一定的领域划分。
* 剪枝：
  * 根据话题的频率、所属标签和领域的频率信息，去掉太过低频的信息。

通过以上步骤，即可获得很多话题关键词以及它们的标签和概念信息，通过统计分布可以大致了解用户喜欢和机器人聊些什么内容，并以此为依据构建话题体系。构建完成后，还需要人工加以整理和干预，即基于用户的关注话题范畴，将其划分层级，并补充整理一些同属领域的其他话题内容，以完善整个话题体系。图5-16展示了一个相对完整的话题内容的体系结构，可供读者参考。

![000](images/00135.jpeg)

图5-16　聊天话题体系图

* * *

[1] http://corenlp.run/。
[2] https://github.com/HIT-SCIR/ltp。
[3] https://biendata.com/competition/CCKS2018_2/。

## 参考文献

[1] Kim Y. Convolutional Neural Networks for Sentence Classification[J]. arXiv preprint arXiv: 1408.5882, 2014.

