词向量与对话管理代码速览

下面是一组在相似度计算、词向量使用和多轮对话管理上的实战片段，保留了核心逻辑但用更轻松的方式串起来。

加权字符串匹配的核心思路
先用 difflib 的 SequenceMatcher 把两串字符的匹配块找出来。没有权重时，就把所有匹配字符数累加，用 2*匹配数 / (长度和) 归一化；有权重时，未提供的权重默认补 1.0 并校验长度，再按匹配位置取 w1 与 w2 的均值求和，最后同样用 2*加权匹配 / 权重和 得到比例。若长度为 0 则直接返回 1.0。

def weighted_match_similarity(sent1, sent2, w1=None, w2=None):  

    ''' 

    计算两个字符串的加权相似度 

    :param sent1: 

    :param sent2: 

    :param w1: w1 是一个与 sent1 长度相同的权重序列 

    :param w2: w2 是一个与 sent2 长度相同的权重序列 

    '''  

    mb = difflib.SequenceMatcher(None, sent1, sent2).get_matching_blocks()  

    if not w1 and not w2:  # 如果均不加权重  

        matches = sum(triple[-1] for triple in mb)  

        return calculate_ratio(matches, len(sent1) + len(sent2))  

    else:  

        if not w1:  

            w1 = len(sent1) * [1.0]  

        if not w2:  

            w2 = len(sent2) * [1.0]  

        # 校验权重序列是否和字符串等长  

        assert len(sent1)==len(w1)  

        assert len(sent2)==len(w2)  

        matches = sum((w1[tri[0] + i] + w2[tri[1] + i]) / 2 for tri in mb for i in range(tri[-1]))  

        return calculate_ratio(matches, sum(w1) + sum(w2))  

def calculate_ratio(matches, length):  

    if length:  

        return 2.0 * matches / length  

    return 1.0  

[PRE0]
词向量文件的加载与查询方式
KeyedVectors 的 load_word2vec_format 可以读文本或二进制格式，腾讯 AI Lab 的中文向量通常用二进制加载，首次载入耗时在二三十分钟左右。加载后获取向量有两种写法，model['词'] 和 model.wv['词'] 效果一致。

from gensim.models.keyedvectors import KeyedVectors  

def word_embeding_txt_to_bin():  

    # 加载通常需要 25 分钟~30 分钟  

    wv = KeyedVectors.load_word2vec_format(WORD_VECTOR_PATH,binary=False)

    wv.load_word2vec_format('/Users/winnie/software_package/Tencent_AILab_ChineseEmbedding.bin', binary=True)  

[PRE1]

WORD_VECTOR_PATH = 'Tencent_AILab_ChineseEmbedding.bin'  

Model = KeyedVectors.load_word2vec_format(WORD_VECTOR_PATH, binary=True)

# 获取一个词的向量方式 1  

print(model['中国'])  

# 获取一个词的向量方式 2  

print(model.wv['中国'])  

[PRE2]
基于 subword 的 FastText 训练流程
语料读取时调用 ltp 分词得到词表。FastText 训练使用 skip-gram，维度 200，窗口 5，最小词频 2，字符 n-gram 范围 1~5，迭代 5 次，多核并行后保存模型。训练好的模型同样支持查词向量和 OOV 生成。

from gensim.models import FastText  

def read_corpus(filename):  

    ''' 

    读取语料并调用 ltp 分词工具 

    :param filename: 语料文件 

    :return: 

    '''  

    sent_list = []  

    with open(filename, 'r', encoding='utf8') as fp:  

        for line in fp:  

            sent_list.append(ltp_seg_handler.segment(line.strip()))  

    return sent_list  

def train_fasttext_embedding(corpus_file):  

    ''' 

    训练 fastText 基于 subword 信息的词向量模型 

    :param corpus_file: 输入语料文本数据 

    :return: 

    '''  

    sentences = read_corpus(corpus_file)  

    model = FastText(sentences, size=200, sg=1, window=5, min_count=2, min_n=1, max_n=5, workers=4, iter=5)  

    model.save('fasttext_wv_model.bin')  

[PRE3]

sentences = [['我', '是', '中国', '人'], ['我', '的', '家乡', '在', '北京']]

[PRE4]

# 模型加载  

model = FastText.load('fasttext_wv_model.bin')  

# 获取一个词的向量方式 1  

print(model['中国'])  

# 获取一个词的向量方式 2  

print(model.wv['中国']) 

# 获取一个 OOV 词向量的方式  

print(model['奥利给 222'])  

print(model.wv['奥利给 222'])  

# 获取一个词最相近的 topn 个词  

print(model.wv.most_similar("漂亮", topn=10)) 

[PRE5]
相似度与距离的实用封装
相似度调用 model.wv.similarity，若异常则回退 0。欧氏距离通过向量相减平方求和开根得到，常用于衡量空间分离程度。

def get_similarity(model, w1, w2):  

    ''' 

    求 w1 和 w2 之间的相似度 

    :param model: model 为加载进内存的词向量模型 

    :param w1: 第一个词 

    :param w2: 第二个词 

    :return: 

    '''  

    try:  

        sim = model.wv.similarity(w1, w2)  

    except Exception as e:  

        print(e)  

        sim = 0  

    return sim  

def euclidean_distance(model, w1, w2):  

    # 欧氏距离计算两个词相似度  

    vec1 = model[w1]  

    vec2 = model[w2]  

    dis = np.sqrt(np.sum(np.square(vec1-vec2)))  

    return dis  

[PRE6]
对话管理的主干结构
对话管理依赖日志、问答、闲聊、状态机等模块。主处理函数接收用户 id 与 NLU 结果，基于历史 DST 做多轮意图推断，记录日志并在意图为 qa 时扩充候选服务列表。

from loguru import logger

from qa import bot_qa, common_qa, medicine_qa, user_qa, ir_qa, mr_qa

from chat import hello_chat, association_chat

from dm.dst_manager import get_dst, add_dst

from util.multi_thread_util import thread_factory

[PRE7]

def dm_main_handler(user_id, nlu_dict):

    """

    对话管理主处理函数

    :param user_id: 用户 id

    :param nlu_dict: NLU 执行结果字典

    :return: 本轮对话执行结果

    """

[PRE8]

# 候选服务列表

candidate_service_list = []

# 从 dst 中获取前几轮的候选结果

dst_list = get_dst(user_id)

logger.info("DST Candidate: {}".format(str(dst_list)))

dst = multi_turn_policy(nlu_dict, dst_list)

logger.info("DST: {}".format(str(dst_list)))

# 加入多轮意图

if dst and dst["service"] == "qa":

    candidate_service_list.extend(["bot_qa", "common_qa", "medicine_qa", "user_qa"])

[PRE9]

def get_dst(user_id):

    """

    获取状态机中的 dst 信息，每个用户对应维护一个状态机

    :param user_id: 用户 id

    :return:

    """

    valid_dst_list = []

    dst_list = dst_dict.get(user_id, None)