

\[57\] Bordes A, Chopra S, Weston J. 基于子图嵌入的问答\[C\]. arXiv预印本 arXiv:1406.3676, 2014.

\[58\] Yang M C, Duan N, Zhou M, et al. 知识型问答的联合关系嵌入\[C\]. EMNLP. 2014, 14: 645-650.

\[59\] Dong L, Wei F, Zhou M, et al. 基于多列卷积神经网络的Freebase问答\[C\]. 计算语言学协会年会暨国际自然语言处理联合会议. 2015:260-269.

\[60\] Hao Y, Zhang Y, Liu K, et al. 融合全局知识的跨注意力知识库问答端到端模型\[C\]. 计算语言学协会年会. 2017:221-231.

\[61\] Bordes A, Usunier N, Chopra S, et al. 基于记忆网络的大规模简单问答\[C\]. 计算机科学, 2015.

# 第7章
## 基于知识图谱的问答系统

在第6章中我们已经详细阐述了如何实现知识可视化，如何实现实体链接，也介绍了基于图谱的两大应用——知识问答和图谱联想，本章将从实战视角详细讲解如何从零构建一个基于知识图谱的问答系统（KGQA）。其中7.1节将介绍问答系统的整体框架，7.2节、7.3节和7.4节将分别详细介绍问答系统的三个核心部分：自然语言理解（NLU）、对话管理（DM）和自然语言生成（NLG），最后7.5节中给出了问答系统服务化的方法。

## 7.1　简介

本章将通过实际代码，结合前几章搭建好的通用及领域知识图谱，展示如何实际构建可用的问答系统。

整体的项目构建流程如图7-1所示。

![000](images/00183.jpeg)

图7-1　项目流程图

问答对话系统整体流程分为三个部分。

首先是自然语言理解，主要包含两个功能：第一是对用户输入的问句进行语义理解，核心是意图识别，以指导后续对话管理模块选择服务执行；第二是对各个服务均可能用到的NLU前序结果进行统一调用、封装，供后续模块使用，减少重复调用。

其次是核心对话管理，包括四个功能。第一是基础功能，根据NLU的结果，选择不同的服务模块进行调用，其中可能存在多个候选服务，形成候选服务列表，例如打招呼意图较为明确，只需要一个服务候选，而问答（QA）粗意图则需要四个服务候选。调用方式采用多线程并行方式，以减少多模块调用时间开销，执行结束后同步返回结果，统一封装，并对多个模块的执行结果进行排序。第二是QA功能，由三部分构成，分别是基于KG的QA模块（4个子模块，分别处理通用QA、领域QA、用户QA和bot人设QA，读者可参考6.3节的内容）、基于检索（IR）的QA模块、基于机器阅读理解（MR）的QA模块。QA意图的识别结果可以有两种：一种为粗意图，需子模块自行判断当前问句是否可回答，另一种为细意图，意图为特定子模块。第三是闲聊能力，由联想（读者可参考6.4节的内容）和兜底两部分构成。第四是多轮对话能力，基于槽位继承的方式，对多轮QA提供支持，设计和维护多轮状态机，保存每轮的`dst`结果，设定多轮时间窗口。

最后一部分是自然语言生成，即生成优化后的答句。

本章将按照标准的对话系统，从自然语言理解、对话管理、自然语言生成以及服务化四个部分进行介绍。

首先前往GitHub上获取代码，项目地址是https://github.com/zhangkai-ai/build-kg-from-scratch。读者可以对照项目文件阅读下文，或者自己从零搭建一个Python项目。项目主函数位于`ch7/main_handler.py`。

```
import json
import time
from loguru import logger
from nlu.nlu_handler import nlu_main_handler
from dm.dm_handler import dm_main_handler
from nlg.nlg_handler import nlg_main_handler
from dm.dst_manager import add_dst
```

首先从各模块引入相关处理函数。

```
def sent_handler(sent, user_id):
    """
    对话输入语句的主处理函数
    :param sent: 对话输入语句
    :param user_id: 用户的id
    :return: 对话回复语句
    """
    # 1. NLU模块，获取对话意图及通用自然语言理解
    nlu_dict = nlu_main_handler(sent)
    '''
    nlu_dict_example = {
        "input": "xxx",
        "base_nlu": {
            "jieba_cut": ["a", "b"]
        },
        "intent": "qa",
        "slot": {}
    }
    '''
    logger.info("NLU RST: {}".format(str(nlu_dict)))
    
    # 2. DM模块，对话管理，选择候选服务并调用，然后对结果排序
    dst = dm_main_handler(user_id, nlu_dict)
    logger.info("DM RST: {}".format(str(dst)))
    
    # 3. NLG模块
    dst = nlg_main_handler(dst, nlu_dict)
    logger.info("NLG RST: {}".format(str(dst)))
    
    # 4. 保存本轮结果到dst中
    dst["time"] = time.time()
    add_dst(user_id, dst)
    
    return dst
```

在主处理函数`sent_handler`中，第一步是通过`nlu_main_handler()`获取用户对话意图以及自然语言处理结果，以字典形式返回，同时记录日志。然后，通过对话管理模块`dm_main_handler()`，选择候选服务并调用，通过自然语言生成模块`nlg_main_handler()`，将回复改写后返回给用户。最后将本轮结果通过`add_dst()`加入多轮记录中。

## 7.2　自然语言理解

### 7.2.1　概述

自然语言处理（Natural Language Processing，NLP）是指理解人类语言并将其转化为机器可理解的结构化信息的过程，主要包括自然语言理解（Natural Language Understanding，NLU）和自然语言生成（Natural Language Generation，NLG）两大任务，除此之外还包括语言理解前的文本处理，以及语言理解后如何对接后续具体任务。NLU是NLP的核心，通常包含问句理解、意图识别、情感分析、词法（Lexical）、句法（Syntax）和语义（Semantic）等不同层次的分析工作，如图7-2所示。

![000](images/00184.jpeg)

图7-2　NLP的组成

在问答系统这一具体应用中，NLU的主要任务是识别用户真实问答意图、问答信息词抽取以及一些基础NLP特征的提取，从而为后续模块提供服务。本节将从基础NLU、意图识别、实体识别与链接和文本相似度与向量化四个方面来介绍。

### 7.2.2　基础NLU

在问答系统应用中，基础自然语言理解阶段主要包括分词、词性标注、命名实体识别、依存句法分析等任务，目标是提取出基础语义特征，供后续模块使用。

1\. 分词

分词几乎是每一个中文NLP任务的必经阶段，将句子拆分成词粒度的语义单元，供后续的NLP任务使用。主流的分词工具有`jieba`分词、哈工大的`pyltp`、`Hanlp`、清华的`THULAC`分词、中科院的`ICTCLAS`分词等。

本文提供3种分词工具的实现方法和调用方法供读者参考。

（1）jieba分词

```
import jieba  
class JiebaUtil:  
    def __init__(self, user_dict=''):  
        # 当用户自定义词表时，Jieba会在系统词表基础上用user_dict进行更新
        if user_dict:  
            # 词表格式：词条、频次  
            # 通常频次可设置为3，但当Jieba依旧无法分开时，需要调大频次值
            jieba.load_userdict(user_dict)  
        print(list(self.jieba_cut("告白气球")))  
  
    def jieba_cut(self, sentence, type=1):  
        seg_list = []  
        if type == 1:  
            # 默认为精确模式  
            seg_list = jieba.cut(sentence, cut_all=False)  
        elif type == 2:  
            # 全模式  
            seg_list = jieba.cut(sentence, cut_all=True)  
        elif type == 3:  
            # 搜索引擎模式  
            seg_list = jieba.cut_for_search(sentence)  
        else:  
            print("jieba input type error: parameter type out of range") 
        return seg_list  
  
jieba_util = JiebaUtil(user_dict='jieba_user_dict.txt')  
```

本文展示了`jieba`的三种分词模式——精准模式、全模式、搜索引擎模式，并且展示了如何自定义用户词表进行分词。在很多时候，句中的一些实体信息在分词过程中会被切分开，例如电影名称“消失的爱人”、歌曲名称“告白气球”等。在特定类型的问答任务中，可能因为分词导致实体无法被完整提取，此时需要自定义用户词表提升任务的准确性。在Jieba中，用户自定义词表`jieba_user_dict.txt`文件的格式如下：

```
消失的爱人 3
简单爱 3
告白气球 3
```

表中有两列信息，用户自定义词条和词频，通过空格连接，频率一般默认设置为3。对于一些较长的词条，通常设置较低频率，在分词时就可以分开；如果希望将原本在一起的词语分开，例如将“小姐姐”切分成更细粒度的“小”和“姐姐”，此时需要分别将“小”和“姐姐”两个词各自单独的频率升高并且降低“小姐姐”这个词条的频率，这样才能实现分词。在实际使用时，还是要根据具体情况设置词频。

（2）哈工大pyltp分词

读者可以使用Python调用接口`pyltp`进行分词，但在此之前，需要先下载`pyltp`的模型[1]，详细文档可参考官网[2]。我们将`pyltp`的所有接口分装成了一个`LTPHander`类，位于`ch7/nlu/seg/ltp_util.py`中，其中包含默认分词方法和用户自定义词表分词方法。用户自定义词表`pyltp_user_dict.txt`格式如下，与`jieba`自定义词表格式不同，`pyltp`仅需要一列词条即可，不需要添加词频信息。

```
消失的爱人
简单爱
告白气球
```

用`pyltp`实现分词的代码如下：

```
import os  
from pyltp import Segmentor  
from pyltp import CustomizedSegmentor  
  
class LTPHander():  
    def __init__(self, ltp_path, seg_dict=''):  
        self.LTP_DATA_DIR = ltp_path  
        # 分词，默认加载  
        self._cws_model_path = os.path.join(self.LTP_DATA_DIR,'cws.model')
        # 分词模型路径，模型名称`cws.model`  
        if seg_dict: # 引入用户字典  
            self._segmentor = CustomizedSegmentor()  
            self._segmentor.load_with_lexicon(self._cws_model_path, self._cws_model_path, seg_dict) # 加载模型  
        else:  
            self._segmentor = Segmentor()  
            self._segmentor.load(self._cws_model_path)  
  
    def segment(self, sent):  
        words = self._segmentor.segment(sent)  
        return list(words)  
```

下面分别展示采用默认分词器和用户自定义词表分词器创建对象的方法。

```
# 创建一个默认分词对象
ltp_seg = LTPHander(ltp_path)  
# 创建一个带用户自定义词表的分词对象
ltp_seg_user = LTPHander(ltp_path, 'pyltp_user_dict.txt')  
sent = '告白气球是周杰伦的歌吗？'  
print(ltp_seg.segment(sent))  
print(ltp_seg_user.segment(sent)) 
```

默认分词器调用的是`pyltp`中的`Segmentor`类，采用用户自定义词表分词器时调用的是`CustomizedSegmentor`。该类需要传入3个参数，第一个为默认分词模型，第二个为用户自己训练的自定义分词模型，当没有自定义模型时，可以设置为默认分词模型，第三个参数为用户自定义词表。

当我们分别创建一个默认分词对象和一个采用用户自定义词表的分词对象时，对于同一个输入——“告白气球是周杰伦的歌吗？”，分词结果对比如下：

```
默认分词器：['告白', '气球', '是', '周杰伦', '的', '歌', '吗', '？']
加载自定义词表：['告白气球', '是', '周杰伦', '的', '歌', '吗', '？']
```

（3）HanLP分词

`HanLP`[3]是由何晗自主开发的一套NLP工具包，包括分词、词性标注、命名实体识别、依存句法分析等多个NLP功能模块。`HanLP`的作者一直在更新工具，工具支持传统方法以及一些相对前沿的实现方法。其分词模块中支持多种分词模式，例如CRF分词模式、感知机分词模式、N最短路径分词模式、维特比分词模式、双数组trie树等。`HanLP`同样具有Python调用接口的包`pyhanlp`。本文参考博客[4]中分词的调用方法，共实现了9种分词模式的调用，供读者参考，完整代码在`ch7/nlu/seg/hanlp_util.py`中。下面展示3种分词模式的调用方法。

```
from pyhanlp import HanLP  
# CRF分词器  
crf_seg = HanLP.newSegment('crf')  
# 感知机分词器  
perceptron_seg = HanLP.newSegment('perceptron')  
# 维特比算法分词器  
viterb = HanLP.newSegment('viterbi')  
  
def hanlp_crf(s):  
    # CRF分词器  
    res = crf_seg.seg(s)  
    words = [term.word for term in res]  
    return ' '.join(words)  
  
def hanlp_perceptron(s):  
    # 感知机分词器  
    res = perceptron_seg.seg(s)  
    words = [term.word for term in res]  
    return ' '.join(words)  
  
def hanlp_viterbi(s):  
    # 维特比算法分词器  
    res = viterb.seg(s)  
    words = [term.word for term in res]  
    return ' '.join(words)  
```

2\. 词性标注

词性标注获取的是词汇级别的语义特征，对于问答系统来说，大多数情况下实体为名词（N）词性，属性或者关系为名词（N）或动词（V），其他一些语气词、助词、副词可能往往不会是问答的信息词，因此可以将词性作为信息词抽取的参考。

