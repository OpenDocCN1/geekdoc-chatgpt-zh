
```
ques_expr_reg = re.compile("什么|啥|多少|何|怎么|怎样|吗|哪|谁|什么地方" \  
                           "|多大|多高|多细" \  
                           "|几月|几号|几岁|几种" \  
                           "|是男是女|好不好|是不是" \  
                           "|(是.+还是.+)")  
hello_intent_reg = re.compile('你好|hello|hi|早上好|中午好|晚上好')  
  
def reg_match(sent):  
    # 基于正则模板的意图识别方法  
    intent = ''  
    if ques_expr_reg.search(sent):  
        intent = 'qa'  
    elif hello_intent_reg.search(sent):  
        intent = 'hello'  
    else:  
        intent = 'chat'  
    return intent  
```

将用户输入的问句分别与各类意图的正则模板进行匹配，命中则返回对应的意图。当模板数量积累到一定量时，可以参考6.3节问答系统中的模板管理方法，将句式模板以特定的JSON格式存放，例如：

```
{  
    "qa": {  
        "regex": [  
            [  
                "(?P<subject>.+)的(?P<predicate>.+)(是|为|称|叫|在|属于|属)({what}|{which})",  
                "例句:姚明的身高是多少"  
            ],  
            [  
                "(?P<subject>.+)的(?P<predicate>.+)(是|为|称|叫|在|属于|属)(?P<object>.+)",  
                "例句:阿里巴巴的总部在杭州吗"  
            ],  
            [  
                "(?P<object>.+)(是|为|属于|称|叫)(?P<subject>.+)(的)(?P<predicate>.+)",  
                "例句:深圳是腾讯的总部地点吗"  
            ],  
            [  
                "(?P<subject>.+)(和)(?P<object>.+)的({attributes_list})({which})({compare_how})",  
                "例句：北京和上海的面积哪个更大"  
            ]  
        ]  
    },  
    "hello": {  
        "regex": [  
            [  
                "({morning}|{noon}|{afternoon}|{hello})({modal_particle})?({punc}*)?$",  
                "你好 / 早上好 / Hi"  
            ]  
        ]  
    }  
}  
```

在模板中还可以预设一些槽位信息，例如<subject>、<predicate>和<object>，在后续模块中进行消歧处理可进一步提升意图识别准确率。其中用到的变量的值如下：

```
{  
    "basic": {  
        "what": "咋样的|啥样的|什么东西|什么|什么样的|啥|嘛|多少年?的?|什么样|怎么样|多大|多高|几号|几|何种|多细|怎样|如何|几何|什么宗教",  
        "which": "哪|哪些|哪个|哪支|哪项|哪只|哪种|哪家|哪部|哪类|哪款|哪所|哪(几|一)(种|类|些|部|行|位|个|本|首|门|款)|什么类别|哪个部位|哪种类型|几种|哪一|哪方面|哪些方面|哪个公司|哪个国家|哪国|哪个单位|哪个企业",  
        "who": "谁|哪一?个人|哪些人|什么人|哪个家伙|哪一?个|哪一?位|哪几个",  
        "morning": "早上好|早晨好|早安|上午好",  
        "noon": "中午好|午安",  
        "afternoon": "下午好",  
        "hello": "你好|hello|Hello|hi|Hi|嗨|好久不见|你在吗",  
        "night": "晚上好|晚安",  
        "compare_how": "更?大|更?小|更?宽|更?窄|更?长|更?短|更?高|更?矮|更?重|更?轻|更?早|更?晚|更?多|更?少|更?低",  
        "punc": "[！？。。～！？。。，….﹏.!,.?~]",  
        "modal_particle": "的|阿|啊|啦|唉|呢|吧|了|哇|呀|吗|哦|噢|喔|呵|嘿|哎|嘛|哟|呗|噢|呐|罢",  
        "attributes_regex": "性别|年龄|年纪|岁数|生日|出生日期|逝世日期|忌日|去世时间|星座|工作|职业|工种|血型|血液|祖国|国籍|国家|老家|祖籍|出生地|身高|高度|体重|重量|成就|父母|朋友|小名|别名|代称|别称|民族|毕业学校|毕业院校|学历|教育|最高学历|信仰|户口|籍贯|户籍|父亲|母亲|户口所在|职称|官职|作品|职务|三围|腰围|臀围|胸围|总部地点|总部地址|总部所在地|总部|经营范围|经营的范围|性质|口号|员工数量|员工人数|年营业额|注册资本|注册资金|董事长|创始人|总资产|股票代码|证券代码|品牌|法定代表人|法人代表|上线|发布|经营理念|产品|企业精神|公司精神|企业类型|公司类型|类型|作者|写的|类别|书名|出版社|出版时间|特点|特色|特征|优点|优势|开本|装帧|页数|译者|字数|纸张类型|版本|版次|楼盘名称|楼盘名|定价|价格|面积|开发商|楼盘|物业|地址|城市|城区|学科|绿化率|建筑形式|容积率|物业费|户数|房价|交房|分布区域|生活区域|区域|科目|纲目|目的|属性|简称|全称|地点|所在地|定义|含义|概念|简介|释义|意思|功效|效果|作用|官网|官方网站|行政区域|领域|隶属|内容|组成|本质|实质|宗旨|应用|前身|用途|网站名称|来源|词性|词性|原因|人口|背景|实施|理念|职能"  
    }  
} 
```

模板解析的方法可参考6.3节中的`build_regexes()`方法，后续的消歧策略也可参考6.3节，本节不再赘述。

## 3. 分类模型

意图识别还可以看作一个多分类任务，当意图数据积累到一定量时，可以采用分类模型来实现意图识别。传统基于机器学习的分类方法将文本分类任务拆分成特征工程构建和训练分类器两部分。特征工程构建通常需要包括文本预处理、特征提取、文本表示三个部分，其目的是将文本特征转换成计算机可理解的表示形式。特征工程的特征可以采用基础NLU方法提取的信息（如分词、词性、依存句法信息），还可以采用基于文本的统计信息等。文本表示可以采用N-Gram词袋模型、向量空间模型，以及根据每个特征的重要性进行加权的表示形式。常见的权重计算方法有布尔权重、TF-IDF型权重以及基于熵的权重，其中布尔权重是指出现为1，未出现则为0，也就是词袋模型；TF-IDF型权重是根据词频信息来定义权重的；基于熵的权重则是将出现在同一文档的特征赋予较高的权重。对于一个任务来说，特征工程构建的特征越能表征文本的信息，分类模型就越能获得较好的分类效果。主流的机器学习分类器有决策树、朴素贝叶斯、SVM以及GBDT/XGBOOST等。传统分类方法存在的主要问题是文本表示是高纬度高稀疏的，特征表达能力很弱。随着神经网络研究的不断成熟，一些基于神经网络提取文本特征的方法涌现出来，例如利用CNN、RNN等网络结构自动获取特征表达能力，去掉繁杂的人工特征工程，从而端到端地解决分类问题。其中具有代表性的方法是`fastText`分类模型，核心采用层次Softmax方法以快速地进行模型的训练和预测，在多分类任务上表现优秀。本节将展示利用`fastText`训练文本分类的方法，完整代码在`ch7/nlu/intent_classification.py`中。

### (1) 数据准备

如表7-2所示，准备带有标签的若干数据，并做好分词。原始数据位于`ch7/nlu/data/intent_data.xlsx`中。

表7-2　意图分类训练数据示例

![000](images/00186.jpeg)

### (2) 数据集拆分

`dataset_split()`函数用于拆分训练集、验证集和测试集，核心调用了`sklearn.model_selection`中的`train_test_split`函数，实现对数据集的按比例划分，划分比例参数为`test_size`。`test_size=0.2`表示划分20%的数据用作测试，80%的数据用于训练。

```
from sklearn.model_selection import train_test_split  
  
def write_list_into_file(object, filename):  
    # 将list写入文件  
    with open(filename, 'w', encoding='utf8') as outfile:  
        for line in object:  
            outfile.write(line + '\n')  
  
def dataset_split(data_file):  
    '''
    拆分train_data、dev_data和test_data 
    '''  
    df = pd.read_excel(data_file)  
    X = df['sent_cut'].tolist()  # 分好词的句子  
    y = df['label'].tolist()  
    # 拆分训练、测试、验证集  
    X_train_dev, X_test, y_train_dev, y_test = train_test_split(X, y, test_size=0.2)  
    X_train, X_dev, y_train, y_dev = train_test_split(X_train_dev, y_train_dev, test_size=0.2)  
    # 将训练数据与标签组装  
    train_data = train_data_format(X_train, y_train)  
    test_data = train_data_format(X_test, y_test)  
    dev_data = train_data_format(X_dev, y_dev)  
    # 写入文件  
    write_list_into_file(train_data, 'train_data.txt')  
    write_list_into_file(test_data, 'test_data.txt')  
    write_list_into_file(dev_data, 'dev_data.txt')  
```

其中的`train_data_format()`函数用于将`sent`和`label`组装成`fastText`训练的特定格式，例如分好词的`sent`为“你好 呀 。” ，对应的意图`label`为“hello”，组装后的数据格式为“`__label__hello` 你好 呀 。”具体代码如下。

```
def train_data_format(X, y, label='__label__'):  
    '''
    :param X: 训练数据[type:list] 
    :param y: 训练数据的标签[type:list] 
    ''' 
    data_set = []  
    for sent, lab in zip(X, y):  
        try:  
            data_set.append(label + str(lab) + ' ' + sent)  
        except:  
            print(sent, lab)  
    return data_set  
```

### (3) 模型训练

下面进行模型训练。

```
from fasttext import train_supervised  
from sklearn.metrics import classification_report  
  
# 设置参数  
kwargs = {'lr': 0.4, 'epoch': 10, 'wordNgrams': 4, 'dim': 300, 'minCount': 10, 'minn': 1, 'maxn': 3, 'bucket': 500000, 'loss': 'softmax'}  
  
def fasttext_train(train_data, test_data, model_path, **kwargs):  
    ''' 
    训练模型、测试和输出各意图的PRF值 
    :param train_data: 训练数据.txt文件 
    :param test_data: 测试数据.txt文件 
    :param model_path: 保存模型的名称 
    :param kwargs: 训练参数列表 
    '''  
    # 训练  
    clf = train_supervised(input=train_data, **kwargs)  
    clf.save_model('%s.bin' % model_path)  
    # 测试  
    result = clf.test(test_data)  
    precision = result[1]  
    recall = result[2]  
    print('Precision: {0}, Recall: {1}\n'.format(precision, recall))  
    # 输出每类PRF值  
    test_sents, y_true = split_sent_and_label(test_data)  
    y_pred = [i[0].replace('__label__', '') for i in clf.predict(test_sents)[0]]  
    print(classification_report(y_true, y_pred, digits=3)) 
```

其中`split_sent_and_label()`函数为`train_data_format()`的反函数，将`fastText`训练的数据格式拆分成句子和对应标签，实现代码如下。

```
def split_sent_and_label(filename):  
    # 拆分带标签的数据为句子列表和标签列表  
    sents, labels = [], []  
    with open(filename, encoding='utf8') as infile:  
        for line in infile:  
            line = line.strip()  
            label, sent = line.split(' ', 1)  
            label = label.replace('__label__', '')  
            sents.append(sent)  
            labels.append(label)  
  
    return sents, labels  
```

### (4) 模型测试

按如下代码进行模型测试。

```
import fasttext as ft  
  
def test(test_file, model_path):  
    # 测试模型并输出各类意图的PRF值  
    clf = ft.load_model(model_path)  
    test_sents, y_true = split_sent_and_label(test_file)  
    y_pred = [i[0].replace('__label__', '') for i in clf.predict(test_sents)[0]]  
    # 输出各类标签的PRF值  
    print(classification_report(y_true, y_pred, digits=3))  
```

### (5) 模型预测

对模型进行预测，实现代码如下。

```
import fasttext as ft  
  
def predict(sent_list, model_path, topN=3):  
    # 预测句子list的结果  
    clf = ft.load_model(model_path)  
    # 预测结果  
    res = clf.predict(sent_list, k=topN)  
    # 预测标签  
    y_pred = [[lab.replace('__label__', '') for lab in labs] for labs in res[0]]  
    # 预测概率  
    y_prob = [[p for p in probs] for probs in res[1] ]  
    # 组装topN的预测结果  
    topN_pred = list(zip(y_pred,y_prob))  
    return topN_pred  
```

基于以上代码就可以实现一个基于`fastText`文本分类模型的数据预处理、模型训练、测试和预测的全部功能了。

### 7.2.4 实体识别与链接
