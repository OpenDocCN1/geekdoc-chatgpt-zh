| 原理 | 主要靠规则对词尾做截断，把不同变体的词压回到同一个词干或根片段，实现方式多是简单的字符串操作，比如去掉 -ing、-ed、-s 等常见后缀 | 会结合词的形态学和语法信息，把词还原成合法的基础词形，即词元，过程通常依赖词典和语法规则的支持，复杂度更高 |
| 适用场景 | 适合追求速度、对精度容忍度较高的粗处理，比如检索、文档分类。目的是把同一词族的多种写法映射成一个共同代表，降低词汇表大小 | 适合对准确性要求高的精细任务，比如自然语言理解、机器翻译等。能保证还原后仍是语法上成立的真实词 |
| 示例 | running、ran、runs 都会被归到词干 run | running 还原为词形 run，better 还原为词形 good |

总体来看，选择哪种方式要看任务对效率和精确度的取舍。词干化实现简单、快，但准确性有限；词形还原更严谨、可读性好，却需要更多计算和语言资源。

### 1.2.2 词干化
常用的词干化实现有以下几种。

#### 1. Porter Stemming算法
Porter Stemming是较早且使用最广的基于规则的词干化方案，通过一系列模式匹配逐步去掉词尾。该方法在信息检索等领域被大量采用。实例1-3演示了借助 NLTK 库执行 Porter Stemming 的过程，展示把词还原到基础形式的效果。

##### 实例1-3：使用Porter Stemming算法将单词还原到它们的基本形式
实例文件gan.py（源码路径：codes\1\gan.py）的具体实现代码如下。

[PRE0]

在上述代码中，先用 Porter Stemming 对词表做词干化，再并排展示原始词与处理后的词干，可以看到 running 被处理为 run 等。

执行上述代码，输出结果如下。

[PRE1]

#### 2. Snowball Stemming算法
Snowball Stemming 又称 Porter2，是对 Porter 的改进版，在保留规则思想的同时修复了一些缺陷，准确性提升且支持多语言。实例1-4展示了用 NLTK 执行 Snowball Stemming 的用法。

##### 实例1-4：使用Snowball Stemming算法将单词还原到它们的基本形式
实例文件gan02.py（源码路径：codes\1\gan02.py）的具体实现代码如下。

[PRE2]

在上述代码中，Snowball 对 jumping 的处理结果为 jump，Porter 在同样输入下也会得到 jump。两者在某些词上结果会不一致，本例突出了 Snowball 对英语语法细节处理的细致性。

执行上述代码，输出结果如下。

[PRE3]

#### 3. Lancaster Stemming算法
Lancaster Stemming 同样基于规则，但策略更激进，倾向于把词截到更短的片段。适用于特定需求，但可能出现过度截断。

实例1-5是一个使用NLTK库执行Lancaster Stemming算法的例子。

##### 实例1-5：使用NLTK库执行Lancaster Stemming算法
实例文件gan03.py（源码路径：codes\1\gan03.py）的具体实现代码如下。

[PRE4]

在上述代码中，对一组词执行 Lancaster 词干化并对比原词。可以看到结果普遍更短。例如 easily 被处理为 easy，而 Porter 或 Snowball 会保留更多词干。该例体现了 Lancaster 的激进特性。

执行上述代码，输出结果如下。

[PRE5]

#### 4. Lovins Stemming算法
Lovins Stemming 是另一种规则型方案，相较 Porter 和 Lancaster 更保守，保留的词形更多，不易过度裁剪。实例1-6展示了使用简化版 Lovins 规则进行词干化。

##### 实例1-6：使用自定义的Lovins Stemming算法词干化单词
实例文件gan04.py（源码路径：codes\1\gan04.py）的具体实现代码如下。

[PRE6]

上述代码演示了仅对以 ing 和 ly 结尾的词做处理的简化 Lovins 规则。实际的 Lovins 包含更复杂的规则集。

执行上述代码，输出结果如下。

[PRE7]

#### 5. Regex-Based Stemming算法
借助正则表达式也可以做词干化，先定义后缀匹配模式，再统一替换。这种方式可针对任务定制，但通用性和鲁棒性通常不如成熟的规则算法。实例1-7展示了基于正则的自定义词干化。

##### 实例1-7：使用正则表达式来执行Regex-Based Stemming算法
实例文件gan05.py（源码路径：codes\1\gan05.py）的具体实现代码如下。

[PRE8]

在上述代码中，通过正则匹配特定后缀再移除。示例里定义了去掉 ing 和 ly 的规则，可按需扩展。

执行上述代码，输出结果如下。

[PRE9]

### 1.2.3 词形还原
词形还原与词干化不同，它会考虑语法和语境，确保输出是合法的词。以下介绍常用的词形还原技术。

#### 1. WordNet Lemmatizer
WordNet 是英语词汇库，NLTK 提供的 WordNet Lemmatizer 利用其中的词形数据把词还原为基本形式。实例1-8演示了该方法的使用。

##### 实例1-8：使用WordNet Lemmatizer()方法执行词形还原
实例文件huan.py（源码路径：codes\1\huan.py）的具体实现代码如下。

[PRE10]

在上述代码中，使用 WordNet Lemmatizer 将词还原为基本形式。参数 pos 用来指定词性，本例多为动词故设为 “v”。根据需求可调整。

执行上述代码，输出结果如下。

[PRE11]

#### 2. spaCy
spaCy 是功能强大的 NLP 库，提供多语言的词形还原能力。实例1-9演示了使用流程。

##### 实例1-9：使用spaCy库进行词形还原
（1）使用以下命令安装spaCy库并下载英语模型。

[PRE12]

（2）使用spaCy库的词形还原功能，实例文件huan02.py（源码路径：codes\1\huan02.py）的具体实现代码如下。

[PRE13]

在上述代码中，先加载英语模型，再把文本送入 NLP 管道，最后提取词形还原结果并展示原文与还原结果。

执行上述代码，输出结果如下。

[PRE14]

#### 3. StanfordNLP
StanfordNLP 是斯坦福大学发布的 NLP 工具包，提供高质量的词形还原功能。实例1-10展示了使用方法。

##### 实例1-10：使用StanfordNLP进行词形还原
实例文件huan03.py（源码路径：codes\1\huan03.py）的具体实现代码如下。