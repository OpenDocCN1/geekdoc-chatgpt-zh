
## 模板变量管理
例子中模板用到的变量有`verb_is`、`includes`、`what`、`which`等，每个变量都对应不同的表述。同样，我们也可以通过JSON格式对变量进行管理和配置。在一些模板中，一些变量的组合是频繁出现的，比如：`what`、`which`、`who`的一些表述可以混用，
* “周杰伦的老婆是哪个人？”（which表述）
* “周杰伦的老婆是谁？”（who表述）
重复定义变量无疑会为后期维护带来较大的工作量，每次在模板中都填写多个变量（例如`{what}|{which}|{who}`）的形式是不友好的，可以定义组合变量（`combo`）来解决这个问题。因此模板变量的管理分为两个层次，一是基础变量`basic`层，仅由字符串和正则元字符构成，二是`combo`层，除了支持字符串和正则元字符外，还可以由`basic`中的正则变量组合而成，例如：
```
{  
 "combo": {  
     "what_which": "{what}|{which}",  
     "what_which_who": "{what}|{which}|{who}",
     },  
 "basic": {  
     "verb_is": "是|为|称|叫作?|在|属于",  
     "includes": "是|有|包括|包含",  
     "what": "咋样的|啥样的|什么东西|什么|什么样的|啥|嘛|多少年?的?|什么样|怎么样|多大|多高|几号|几|何种|多细|怎样|如何|几何|什么宗教",  
     "which": "哪|哪些|哪个|哪支|哪项|哪只|哪种|哪家|哪部|哪类|哪款|哪所|哪(几|一)(种|类|些|部|行|位|个|本|首|门|款)|什么类别|哪个部位|哪种类型|几种|哪一|哪方面|哪些方面|哪个公司|哪个国家|哪国|哪个单位|哪个企业",
     "who": "谁|哪一?个人|哪些人|什么人|哪个|哪个家伙|哪一个|哪位|哪几个|哪一位"  
 }  
} 
```
更多完整的模板变量存放在`data/regex_variables.json`文件中。

## (2) 模板生成
和模板相关的操作均封装在`RegexMatching`类中，在`regex_matching.py`文件中，核心函数`build_regexes()`将模板库中的模板与模板变量组装编译成正则表达式，可供后续匹配使用，代码如下：
```
1. def build_regexes(self, regex_var, regex_path):  
2.     """ 
3.     加载模板正则表达式中的变量，将其与模板组装成一个完整正则表达式，并编译
4.     :param var_path: 模板变量文件的路径 
5.     :param regex_path: 模板库文件的路径 
6.     :return: 字典数据结构，其中包含加载了正则变量编译后的正则表达式，供后续匹配使用 
7.     """  
8.     for key in regex_var['basic']:  
9.         # 将basic变量的值存放在对应的regex_variable_dict中  
10.        self.regex_variable_dict[key] = regex_var['basic'][key]  
11.
12.    for key in (regex_var['combo']):  
13.        # 找出combo变量中用到的basic变量  
14.        mm = re.findall("\{([^\d]+?)\}", regex_var['combo'][key])  
15.        # 将combo匹配到的变量对应的变量值替换到combo变量中  
16.        used_vars = {var: self.regex_variable_dict[var] for var in set(mm)} 
17.        val = regex_var['combo'][key].format(**used_vars)  
18.        self.regex_variable_dict[key] = val  
19.
20.    # 读取模板库中的全部模板配置  
21.    regex_dict = util.read_json(regex_path)  
22.    for rel in regex_dict:  
23.        for regex in regex_dict[rel]["regex"]:  
24.            regex_expr = regex[0]  
25.            # 找出正则模板中用到的全部变量  
26.            mm = re.findall("\{([^\d]+?)\}", regex_expr)  
27.            if mm:  
28.                # 对匹配到的变量去重，形成替换映射字典  
29.                used_vars = {var: self.regex_variable_dict[var] for var in set(mm)}  
30.                # 将变量组装到正则模板中  
31.                reg = regex_expr.format(**used_vars)  
32.            else:  # mm == []，针对以前不含变量的正则表达式  
33.                reg = regex_expr  
34.            regex.append(re.compile(reg)) # 编译正则表达式并附加到列表中
35.    return regex_dict
```
`build_regexes`函数主要包括两部分：第一部分对应第8～18行，解析编译模板变量，第二部分对应第20行以后代码，实现的是将模板库中每个模板的变量，用编译后的模板变量进行替换和编译，供后续匹配使用。

## (3) 模板匹配
以一般疑问句为例，将输入的问句与编译后的模板库进行匹配，如果能匹配到一般疑问句类型的问题模板，则该问句是一个已支持的一般疑问句问题类型，并且根据匹配到的槽位信息即可获得模板定义中的三元组信息，通过槽位名称对应成候选三元组列表。该模板匹配过程同时完成了问句的分析以及三元组提取工作，具体实现也在`regex_matching.py`文件中，代码如下：
```
def get_candidates_for_yes_no_question(self, sent):  
    ''' 
    匹配一般疑问句模板，获得候选三元组信息 
    :param sent: 用户输入的问句 
    :return: 候选三元组列表
    '''  
    triple_candidates = []  
    for template_type in self.regex_template_yes_no:  
        for regex in self.regex_template_yes_no[template_type]['regex']: 
            # regex的构成[原始配置模板, 例句, 加载变量后编译好的正则表达式]  
            m = regex[2].search(sent)  
            if m: # 如果匹配到结果  
                matched_slots = m.groupdict() # 取出对应槽位的值  
                candi = {}  
                candi['subject'] = matched_slots['subject']  
                # 实体校验：基于图谱中全部实体词构建的trie树结构匹配提取出的subject子串内容是否是一个实体  
                matched_subject = entity_trie.trie_match(candi['subject'])  
                candi['subject'] = self.max_len_match(candi['subject'], matched_subject)  
                candi['predicate'] = matched_slots['predicate']  
                # 属性校验：基于图谱中全部属性构建的trie树结构匹配提取出的predicate子串内容是否是一个属性值  
                matched_predicate = property_trie.trie_match(candi['predicate'])  
                candi['predicate'] = self.max_len_match(candi['predicate'], matched_predicate)  
                candi['object'] = matched_slots['object']  
                # 去掉结尾无关紧要的语气词和标点  
                ignore_end = ['呀','吗','的','?','？','嘛']  
                if candi['object'][-1] in ignore_end:  
                    candi['object'] = candi['object'][:-1]  
                triple_candidates.append(candi)  
    return triple_candidates  
```
其中用到的`max_len_match`函数实现如下：
```
def max_len_match(self, match_str, match_results):  
    ''' 
    将问句中匹配到的子串与外部资源库如实体库、属性库、关系库匹配结果进行比较 
    将最大长度的匹配结果作为最优选择 
    :param match_str: 问句中匹配的子串 
    :param match_list: 外部资源库中匹配到的结果 
    :return: 返回校验过后的匹配结果 
    '''  
    max_len = 0  
    max_len_str = ''  
    for match in match_results:  
        if match in match_str:  
            if max_len < len(match):  
                max_len = len(match)  
                max_len_str = match  
    if max_len_str == '':  
        max_len_str = match_str  
    return max_len_str 
```
`max_len_match`实现的功能是将问句中匹配到的子串与外部资源库（如实体库、属性库、关系库）通过trie树匹配得到的结果进行比较，并选择最大长度的匹配结果作为最优匹配。

## 2. 短语映射
获取候选三元组信息后，需要将正则表达式匹配到的信息词内容与已有的知识图谱中的字段进行映射，例如`label`、`property`、`entity`等。本文介绍的是基于trie树匹配的方法。首先将图谱中的全量实体导出作为实体词典列表，接着利用Python的marisa_trie包中的`marisa_trie.Trie()`方法，将该列表构建为一个字典树。字典树在匹配时可以极大地节约时间成本，提升问答系统的效率。下面创造一个`TrieTree`类，包含构造函数和一个`trie_match`函数。构造函数实现的功能：若用户输入一个trie树文件，则自动加载，否则根据全部词条构造一棵trie树。函数`trie_match`的输入是一个句子或一个从句子中提取的带候选实体的子串，通过匹配的方法将匹配到的实体库中的词条全部返回，注意，我们仅保存每个词条的最大匹配。例如问句“周杰伦和林俊杰谁大？”，句中同时存在“周杰伦”“周杰”和“林俊杰”三个人名实体，但“周杰”是“周杰伦”的一个子串，仅保留最长匹配的结果，最后的返回结果是[“周杰伦”, “林俊杰”]。
```
class TrieTree(object):  
    def __init__(self, persis_path=''):  
        if persis_path:  
            self.trie_tree = marisa_trie.Trie()  
            self.trie_tree.load(persis_path)  
        else:  
            # 没有trie树文件，构建一棵trie树  
            keys = []  
            with open('total_entity.txt', 'r', encoding='utf8') as f:  
                for raw in f:  
                    keys.append(raw.strip())  
            # 构建trie树  
            self.trie_tree = marisa_trie.Trie(keys)  
            self.trie_tree.save('../data/trie.marisa')  # 存储trie树文件
  
    def trie_match(self, sent):  
        ''' 
        找出输入句中包含在trie树中的全部词条，并且支持最大匹配的存放，
        例如周杰伦，会返回周杰伦而非周杰 
        :param sent: 输入的句子未分词 
        :return: 匹配到的实体列表的list 
        '''  
        matched_set = set()  
        index = 0  
        while index < len(sent):  
            matched_list = self.trie_tree.prefixes(sent[index:])  
            if matched_list:  
                max_matched_word = ''  
                # 存放最大的匹配结果  
                for matched_word in matched_list:  
                    if len(matched_word) >= len(max_matched_word):  
                        max_matched_word = matched_word  
                if max_matched_word:  
                    matched_set.add(max_matched_word)  
                    index += len(max_matched_word)  
                else:  
                    index += 1  
            else:  
                index += 1  
        # 对匹配到的内容按照长度从大到小排序  
        matched_list = sorted(list(matched_set), key=lambda m: len(m), reverse=True)  
        return matched_list  
```
对信息词做了映射以后，还需要将提取到的`subject`、`predicated`、`object`等词语表述与数据库中的实体结点进行链接，由于提取的短语自然表述可能非常多样化，无法与图谱中的标准名称相匹配，为了提升实体链接的效果，通常要加入一些相似度的方法。构建图谱时，如果为每个实体词构建了`synonym`（同义词）字段，则可以在一定程度上提升匹配度。此外，还可以通过一些其他相似度的方法，例如字符串相似度和语义相似度来进行模糊匹配，具体的方法可参考6.3.2节短语映射中的相关内容。

## 3. 消歧和排序
由于问句分析的模板匹配环节可能会命中多个问句模板，所以会出现提取出多个候选三元组的情形，此时要进行消歧和排序，选择最佳的候选三元组作为结果。例如问句：“周杰伦是中国人吗？”和“周杰伦是男人吗？”
以上两个问句的句式完全相同，但是属于不同的属性查询，前者是查询“国籍”属性，后者是查询“性别”属性，然而模板无法自行识别“中国人”属于国籍、“男人”属于性别，因此每个问句都分别会匹配到两个三元组：
(`subject`: “周杰伦”, `predicate`: “国籍”, `object`: “中国人”)，
(`subject`: “周杰伦”, `predicate`: “性别”, `object`: “中国人”)，
(`subject`: “周杰伦”, `predicate`: “国籍”, `object`: “男人”)，
(`subject`: “周杰伦”, `predicate`: “性别”, `object`: “男人”)，
如何判断提取的三元组中哪个更正确？此时需要对`object`内容进行消歧，消歧就是去掉错误的三元组，保留正确的三元组的过程。下面以性别（`gender`）和国籍（`nationality`）两个属性为例，介绍两种消歧方法。
