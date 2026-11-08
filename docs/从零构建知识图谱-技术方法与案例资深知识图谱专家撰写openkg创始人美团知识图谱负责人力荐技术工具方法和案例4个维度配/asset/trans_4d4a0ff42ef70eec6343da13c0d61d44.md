

### （1）jieba词性标注

`jieba`工具中集成了词性标注的功能。在`JiebaUtil`类中添加`jieba_postag`方法，需要引入头文件`jieba.posseg`，调用`jieba.posseg.cut()`或`jieba.posseg.lcut()`方法，前者返回的是一个生成器对象，后者返回的是`list`对象。例如输入问句“晴天是周杰伦的歌吗？”，文本以调用`lcut`为示例，调用`jieba.posseg.lcut()`方法返回的结果如下：

```
[pair('晴天', 'nz'), pair('是', 'v'), pair('周杰伦', 'nr'), pair('的', 'uj'), pair('歌', 'n'), pair('吗', 'y'), pair('？', 'x')]
```

该`list`对象中每个元素`w`为一个`pair`，其中第一个元素是词，第二个元素是词性。但是取结果时，不能按照`w[0]`和`w[1]`的方式，需要通过`w.word`取词，`w.flag`取词性，详细的代码如下。

```
import jieba.posseg  
def jieba_postag(self, sentence):  
    ''' 
    分词和词性标注 
    :param sentence: 输入句子 
    :return: 返回分词和词性标注的结果 
    '''  
    result = jieba.posseg.lcut(sentence)  
    result = [w.word + '/' + w.flag for w in result]  
    return result  
```

值得注意的是，`import jieba`和`import jieba.posseg`背后的模型版本不同，可能会出现分词结果不一致的情况。

### （2）pyltp词性标注

`LTPHander`类中包含词性标注的实现方法，需要从`pyltp`中引入`Postagger`类。与`jieba`不同之处在于，`pyltp`的词性标注是基于分词模型的结果再进行词性标注的，因此不会出现词性标注中分词结果与单独调用分词模型结果不一致的情况。详细的词性解释可参考官方网站[5]。具体实现方法如下：

```
import os  
from pyltp import Segmentor  
from pyltp import CustomizedSegmentor  
from pyltp import Postagger  
  
class LTPHander():  
    def __init__(self, ltp_path, seg_dict=''):  
        self.LTP_DATA_DIR = ltp_path  
        # 分词，默认加载  
        self._cws_model_path = os.path.join(self.LTP_DATA_DIR,'cws.model') # 分词模型路径，模型名称为cws.model  
        if seg_dict: # 引入用户字典  
            self._segmentor = CustomizedSegmentor()  
            self._segmentor.load_with_lexicon(self._cws_model_path, self._cws_model_path, seg_dict) # 加载模型  
        else:  
            self._segmentor = Segmentor()  
            self._segmentor.load(self._cws_model_path)  
        # 词性标注，默认加载  
        self._pos_model_path = os.path.join(self.LTP_DATA_DIR, 'pos.model') # 词性标注模型路径，模型名称为pos.model
        self._postagger = Postagger()  
        self._postagger.load(self._pos_model_path)  
  
    def segment(self, sent):  
        # 分词方法  
        words = self._segmentor.segment(sent)  
        return list(words)  
  
    def pos_tag(self, sent):  
        # 词性标注方法  
        words = self.segment(sent)  
        postags = self._postagger.postag(words)  
        return list(postags) 
```

与分词模型一样，`pyltp`中的词性标注方法同样支持采用默认分词模型和用户自定义分词模型及用户自定义词表。词性标注方法`pos_tag()`会首先调用分词方法`segment()`，获得句子的分词结果，然后将其作为词性标注器对象`self._postagger.postag`的输入，得到词性标注的结果。

### 3. NER

`NER`（命名实体识别，Name Entity Recognition）是问答系统中的核心任务之一，可以帮助识别问句中的候选实体词。下面介绍使用`pyltp`工具进行`NER`的方法。

`pyltp`的命名实体识别是基于分词和词性标注的结果进行的，首先引入`NamedEntityRecognizer`对象：

```
from pyltp import NamedEntityRecognizer  
```

然后在`__init__()`函数中加载`NER`模型并创建命名实体对象`self._ner`。

```
# 命名实体识别模型路径，模型名称为`ner.model`  
self._ner_model_path = os.path.join(self.LTP_DATA_DIR, 'ner.model') 
# 初始化实例  
self._ner = NamedEntityRecognizer() 
```

`pyltp`共支持人名（`Nh`）、地名（`Ns`）、机构名（`Ni`）三类实体的识别。返回结果采用`BIESO`（Begin-Inside-End-Single-Outside）标签体系，其中`B`表示实体开始词，`I`表示实体中间词，`E`表示实体结束词，`S`表示单个词成实体，`O`表示不是实体。本文对`pyltp`的`NER`返回结果进行了封装，将实体按照（开始位置，结束位置，实体字符串，实体类型）四元组的结构返回。

```
def ner(self, sent):  
    '''
    NER方法，共可以识别三种类型的命名实体： 
    - Nh    人名 
    - Ni    机构名 
    - Ns    地名 
    :param sent: 中国工商银行总部在哪里 
    :return: [(0, 6, '中国工商银行', 'Ni')] 
             [(开始index, 结束index, NE子串1, NE类型), (开始index, 结束index, NE子串2, NE类型), ……] 
    '''  
    words = self.segment(sent)  # 分词结果  
    postags = self.pos_tag(sent)  # 词性标注结果  
    netags = self._ner.recognize(words, postags)  
    res = []  
    cur_index = 0  
    ne_start = 0  # 实体开始的词的索引
    start = 0  # 实体开始的下标位置  
    for i, lab in enumerate(netags):  
        # 当NER标签为O和I时，跳到下一个词，更新cur_index  
        if netags[i] == 'O' or netags[i].startswith('I'):  
            cur_index += len(words[i])  
            continue  
        # 当标签为S时，表示单个词成实体  
        if netags[i].startswith('S'):  
            start = cur_index  
            end = cur_index + len(words[i])  
            ne_type = netags[i].split('-')[1]  
            ne_words = words[i]  
            res.append((start, end, ne_words, ne_type))  
        # 当标签为B时，表示该词是实体开头的词  
        elif netags[i].startswith('B'):  
            ne_start = i  
            start = cur_index  
        # 当标签为E时，表示该词是实体结束的词  
        elif netags[i].startswith('E'):  
            ne_end = i  
            end = cur_index + len(words[i])  
            ne_type = netags[i].split('-')[1]  
            ne_words = ''.join(words[ne_start:ne_end + 1])  
            res.append((start, end, ne_words, ne_type))  
        cur_index += len(words[i])  
    return res  
```

例如，输入问句“中国工商银行在北京的总部地址？”，会返回如下结果：

```
分词结果：['中国', '工商', '银行', '在', '北京', '的', '总部', '地址']
词性标注结果：['ns', 'n', 'n', 'p', 'ns', 'u', 'n', 'n']
NER结果：[(0, 6, '中国工商银行', 'Ni'), (7, 9, '北京', 'Ns')]
```

其中“中国工商银行”是一个机构名称，属于`Ni`实体类型，包含三个词语“中国”“工商”“银行”，在句中的位置是`[0:6)`（左边为闭区间，右边为开区间），“北京”为句中的第二个实体，属于地名`Ns`实体类型，在句中的位置是`[7:9]`。

### 4. 依存句法分析

依存句法分析是获得句中词和词之间依存关系的任务，其中关系可以是主谓关系（`SBV`）、动宾关系（`VOB`）等。`pyltp`内包含依存句法分析模块，详细的文档[6]可参考官网，依存标签如表7-1所示，更多内容可参考相关网页[7]。依存分析有多套标注，经典的还有`Stanford`依存分析参考的宾州树库的依存标注。依存标签与`pyltp`的有所不同，本文不再展开详细介绍，感兴趣的读者可以参考`Stanford NLP`依存分析任务的官方网站[8]自行研究。

表7-1　`pyltp`依存标签体系

![000](images/00185.jpeg)

下面介绍`pyltp`的句法分析的实现。引入`Parser`类：

```
from pyltp import Parser  
```

在`__init__()`函数中加载`parser`模型并创建命名实体对象`self._parser`。

```
# 依存句法分析模型路径，模型名称为parser.model  
self._par_model_path = os.path.join(self.LTP_DATA_DIR, 'parser.model')   
# 初始化实例  
self._parser = Parser() 
```

对于依存句法分析方法`parser()`，需要将分词和词性标注的结果作为输入，依存句法分析器`self._parser`的输入的具体实现如下：

```
def parser(self, sent):  
    '''
    Root结点索引为0，第一个词开始索引为1、2、3…… 
    arc.relation 表示依存弧的关系
    arc.head 表示依存弧的父结点词的索引
    :param sent: 输入句子 
    :return: [(父结点逻辑索引，当前结点的父结点词，当前结点逻辑索引，当前结点词，依存关系), ……] 
    '''  
    words = self.segment(sent)  
    postags = self.pos_tag(sent)  
    arcs = self._parser.parse(words, postags)  # 句法分析  
    # 打印结果  
    # print "\t".join("%d:%s" % (arc.head, arc.relation) for arc in arcs)  
    parser_words = ['ROOT'] + words  
    res = [(arc.head, parser_words[arc.head], idx+1, parser_words[idx+1], arc.relation) for idx, arc in enumerate(arcs)]  
  
    return res  
```

`parser`函数将依存分析结果封装成一个`list`返回，`list`中每个元素是一个五元组——（当前结点的父结点逻辑索引，当前结点的父结点词，当前结点逻辑索引，当前结点词，依存关系），核心调用的是依存句法分析器`self._parser`的`parser`方法。

## 7.2.3　意图理解

意图理解是整个问答对话系统中重要的任务之一，具体是指对用户输入的句子进行语义理解，进而识别出用户的真实意图，再进入对应的处理模块分别进行处理。本文将系统核心意图分为打招呼意图（`hello`）、问答意图（`qa`）和闲聊意图（`chat`）三种，下面将介绍意图识别的三种实现方法。

### 1. 相似度检索

对于一些已知意图的问句，最基本的意图识别方法是全串匹配，即可得到精准的意图标签，如`full_match()`函数所示方法。然而该方法的召回率必然很低，所以我们可以做一些改进，主要从增加检索库数据和优化相似度检索方式两个角度出发。

```
def full_match(sent):  
    # 全匹配意图识别方法  
    intent = ''  
    if sent == '你好':  
        intent = 'hello'  
    if sent == '姚明的身高是多少？':  
        intent = 'qa'  
      ......
    return intent
```

基于有限的问句数据，我们可以通过数据泛化的方式增加同一个问题的多样化表述的语料，提升对相似问句的匹配召回率。常用的数据泛化方法有以下几种。

#### （1）机器翻译方法

机器翻译方式的基本思想是通过多家翻译接口，将初始问句翻译到多种目标端语言再翻回中文，由于经历了回翻的过程，可以得到语义相似但表述不同的新的问句，但是该方法产生的新的问句可能会有一些不通顺的情况，此时可以结合语言模型对多个翻译结果进行排序选优。

#### （2）同义词替换

通过同义词库、`WordNet`、`HowNet`、同义词林、大辞林、词向量获取相似词语等方法可以替换掉句中的一些部分，泛化出新的意图数据。为了提升替换的准确性，也可以结合基础`NLU`模块中获得的词性、依存关系等特征来为句中词语赋予不同的替换权重，还可以结合`TF-IDF`、`TextRank`等算法算出句中词语的权重，有选择地替换重要或者不重要的词汇。

#### （3）加减词

加减句中一些不重要的词，例如标点、助词、语气词、副词等。除此以外，还可以随机添加一些用户在问答时可能会说的插入语信息，例如“我想知道……”“请问一下……” “能不能告诉我……”等，这些可以基于用户问答的历史数据进行统计和搜集。

#### （4）同义句式改写

在问答中，有很多类型的问题可以有多种常用表述，例如`isA`问题就可以用以下几种句式来询问：

```
"xx是一种yy吗"
"xx是yy中的一种吗"
"xx属于yy吗"
```

那么对于任何一个查询三元组问题，则都可以通过问题的句式进行数据泛化。相似的句式还可以结合依存句法分析，提取出句式模板进行构建。

#### （5）生成式改写

当相似语义数据积累到一定量时，即可训练生成式改写模型，将任意两个语义相似的数据分别作为源和目标端，训练文本生成模型，对原语料做语义泛化，增加语料的多样性。

除了增加检索数据的多样性，提升相似度检索能力也是一种重要方法。可以将意图数据存入一些数据检索引擎，例如`Elasticsearch`就是一款可以高效实现相似文本检索的数据库，其内置多种文本相似度匹配算法，包括`TF-IDF`相似度模型、`Okapi BM25`相似度模型、`DFR`相似度模型和`IB`相似度模型等。根据问答对话系统问句的特点，`BM25`算法通常更适合短文本相似度的匹配。

### 2. 模板匹配

基于全句匹配方法，还可以将常见句式、关键词等抽象成模板，通过模板匹配的方法进行意图识别。下列代码展示了基于关键词的模板匹配方法。

