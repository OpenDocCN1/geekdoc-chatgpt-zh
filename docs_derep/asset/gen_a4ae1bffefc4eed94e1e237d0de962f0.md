玩转 Python 文本分析：从炫酷词云到深度语义洞察

文本数据处理从来不只是枯燥的数字堆砌，今天我们就用三个实战案例，带你体验从可视化展示、可读性量化到 NLP 核心能力的完整进阶路线。

### 一、 词云可视化：让高频词“跳”出心形轮廓

首先搞定环境依赖，`conda-forge` 源下的 `wordcloud` 包兼容性最稳。

[PRE0]

conda install -c conda-forge wordcloud

读取素材文本，这里以经典剧本《罗密欧与朱丽叶》为例，`pathlib` 处理路径跨平台无压力。

[PRE1]

In [1]: from pathlib import Path

In [2]: text = Path('RomeoAndJuliet.txt').read_text()

想要词云长成特定造型？得先用 `imageio` 读入一张掩码图片（如心形轮廓 `mask_heart.png`），白色区域将承载文字。

[PRE2]

In [3]: import imageio

In [4]: mask_image = imageio.imread('mask_heart.png')

实例化 `WordCloud` 对象时，`colormap='prism'` 赋予绚丽配色，`background_color='white'` 设定白底，`mask` 参数则套用刚才的心形模版。

[PRE3]

In [5]: from wordcloud import WordCloud

In [6]: wordcloud = WordCloud(colormap='prism', mask=mask_image,

...     background_color='white')

...

核心一步：`generate()` 方法直接吞入全量文本，自动完成分词、词频统计与布局计算。

[PRE4]

In [7]: wordcloud = wordcloud.generate(text)

成品落盘为 PNG，分辨率足够清晰，方便后续分享或嵌入报告。

[PRE5]

In [8]: wordcloud = wordcloud.to_file('RomeoAndJulietHeart.png')

在 Notebook 里即时预览？开启 `matplotlib` 内联模式，`imshow` 直接渲染对象，记得关掉坐标轴更美观（此处代码省略了 `plt.axis('off')`）。

[PRE6]

%matplotlib

[PRE7]

import matplotlib.pyplot as plt

plt.imshow(wordcloud)

---

### 二、 可读性量化：用硬指标拆解文本难度

光看词云不过瘾，`textatistic` 库能把文本“体检”出十几项专业指标。先装包：

[PRE8]

pip install textatistic

同样读入剧本文本，实例化 `Textatistic` 对象即可自动完成句法与词汇层面的统计。

[PRE9]

In [1]: from pathlib import Path

In [2]: text = Path('RomeoAndJuliet.txt').read_text()

[PRE10]

In [3]: from textatistic import Textatistic

In [4]: readability = Textatistic(text)

设置显示精度为小数点后三位，调用 `.dict()` 一键导出全量指标字典。

[PRE11]

In [5]: %precision 3

Out[5]: '%3.3f'

In [6]: readability.dict()

Out[6]:

{'char_count': 115141,

 'word_count': 26120,

 'sent_count': 3218,

 'syb1_count': 30166,

 'notdalechall_count': 5823,

 'polysyblword_count': 549,

 'flesch_score': 100.892,

 'fleschkincaid_score': 1.203,

 'gunningfog_score': 4.087,

 'smog.score': 5.489,

 'dalechall_score': 7.559}

**解读关键看点**：
- **Flesch Reading Ease 100.89**：极易阅读，接近小学水平；
- **Flesch-Kincaid Grade 1.2 / Gunning Fog 4.09 / SMOG 5.49**：均指向极低年级阅读门槛；
- **Dale-Chall 7.56**：虽高于前几项，但仍属平易近人范畴。
莎翁原著在现代拼写规范下，词汇与句法复杂度远低于当代学术文本，这组数据印证了“通俗大师”的标签。

---

### 三、 spaCy 实战：实体识别与语义相似度双管齐下

工业级 NLP 首选 `spaCy`，安装后别忘下载小模型 `en_core_web_sm`（命令行别名 `en`）。

[PRE12]

conda install -c conda-forge spacy

[PRE13]

python -m spacy download en

载入管线，几毫秒完成分词、词性标注、依存分析及实体识别全流程。

[PRE14]

In [1]: import spacy

In [2]: nlp = spacy.load('en')

喂入一段包含时间、人名、机构名的测试句，`document.ents` 直接吐出结构化实体三元组。

[PRE15]

In [3]: document = nlp('In 1994, Tim Berners-Lee founded the ' +

    ...:     'World Wide Web Consortium (W3C), devoted to ' +

    ...:     'developing web technologies')

    ...:

[PRE16]

In [4]: for entity in document.ents:

    ...:     print(f'{entity.text}: {entity.label_}')

    ...:

1994: DATE

Tim Berners-Lee: PERSON

the World Wide Web Consortium: ORG

**进阶玩法：文档级语义相似度**。基于词向量平均池化，`similarity()` 能给出 0-1 间的语义距离。

重新初始化管线（或复用上文 `nlp`），分别加载《罗密欧与朱丽叶》与《爱德华二世》全文。

[PRE17]

In [1]: import spacy

In [2]: nlp = spacy.load('en')

[PRE18]

In [3]: from pathlib import Path

In [4]: document1 = nlp(Path('RomeoAndJuliet.txt').read_text())

In [5]: document2 = nlp(Path('EdwardTheSecond.txt').read_text())

输出 **0.935** 的高相似度，佐证了同一作者、同一时期、同一体裁戏剧在词汇分布与主题空间上的高度重合。

[PRE19]

In [6]: document1.similarity(document2)

Out[6]: 0.9349950179100041

---

**总结**：从 `wordcloud` 的直观美感，到 `textatistic` 的量化严谨，再到 `spaCy` 的深度语义理解，三行主流库串起文本分析的“观、测、悟”三重境界。代码虽短，组合拳打出的洞察力却不容小觑。