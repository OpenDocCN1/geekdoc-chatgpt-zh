
## 实体抽取方法概述

目前实体抽取有两种主流方法。第一种是通过命名实体识别，如利用系统自带的NLP处理函数，识别出待抽取句子的人名、地名、组织机构名等实体信息，但是该方法需要针对每个命名实体类别训练各自的命名实体识别，在缺少训练数据的情况下不可靠，但优势是可以实现新词发现。另一种方法是构建相关类别的字典，通过搜寻字典来识别实体，该方法可以准确识别候选实体，缺点是无法发现新的候选实体，因此可以将两种方法结合做实体抽取。

上面提到候选实体的抽取通过外部模块`map_company_mention.py`实现，这里我们将两种方法结合来做候选实体抽取。编写字典过滤函数，构建公司领域的字典树，获取函数`map_company_mention`中每个句子`sentence_text`的文本信息，在待抽取文本`sentence_text`中匹配字典子串来实现候选实体抽取，将存在字典树中的字符串作为候选实体输出。此外，通过命名实体识别的结果`ner_tags`找出连续NER标记为`ORG`的序列，在这里，连续是为了找出最长匹配的子串，如“小米科技有限公司”被分成[“小米”，“科技”，“有限”，“公司”]，相应的NER结果为[‘ORG’,‘ORG’,‘ORG’,‘ORG’]，因此需要获取最长匹配子串，并将分词结果拼接后得到候选实体。

在本步骤的最后，执行命令：

```
$ deepdive compile && deepdive do company_mention
```

系统会自动将相关信息存储到公司候选实体表中。

## (5) 候选实体对生成

上一个步骤完成了公司候选实体的抽取，而关系抽取的目的是抽取句子中已标记实体对之间的语义关系，因此在关系抽取之前要完成候选实体对的抽取。本步骤需要抽取一个句子中同时出现两个不同公司的候选实体对，它们代表着存在符合待抽取关系的可能性。同样地，先定义一张存放候选实体对的表，命名为`supplier_candidate`，抽取候选实体对需要两个候选实体的id和名字，因此表的设计形式如下：

```
@extraction
supplier_candidate(
    p1_id  text,
    p1_name text,
    p2_id  text,
    p2_name text
).
```

`p1_id`和`p1_name`表示公司A候选实体的id和名字，`p2_id`和`p2_name`表示公司B候选实体的id和名字。此外再定义一个候选实体对抽取函数，代码如下所示：

```
function map_supplier_candidate over(
    p1_id  text,
    p1_name text,
    p2_id  text,
    p2_name text
) returns rows like supplier_candidate
implementation "udf/map_supplier_candidate.py" handles tsv lines.
```

候选实体对的内容分别来自之前定义的候选实体表`company_mention`，最后以候选实体对的形式存放在`supplier_candidate`表中，还可以通过一些先验规则进行约束，如统计句子中的公司候选实体的数量，代码如下所示：

```
num_company(doc_id, sentence_index, COUNT(p1)):-
    company_mention(p1, _, doc_id, sentence_index, _, _).
```

然后执行如下代码：

```
supplier_candidate += map_supplier_candidate(p1, p1_name, p2, p2_name) :-
    num_company(same_doc, same_sentence, num_p)
    company_mention(p1, p1_name, same_doc, same_sentence, p1_begin, _),
    company_mention(p2, p2_name, same_doc, same_sentence, p2_begin, _),
    num_p <= 5,
    p1_name != p2_name,
    p1_begin != p2_begin.
```

先验规则有以下几条：

*   `p1_name != p2_name`，表示一个句子中候选实体的名字不能相同；
*   `p1_begin != p2_begin`，表示一个句子中候选实体的起始位置不能相同；
*   `num_p <= 5`，表示一个句子中候选实体的个数需小于等于5个。

读者也可以根据自己项目的实际情况自行修改。最后执行如下代码生成候选实体对`supplier_candidate`表。

```
$deepdive compile && deepdive do supplier_candidate
```

## (6) 特征抽取

上一步骤抽取到了候选实体对，但是无法得知实体对之间是否存在本项目需要抽取的供应商关系，因此本步骤将抽取候选实体对之间的文本特征，供模型判别供应商关系是否存在。同样，先定义一个关系特征表，用于存放实体对和特征，命名为`supplier_feature`，表的设计结构如下所示：

```
@extraction
supplier_feature(
    p1_id  text,
    p2_id  text,
    feature  text
).
```

`p1_id`表示公司A候选实体的id，`p2_id`表示公司B候选实体的id，`feature`表示从句子中抽取的特征。

为了得到候选实体对之间的特征，需要定义一个特征抽取函数，而前面步骤3中NLP处理的结果可用于特征抽取，因此该特征抽取函数的输入可以是`sentences`表的NLP处理结果，输出可以是NLP组合的各种特征。特征抽取函数`extract_feature`的代码如下所示：

```
function extract_feature over(
    p1_id  text,
    p2_id  text,
    p1_begin_index  int,
    p1_end_index  int,
    p2_begin_index  int,
    p2_end_index  int,
    doc_id  text,
    sentence_index  int,
    tokens  text[],
    lemmas  text[],
    pos_tags  text[],
    ner_tags  text[],
    dep_types  text[],
    dep_tokens  int[]
)return rows like supplier_feature
implementation "udf/extract_feature.py" handles tsv lines.
```

同理，函数需要一个外部特征抽取模块函数来抽取特征。在`udf/`目录下编写`extract_feature.py`，该模块调用了DeepDive自带的`ddlib`库，可得到各种词性标注、命名实体识别、词序列的窗口特征。读者也可以自行定义特征抽取规则。

抽取函数`extract_feature`中的列`p1_begin_index`和`p1_end_index`分别表示公司A候选实体的起始索引和结束索引，`p2_begin_index`和`p2_end_index`分别表示公司B候选实体的起始索引和结束索引，内容引用自之前定义的`company_mention`表，而类似`tokens`、`lemmas`等NLP处理结果可引用`sentences`表中的内容，因此把`company_mention`表和`sentences`表做join操作，把得到的结果作为`function extract_feature`函数的输入，最后将结果输出到`supplier_feature`表中，代码如下所示：

```
supplier_feature += extract_feature(
    p1_id, p2_id, p1_begin_index, p1_end_index, p2_begin_index, p2_end_index,
    doc_id, sentence_index, tokens, lemmas, pos_tags, ner_tags, dep_types, dep_tokens
    ) :-
    company_mention(p1_id, _, doc_id, sentence_index, p1_begin_index, p1_end_index),
    company_mention(p2_id, _, doc_id, sentence_index, p2_begin_index, p2_end_index),
    sentences(doc_id, sentence_index, _, tokens, lemmas, pos_tags, ner_tags, _, dep_types, dep_tokens).
```

最后键入命令生成特征表。

```
$deepdive compile && deepdive do supplier_feature
```

## (7) 样本标注

DeepDive使用远程监督和启发式规则的方法来对样本进行标注。需要准备一些已标注的实体对作为训练数据以及待标注的候选实体对，通过规则从候选实体对中标出一部分正例和负例。因此可在`app.ddlog`中定义一个关系标签表`relation_label`，该表用于存放候选实体对、标注规则和标签，表的格式如下所示：

```
@extraction
relation_label(
    @key
    @references(relation='has_relation', column='p1_id', alias='has_relation')
    p1_id text,
    @key
    @references(relation='has_relation', column='p2_id', alias='has_relation')
    p2_id text,
    @navigable
    label  int,
    @navigable
    rule_id  text
).
```

当一个关系中的列引用另一个关系作为主键的列时，需要提供`@references`注释。上述`relation_label`关系中的列`p1_id`和`p2_id`引用了关系`has_relation`中定义的主键列`p1_id`和`p2_id`，因此需要`@references`注释，其中参数`relation`表示引用的关系名，参数`column`表示引用的列名，同时，DeepDive定义当一个关系中的列引用另一个关系超过一次时，需要提供别名参数`alias`加以区别。在本实例中，关系`relation_label`中的列`p1_id`和`p2_id`引用关系`has_relation`两次，因此需要提供别名参数`alias`。列`label`代表标签，表示待抽取关系上的相关性，数据类型是`int`，为正值时表示正相关，为负值时表示负相关，绝对值越大则相关性越强。`rule_id`代表决定标签相关性的规则名称。

接着进行初始化定义。通过之前生成的`supplier_candidate`候选实体对表导入所有的候选实体对，初始`label`定义为0，初始`rule_id`定义为NULL.

```
relation_label(p1, p2, 0, NULL):- supplier_candidate(p1, _, p2, _).
```

导入所有的候选实体对之后，将知识库中的先验数据导入`relation_label`表中，`rule_id`标记为`'from_dbdata'`，知识库中的数据往往具有较强的正相关性，因此可将`label`权重设置为+3，相关代码如下所示：

```
relation_label(p1, p2, 3, 'from_dbdata'):-
    supplier_candidate(p1, p1_name, p2, p2_name),
    supplier_dbdata(n1, n2),
    [lower(n1) = lower(p1_name), lower(n2) = lower(p2_name)].
```

由于本项目抽取的“供应商”关系是有方向的，即当文本只提及关系“公司B是公司A的供应商”时，反向关系“公司A是公司B的供应商”不成立，因此采取补充负例的策略，使得系统可以正确区分关系的方向。本项目复用原先准备的数据，仅调换公司A和公司B的位置，并把`label`权重设置为-3，使系统能学习相应特征，相关代码如下所示：

```
relation_label(p1, p2, -3, 'from_dbdata'):-
    supplier_candidate(p1, p1_name, p2, p2_name),
    supplier_dbdata(n1, n2),
    [lower(n2) = lower(p1_name), lower(n1) = lower(p2_name)].
```

在导入过程中，为了防止英文大小写问题导致的实体对齐出错，可以调用`lower()`函数将`n1`、`n2`、`p1_name`、`p2_name`转换成小写。对于未知样本，可以通过启发式规则的方法进行预标记，因此还需要编写一个函数，将`extract_feature`中的内容抽取到`relation_label`表中，函数命名为`supervise_relation`，代码如下所示：

```
function supervise_relation over(
    p1_id  text,
    p1_begin_index  int,
    p1_end_index  int,
    p2_id  text,
    p2_begin_index  int,
    p2_end_index  int,
    doc_id  text,
    sent_index  int,
    sent_text  text,
    tokens  text[],
    lemmas  text[],
    pos_tags  text[],
    ner_tags  text[],
    dep_types  text[],
    dep_tokens  int[]
) return rows like relation_label
implementation "udf/supervise_relation.py" handles tsv lines.
```

`supervise_relation.py`是启发式规则的定义和标签计算函数，对于本例，可以获取包含“公司B是公司A的供应商”、“公司B入围公司A供应商名单”等句式的正样本，也可获取包含“公司A控股公司B”、“公司A持有公司B股份”等句式的负样本，并对正负样本打上相应的标签。

细心的读者可能会问，当若干条规则同时适用同一候选实体对，该做如何处理？

DeepDive提供了一种规则冲突的解决办法，称为投票机制。当远程监督生成的标签和启发式规则生成的标签发生冲突，或者不同规则生成的标签产生冲突时，DeepDive会采用多数裁定原则（Majority Vote）解决该冲突。

例如，一个候选对在知识库中找到了映射，标签为+3，同时满足一条启发式规则，得到标签为-1。使用多数裁定原则对所有标签求和：SUM= (+3) +(-1) = +2，最终得到的标签为+2。

因此我们可以将不同规则抽取的标签加入`relation_label`表中，主要对`supplier_candidate`、`company_mention`、`sentences`三个表中的标签做求和操作，在多条规则和知识库标记的结果中，为每对实体投票。执行代码如下：

```
relation_label_resolved(p1_id, p2_id, SUM(vote)) :- relation_label(p1_id, p2_id, vote, rule_id).
```

最后编译并执行：

```
$deepdive compile && deepdive do relation_label_resolved
```

生成`relation_label_resolved`表，实现样本标注。

## (8) 因子图构建

上述的第1～7步都可以称为准备过程，以产生训练关系抽取模型所需的数据、特征等。DeepDive采用因子图模型，在构建模型前需要确定`supplier_candidate`表中的（公司A，公司B）供应商关系是否真的存在，因此在这一过程中，又需要定义一张最终存储的表，命名为`has_relation?`，其中`?`表示此表是用户模式下的变量表，即需要推导关系的表。这里需要预测的是公司之间是否存在这样一种关系，表的格式如下所示：

```
@extraction
has_relation?(
    @key
    @references(relation="company_mention", column="mention_id", alias="p1")
    p1_id  text,
    @key
    @references(relation="company_mention", column="mention_id", alias="p2")
    p2_id  text
).
```

上面提到关系表`relation_label`中的列`p1_id`和`p2_id`引用关系表`has_relation`，在这里又可以看到关系表`has_relation`中的`p1_id`和`p2_id`是引用自关系表`company_mention`中的列`mention_id`，也验证了本项目最终要抽取的是候选实体之间的关系。

将通过远程监督和启发式规则获得的标签放入已知变量中，再根据`SUM(vote)`的结果来进行判断，如果`SUM(vote)`大于0，则公司A和公司B之间存在供应商关系，如果`SUM(vote)`小于0，则说明不存在关系，如果`SUM(vote)=NULL`，说明还需要进一步判断，相关代码如下：
