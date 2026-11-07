# 5分钟上手语言模型数据预处理

[PRE0]python
# 用极简语句集搭起实验语料
sentences=['我', '喜欢', '玩具', '', '我', '爱', '爸爸', ' ', '我', '讨厌', '挨打']
# 把所有句子拼接、切分并去重得到唯一词表
word_list=list(set(' '.join(sentences).split()))
# 为每个词分配一个唯一数字编号
word_to_idx={word:idx for idx, word in enumerate(word_list)}
# 构建编号到词的反向映射
idx_to_word={idx: word for idx, word in enumerate(word_list)}
voc_size=len(word_list) # 统计最终词表规模
print(' 词汇表:', word_to_idx) # 展示映射结果
print( '词汇表大小:', voc_size) # 打印词表长度

[PRE1]python
import torch # 载入 PyTorch
import random # 载入随机工具
batch_size=2# 单次批处理样本数
def make_batch():
    input_batch=[]# 装输入序列的列表
    target_batch=[]# 装目标值的列表
    selected_sentences=random.sample(sentences, batch_size) # 从语料中随机抽样

[PRE2]python
for sen in selected_sentences: # 对抽到的句子逐个处理
    word = sen.split() # 以空格切分为词
    # 前面所有词构成输入序列
    input = [word_to_idx[n] for n in word[:-1]] # 映射为索引
    # 最后一个词作为预测目标
    target = word_to_idx[word[-1]] # 取目标索引
    input_batch.append(input) # 累加到输入批次
    target_batch.append(target) # 累加到目标批次
    input_batch = torch.LongTensor(input_batch) # 转换成长整型张量
    target_batch = torch.LongTensor(target_batch) # 转换成长整型张量
    return input_batch, target_batch # 输出批次对

[PRE3]python
input_batch, target_batch = make_batch() # 获取一次批数据
print("输入批处理数据:", input_batch) # 显示输入张量
###### 将索引还原为可读词形
input_words = []
for input_idx in input_batch:
    input_words.append([idx_to_word[idx.item()] for idx in input_idx])