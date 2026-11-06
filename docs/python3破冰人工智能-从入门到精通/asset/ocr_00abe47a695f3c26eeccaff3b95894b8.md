$idf_i = \log \frac{|D|}{\{|j: t_i \in d_j\}| + 1}$

注意，上述公式中分母加 1 是为了避免分母为 0。

其中，|D|是语料库中的文档总数。

|{j: t_i ∈ d_j}| 表示包含词语 t_i 的文件数目（即 n_{i,j} ≠ 0 的文件数目），如果该词语不在语料库中，就会导致被除数为零，因此一般情况下使用 |{j: t_i ∈ d_j}| + 1。

最后，TF-IDF 公式为：

$\text{TF-IDF}=tf_{i,j}\times idf_i$

# 2. 基于 TF-IDF 算法的关键词抽取

本部分将以 Jieba 分词下的 TF-IDF 为例进行实战，首先针对 jieba.analyse.extract_tags 进行参数说明。

```
jieba.analyse.extract_tags(sentence, topK=20, withWeight=False, allowPOS=())
```

+   - sentence: 为待提取的文本。
+   - topK: 表示返回关键词个数，默认值为 20。
+   - withWeight: 为关键词权重值，默认值为 False。
+   - allowPOS: 指定词性，默认值为 空，即不筛选。

# 【例 8-4】Jieba 分词的关键词提取

输入：

```
import jieba.analyse
import math
```

text="人工智能在国内获得快速发展，国家相继出台一系列支持人工智能发展的政策，各大科技企业也争相宣布其人工智能发展战略，资本更是对这一新兴领域极为倾心。作为新一轮产业变革的核心驱动力，中国的人工智能发展正在进入新阶段，而且中国有望成为引领全球人工智能发展的重要引擎。"

```
keywords1=jieba.analyse.extract_tags(text)
print('关键词提取'+'/'.join(keywords1))
```

输出：

```
关键词提取 人工智能/发展/倾心/驱动力/争相/引擎/引领/变革/新一轮/各大/极为/相继/新兴/出台/中国/核心/更是/科技/一系列/快速
```

输入：

```
keywords_top=jieba.analyse.extract_tags(text, topK=3)
```