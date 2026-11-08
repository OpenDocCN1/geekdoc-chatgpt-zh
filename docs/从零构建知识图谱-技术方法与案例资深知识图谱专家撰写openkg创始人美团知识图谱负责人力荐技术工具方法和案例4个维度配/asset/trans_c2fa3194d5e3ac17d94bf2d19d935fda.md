

# 知识图谱问答消歧与查询

## 性别属性消歧

```
def disambiguation_gender(self, triple, sent):  
    ''' 
    属性"性别"的消歧处理 
    :param triple: 输入一个候选的三元组 
    :param sent: 用户输入的问句 
    :return: 返回结果为两个：一个是该三元组是否符合gender属性， 
             另一个是消歧后的三元组信息 
    '''  
    #  判别subject词和object是否错误提取了属性词
    if re.search(attributes_regex, triple['object']) or re.search(attributes_regex, triple['subject']):  
        return [False, {}]  
    # 排除subject词是一些无意义的表述的情况  
    if re.search(general_pattern, triple['subject']):  
        return [False, {}]  
    match_male = re.search(reg_male, triple['object'])  
    match_female = re.search(reg_female, triple['object'])  
    # 排除subject词提取错误的情况  
    regex_1 = '(.+)是(.+)'  
    if re.search(regex_1, triple['subject']):  
        return [False, {}]  
    if match_male :  
        triple['object'] = '男'  
    elif match_female:  
        triple['object'] = '女'  
    else:  
        triple['object'] = 'x?'  
  
    return [True, triple] 
```

在`gender`属性的消歧方法中，先判断`subject`和`object`提取的内容是否属于明显错误的情况，例如将属性值作为`subject`和`object`槽位提取出来，判断方法是将提取的槽位内容与`attributes_regex`匹配，命中`attributes_regex`则返回`False`，直接排除该候选三元组。然后判断提取的槽位内容是否是无意义或其他错误类。均判断完毕后，再将多样化的性别表述转化为标准的说法“男”或“女”。其中用到的`attributes_regex`、`general_pattern`、`reg_male`和`reg_female`变量定义如下：

```
attributes_regex = "性别|年龄|年纪|岁数|生日|出生日期|逝世日期|忌日|去世时间|星座|工作|职业|工种|血型|血液|祖国|国籍|国家|老家|祖籍|出生地|身高|高度|体重|重量|成就|父母|朋友|小名|别名|代称|别称|民族|毕业学校|毕业院校|学历|教育|最高学历|信仰|户口|籍贯|户籍|父亲|母亲|户口所在|职称|官职|作品|职务|三围|腰围|臀围|胸围|总部地点|总部地址|总部所在地|总部|经营范围|经营的范围|性质|口号|员工数量|员工人数|年营业额|注册资本|注册资金|董事长|创始人|总资产|股票代码|证券代码|品牌|法定代表人|法人代表|上线|发布|经营理念|产品|企业精神|公司精神|企业类型|公司类型|类型|作者|写的|类别|书名|出版社|出版时间|特点|特色|特征|优点|优势|开本|装帧|页数|译者|字数|纸张类型|版本|版次|楼盘名称|楼盘名|定价|价格|面积|开发商|楼盘|物业|地址|城市|城区|学科|绿化率|建筑形式|容积率|物业费|户数|房价|交房|分布区域|生活区域|区域|科目|纲目|目的|属性|简称|全称|地点|所在地|定义|含义|概念|简介|释义|意思|功效|效果|作用|官网|官方网站|行政区域|领域|隶属|内容|组成|本质|实质|宗旨|应用|前身|用途|网站名称|来源|词性|词性|原因|人口|背景|实施|理念|职能"  
general_pattern = "哪里|什么|哪一位|哪位|哪一样|哪几个|哪一个|哪个|哪"  
reg_male = "(成年|青年|幼年|老年|壮年)?(大叔|雄性|汉子|小伙子|少爷|爷们|纯爷们|帅哥|你哥|公子|男神|男生|男性|男人|男|绅士|小鲜肉|小男孩)"  
reg_female = "(成年|青年|幼年|老年|壮年)?(女性|女生|女神|女的|女|雌性|妹子|姑娘|美女|妹子|妹纸|萌妹纸|萌妹子|软妹|软妹纸|软妹子|女神|小姐|母|女子|宫|御姐)"  
```

## 国籍属性消歧

在国籍属性消歧中，同样做了通用的`subject`、`object`实体词校验。在对`object`提取的内容进行标准化时，将国家名称列表（`country.txt`）构建成一棵`trie`树，通过`trie`树匹配的方法查看能否匹配到国家名称，并做标准化处理。`trie`树匹配的方法能够提升三元组的质量，提升查询的准确性。

```
def disambiguation_nationality(self, triple, sent):  
    ''' 
    属性"国籍"的消歧处理 
    :param triple: 输入一个候选的三元组 
    :param sent: 用户输入的问句 
    :return: 返回结果为两个：一个是该三元组是否符合该三元组，  
             另一个是消歧过后的三元组信息 
    '''  
    # 排除subject词和object提取内容错误的情况，例如将属性值匹配到了subject和object槽位的情况  
    if re.search(attributes_regex, triple['object']) or re.search(attributes_regex, triple['subject']):  
        return [False, {}]  
    # 排除subject词是一些无意义的表述的情况  
    if re.search(general_pattern, triple['subject']):  
        return [False, {}]  
    replace_word = '属不'  
    pattern_0 = '隶属|前身|影响'  
    triple['subject'] = re.sub(replace_word, '', triple['subject'])  
    # 排除一些相似属性的干扰  
    if re.search(pattern_0, sent):  
        return [False, {}]  
    # 剔除国籍+人的情况，仅保留国籍信息  
    if '人' in triple['object']:  
        triple['object'] = triple['object'].replace('人', '')  
    if triple['object'] != 'x?':  
        # 通过字典树匹配的方式来确定提取的object是否在国籍字典里  
        trie_result = self.trie_matcher.trie_match(triple['object'])  
        if 'country' in trie_result:  
            triple['object'] = trie_result['country'][0]  
            return [True, triple]  
    else:  
        triple['object'] = 'x?'  
        return [True, triple]
```

对于消歧没有通过的三元组，返回`False`和空的三元组，排除错误三元组候选答案。另外，在该模块中，当问询问题的值涉及数字的属性时，例如身高、体重、时间等，还需要在消歧模块中对数字做标准化处理，使之能够与图谱存放的数据格式相匹配。例如：字符串型“十八岁”转为`int`型的数字`18`，`100`斤转为`50kg`。其余属性消歧的完整代码可参考`kgqa`项目下的`template_match/args_disambiguation.py`文件。

## 候选三元组排序策略

有时做完消歧后，仍然还剩多个候选三元组，此时需要进一步做排序操作，下面提供几种排序策略供读者参考。

1）模板设置优先级。通常为了能尽可能覆盖更多的问句，每个问题类型都会写一些约束相对宽松的模板，有些模板则比较严格，原则上来说，越是严格的模板，其准确率越高，所以其优先级越高，而对于特别宽泛的模板，优先级要设置低一些。因此可以根据提取到的三元组所在的模板优先级顺序进行排序。

2）由于是采用模板匹配的方式，那么句式模板匹配到的内容占整个输入句子的比重越高，一定程度上说明该模板与输入问句更“契合”。因此可以将匹配子串占全句长度的比重作为三元组排序的一个参考。

3）将查询到的三元组进行`KG`查询，判断其是否一个真实存在的三元组，对不存在的三元组进行剔除。

## 查询构建

通过实体链接和消歧模块得到标准查询三元组后，需要构建图谱查询语句进行结果的查询。下面针对三类不同的问题介绍如何构造查询模板。

### 基于单三元组查询 object

1）基于单三元组，根据`subject`和`predicate`查询`object`的结果，例如：(`subject`: “周杰伦”, `predicate`: “国籍”, `object`: “x?”)。

```
def kg_search_o_by_sp(subject_id, predicate):  
    ''' 
    根据subject_id和predicate查询object 
    :param subject_id: subject结点 
    :param predicate: 属性或者关系 
    :return: 返回subject在predicate属性下对应的值 
    '''  
    value = kg_dao.search_spo(subject_id, predicate)  
    if not value:  
        # 当无法匹配时，获取predicate的相似表述再进行查询  
        predicate_similarities = get_similarities(predicate)  
        for sim_pred in predicate_similarities:  
            value = kg_dao.search_spo(subject_id, sim_pred)  
            if value:  
                return value  
    if not value:  
        return None  
    return value 
```

此类问题是最基础的查询问题，通过输入`subject`结点的`id`和`predicate`查询图谱中`object`的值。具体的，通过`kg_dao.search_spo()`方法传入`subject_id`和`predicate`查询返回`object`的值。为了提升查询匹配度，可以通过获取`predicate`相似表述的方法再次查询验证，当匹配结果为空时，返回`None`，否则返回`object`的值。`kg_dao.search_spo()`的操作主要是知识图谱底层的查询操作，读者可以根据自己实际的图谱结构，编写对应的查询代码，本文提供了一种基于`Neo4j`的实现供读者参考，详细代码位于`kgqa`项目下的`data_access/kg_dao.py`文件中。

### 基于单三元组校验

2）基于单三元组，校验`subject`、`predicate`，`object`是否满足查询条件，例如：(`subject`: “周杰伦”, `predicate`: “国籍”, `object`: “中国人”)。

```
def kg_check_o_by_sp(subject_id, predicate, object):  
    ''' 
    将实体链接后的三元组进行KG校验 
    :param subject_id: subject结点的id 
    :param predicate: 属性或者关系 
    :param object_id: predicate的值 
    :return: 返回校验结果，True 或者 False 
    '''  
    value = kg_dao.search_spo(subject_id, predicate)  
    if value:  
        # 若能匹配成功，返回True  
        if value in object or object in value:  
            return True  
    # 当无法匹配时，获取predicate的相似表述再进行查询  
    predicate_similarities = get_similarities(predicate)  
    for sim_pred in predicate_similarities:  
        value = kg_dao.search_spo(subject_id, sim_pred)  
        if value:  
            # object校验的策略  
            if value in object or object in value:  
                return True  
    return False  
```

此类问题是常见的是否类查询问题，通过图谱查询，校验提取的三元组是否成立。与`kg_search_o_by_sp`方法不同，这里需要将图谱查询得到的`object`值与问句中提取的`object`值进行比对。本文采用了一种匹配策略，当查询结果`value`是`object`的子串或者`object`是`value`的子串时均视为正确匹配，以提升结果的匹配度。该种策略仅供参考，读者可以在实际操作中根据具体的图谱数据情况和问题情况，设计不同的匹配策略。

### 多跳查询

3）多跳查询的复杂问题，例如：(`subject`: “美人鱼”, `predicate`: “主要成就”, `object`: “`subject_jump`:导演”)。这类问题需要进行多跳查询，实际上是两个查询三元组的组合，第二个查询三元组需要依赖于第一个三元组的查询结果再进行查询，即先基于三元组 (`subject`:“美人鱼”, `predicate`:“导演”, `object`:“x?”)查出美人鱼的导演是“周星驰”，再将其作为第二个三元组(`subject`: “周星驰”, `predicate`:“主要成就”, `object`:“x?”)的查询主体`subject`进行“主要成就”的查询。

```
def kg_check_jump_qa(subject_id, predicate, object):  
    ''' 
    复杂问题利用多跳查询进行三元组校验 
    :param subject_id: subject结点的id 
    :param predicate: 属性或者关系 
    :param object: 多跳信息值 
    :return: 返回校验或查询结果 
    '''  
    # 当存在subject_jump时，属于一个二跳查询问题  
    if object.startswith('subject_jump'):  
        first_predicate = object.split(':', 1)[1]  
        # 通过Neo4j关系查询获取第二个三元组的subject的id  
        second_subject_id = kg_dao.get_node_relation(subject_id, first_predicate)  
        if second_subject_id:  
            value = kg_dao.search_spo(second_subject_id, predicate)  
            if value:  
                # object校验的策略  
                if value in object or object in value:  
                    return True  
    else: # 当非多跳查询问题时，执行传统的单spo三元组查询方法  
        if object != 'x?':  
            return kg_check_o_by_sp(subject_id, predicate, object)  
        else:  
            return kg_search_o_by_sp(subject_id, predicate)  
    return False  
```

`kg_check_jump_qa()`函数首先判断提取的三元组是否是一个多跳查询问题，当`object`中包含“`subject_jump`”开头的标识时，将多跳查询的三元组拆分，进行两次分开的查询获得查询结果，以防止多个三元组查询的错误传递问题。该函数的实现中没有加入相似度的匹配方式，读者可以根据实际需求设计查询方法。`kg_dao.get_node_relation()`函数实现的功能是根据输入结点的`id`获得相关联的结点`id`的过程，在`Neo4j`中可以视为关系查询（区别于属性查询的结果是值），具体实现可参考项目代码：`data_access/kg_dao.py`。`kg_dao.search_spo()`函数的实现如下：

