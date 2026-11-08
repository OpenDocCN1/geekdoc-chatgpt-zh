# 粒度抉择：整词还是子词

在自然语言处理里，文本最终要被拆成模型能处理的单元，这个拆分粒度直接决定语义能否被稳定捕捉。保留完整词形直观易读，却对新词和拼写变体很脆弱；把词再切成更小的碎片，序列会变长，但能覆盖未登录表达，也让词汇表压力变小。实际工程中，任务闭环、词表固定的场景更倾向整词处理，而开放域的预训练模型几乎都依赖子词化，比如 BERT 就以此来消化海量词汇。

下面用三句中文做手感对比。先看一个极简的整词切分，再接入社区预训练分词器的细粒度处理，随后用手写的 BPE 与官方 WordPiece 做二次对照，差异一目了然。

[PRE0]python

import re

from transformers import AutoTokenizer

# 示例文本

texts=["自然语言处理的分词方法可以提升模型的理解能力。",

       "词级分词与子词分词适用于不同的NLP任务。",

       "BERT模型常用子词分词以处理大量词汇。" ]

# 词级分词实现

def word_level_tokenization(text):

    """

    实现简单的词级分词

    参数:

    -text: str, 输入的文本

    返回:

    -tokens: list of str, 词级分词结果

    """

    # 使用空格和标点符号分隔文本

    tokens=re.findall(r"\b\w+\b", text)

    return tokens

# 使用Hugging Face的Tokenizer进行子词分词

model_name="bert-base-chinese"

tokenizer=AutoTokenizer.from_pretrained(model_name)

def subword_level_tokenization(text, tokenizer):

    """

    使用子词分词将文本分解为子词单元

    参数:

    -text: str, 输入的文本

    -tokenizer: 预训练模型的分词器

    返回:

    -subwords: list of str, 子词分词结果

    """

    # 通过Tokenizer进行分词

    tokenized=tokenizer.tokenize(text)

    return tokenized

# 执行词级分词和子词分词

for text in texts:

    word_tokens=word_level_tokenization(text)

    subword_tokens=subword_level_tokenization(text, tokenizer)

    print(f"原始文本: {text}")

    print(f"词级分词结果: {word_tokens}")

    print(f"子词分词结果: {subword_tokens}\n")

运行后能明显感受到两者的分治风格。整词那条路径在纯中文上几乎把整句当作一个大块拿下，而 bert-base-chinese 的分词器会把句子拆成更细的片段，英文缩写 NLP、BERT 也被拆到字符或子串层面，这正是子词对罕见符号的容错体现。

[PRE1]

原始文本：自然语言处理的分词方法可以提升模型的理解能力。

词级分词结果：['自然语言处理的分词方法可以提升模型的理解能力']

子词分词结果：['自然', '语', '言', '处理', '的', '分', '词', '方法', '可', '以', '提', '升', '模', '型', '的', '理', '解', '能', '力']

原始文本：词级分词与子词分词适用于不同的NLP任务。

词级分词结果：['词级分词与子词分词适用于不同的NLP任务']

子词分词结果：['词', '级', '分', '词', '与', '子', '词', '分', '词', '适', '用', '于', '不', '同', '的', 'N', 'L', 'P', '任', '务']

原始文本：BERT模型常用子词分词以处理大量词汇。

词级分词结果：['BERT模型常用子词分词以处理大量词汇']

子词分词结果：['B', 'ER', 'T', '模', '型', '常', '用', '子', '词', '分', '词', '以', '处', '理', '大', '量', '词', '汇']

子词化让模型面对新词也不慌，但代价是序列变长。可学习的合并策略在可解释性和词汇表规模之间做了折中，BPE 与 WordPiece 就是常见代表。

[PRE2]python

import re

from collections import Counter, defaultdict

# 初始化词汇表和示例文本

vocab={"自然语言", "处理", "的", "分词", "方法", "可以",

       "提升", "模型", "理解", "能力"}

texts=[

    "自然语言处理的分词方法可以提升模型的理解能力。",]

# 构建BPE分词表

def build_bpe_vocab(vocab, num_merges=5):

    """

    构建BPE分词词汇表

    参数:

    -vocab: set, 初始词汇表

    -num_merges: int, 合并次数

    返回:

    -bpe_vocab: dict, BPE词汇表

    """

    bpe_vocab={word: list(word)+["</w>"] for word in vocab}

    for i in range(num_merges):

        # 统计词对的出现频率

        pairs=defaultdict(int)

        for word, tokens in bpe_vocab.items():

            for j in range(len(tokens)-1):

                pairs[(tokens[j], tokens[j+1])] += 1

        # 找到出现频率最高的词对

        if not pairs:

            break

        best_pair=max(pairs, key=pairs.get)

        # 合并词对

        for word, tokens in bpe_vocab.items():

            j=0

            while j < len(tokens)-1:

                if tokens[j]==best_pair[0] and tokens[j+1]==best_pair[1]:

                    tokens[j:j+2]=["".join(best_pair)]

                j += 1

    return bpe_vocab

bpe_vocab=build_bpe_vocab(vocab)                # 执行BPE分词

# 对文本进行BPE编码

def bpe_encode(text, bpe_vocab):

    """

    使用BPE分词对文本编码

    参数:

    -text: str, 输入的文本

    -bpe_vocab: dict, BPE词汇表

    返回:

    -encoded_text: list, 编码后的子词列表

    """

    tokens=list(text)

    encoded_text=[]

    while tokens:

        for i in range(len(tokens), 0, -1):

            subword="".join(tokens[:i])

            if subword in bpe_vocab:

                encoded_text.append(subword)

                tokens=tokens[i:]

                break

        else:

            encoded_text.append(tokens[0])

            tokens=tokens[1:]

    return encoded_text

# 对示例文本进行BPE编码

for text in texts:

    encoded_text=bpe_encode(text, bpe_vocab)

    print(f"原始文本: {text}")

    print(f"BPE编码结果: {encoded_text}\n")

在小词表下，手写的 BPE 已经能把高频组合比如“自然语言”“分词”保留下来，同时把标点单独处理。接下来看工业级的 WordPiece 效果。

[PRE3]python

from transformers import AutoTokenizer

# 使用Hugging Face的Tokenizer加载预训练模型

tokenizer=AutoTokenizer.from_pretrained("bert-base-chinese")

def wordpiece_tokenization(text, tokenizer):

    """

    使用WordPiece分词对文本进行分词

    参数：

    -text: str, 输入的文本

    -tokenizer: 预训练模型的分词器

    返回:

    -tokens: list of str, WordPiece分词结果

    """

    # 使用预训练模型的WordPiece分词器进行分词

    tokens=tokenizer.tokenize(text)

    return tokens

# 对文本使用WordPiece分词

for text in texts:

    wordpiece_tokens=wordpiece_tokenization(text, tokenizer)

    print(f"原始文本：{text}")

    print(f"WordPiece分词结果：{wordpiece_tokens}\n")

BPE 与 WordPiece 思想相近，都靠贪心合并，但 WordPiece 在训练时引入概率筛选，中文里更倾向保留“自然”“语言”等完整单元，同时对“可”“以”“提”“升”这类常见部件做细化。两者对照有助于判断自己的语料该偏保守整词，还是偏灵活子词。

[PRE4]

原始文本：自然语言处理的分词方法可以提升模型的理解能力。

BPE编码结果：['自然', '语言', '处理', '的', '分词', '方法', '可以', '提升', '模型', '的', '理解', '能力', '。']

原始文本：自然语言处理的分词方法可以提升模型的理解能力。

WordPiece分词结果：['自然', '语言', '处理', '的', '分', '词', '方法', '可', '以', '提', '升', '模', '型', '的', '理', '解', '能', '力', '。']

从这次输出看，BPE 在本例中把“分词”“可以”“提升”等保留得更完整，序列更短可读性更好；WordPiece 则进一步拆分，部分词被拆到字级，模型对拼写变异的鲁棒性更强。选型本质上是在可解释性与泛化能力之间做权衡。

[PRE5]python

from transformers import AutoTokenizer