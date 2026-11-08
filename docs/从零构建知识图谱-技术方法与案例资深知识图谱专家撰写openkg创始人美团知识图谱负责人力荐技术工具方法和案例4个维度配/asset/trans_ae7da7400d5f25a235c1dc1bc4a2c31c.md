

其中，问答主处理函数的传入参数包括用户的输入句子（`sent`），用户id（`user_id`）以及NLU解析结果的字典（`nlu_dict`）。在函数内，按照优先级对不同的问答接口进行访问。顺序是通用问答`common_qa`，领域（医药）问答`medicine_qa`，用户信息问答`user_qa`和bot图谱（人设）问答`bot_qa`。例如，首先判断通用问答，如果`common_qa.handlder`有返回值（若无返回，则是`None`），则回复内容取`common_qa.handlder`的返回值。接下来将详细介绍这四类特定的问答。

### 1. 基于知识图谱的问答模块

通用知识问答、领域知识问答（医药知识图谱）、用户知识图谱问答以及bot图谱问答的四个函数分别位于`ch7/qa/common_qa.py`、`ch7/qa/medicine_qa.py`、`ch7/qa/user_qa.py`、和`ch7/qa/bot_qa.py`中。由于具体函数细节较为类似，本小节仅展开介绍通用知识问答函数，对于其他函数处理流程，读者可以自行查阅。

```
import re
 
# 百科图谱示例
common_kg = {
    "周杰伦": {
        "星座": "摩羯座"
    },
    "韩寒": {
        "星座": "天秤座"
    }
}
    
# 语义解析规则示例
reg = re.compile(u'(?P<subject>.+)(的)(?P<predicate>.+)(是|为|称|叫|在|属于|属)(?P<object>.+)')
```

代码中给出了一个简单示例，如果图谱中含有两条数据，分别是“周杰伦星座是摩羯座”，以及“韩寒的星座是天秤座”，则用正则表达式可以判断例如“韩寒的星座是天秤座？”或者“周杰伦的星座为狮子座？”这样的问句形式。

```
def handler(user_id, nlu_dict, dst):
    """
    基于百科图谱的问答处理函数
    :param user_id: 用户id
    :param nlu_dict: NLU执行结果字典
    :param dst: 历史对话状态结果
    :return:
    """
    text = nlu_dict["input"]
    rst = None
    s = None
    p = None
    ans = None
    
    # 多轮问答
    if dst:
        multi_turn_slot = nlu_dict["slot"]["multi_turn_slot"]
        # 图谱查询
        # 尝试替换subject
        s = multi_turn_slot
        ans = common_kg.get(s)
        if ans:
            p = dst["slot"]["p"]
            ans = ans.get(p)
        # 尝试替换predicate
        if not ans:
            s = dst["slot"]["s"]
            ans = common_kg.get(s)
            if ans:
                p = multi_turn_slot
                ans = ans.get(p)
    
    # 基于示例规则的语义解析、基于示例图谱的查询
    if not ans:
        match = reg.match(text)
        if match:
            s = match.group("subject")
            p = match.group("predicate")
            # 图谱查询
            ans = common_kg.get(s)
            if ans:
                ans = ans.get(p)
    # 组装返回结果
    if ans:
        rst = {
            "service": "common_qa",
            "slot": {
                "s": s,
                "p": p,
            },
            "nlg": ans
        }
    return rst
```

在主处理函数中，首先获取用户输入`text`，然后进行多轮判断。如果多轮中的槽位信息中包括可替换的`s`和`p`，则直接获取`ans`，否则，进行图谱查询。首先提取出用户问句中的`subject`和`predicate`，分别存入`s`和`p`中。如果用户的输入句子是“韩寒的星座是天秤座么？”，则`s`为韩寒，`p`为星座。此时，通过`common_kg.get(s)`可以获取`ans`为{‘星座’: ‘天秤座’}，再通过`ans.get(p)`获取结果为天秤座。最后，返回`rst`的结果为{‘service’: ‘common_qa’, ‘slot’: {‘s’: ‘韩寒’, ‘p’: ‘星座’}, ‘nlg’: ‘天秤座’}。当然，返回值需要通过自然语言生成之后，以更自然的方式输出给用户，具体将在7.4节加以介绍。

### 2. 基于检索的问答

基于检索的问答是QA服务的补充。本节给出了基于ES和HNSW检索的两个示例。

```
from util.es_util import es_object
from util.hnsw_util import hnsw_object
    
# 示例检索库
ir_base = {
    "奥运会全称是什么": "奥林匹克运动会",
    "愚人节在几月几日": "四月一日",
    "焚书坑儒是谁干的": "秦始皇"
}
```

在以上代码中，我们先引入ES库和HNSW库，并给出一个示例检索库。包括3个问答对。

```
def handler(user_id, nlu_dict, dst, match_mode="full"):
    """
    基于检索的问答处理函数
    :param user_id: 用户id
    :param nlu_dict: NLU执行结果字典
    :param dst: 历史对话状态结果
    :param match_mode: 匹配支持三种方式，全串匹配、基于ES的字符相似度匹配、基于Embedding的向量相似度匹配
    :return:
    """
    text = nlu_dict["input"]
    rst = None
    match = None
    
    if match_mode == "full":
        match = ir_base.get(text, None)
    elif match_mode == "es":
        match = match_by_es(text)
    elif match_mode == "embedding":
        match = match_by_embedding(text)
    if match:
        rst = {
            "service": "ir_qa",
            "nlg": match
        }
    return rst
```

如上述代码所示，如果参数`match_mode`的值是`full`则是全串匹配，如果是`es`则是基于ES的字符相似度匹配，如果是`embedding`则是基于Embedding的向量相似度匹配。将获取到的用户输入与示例检索库中的三个问答对做匹配，如果匹配，则返回结果。

```
def match_by_es(text):
    """
    基于字符相似度进行检索，利用ES默认的BM25算法进行KNN查询
    :param text: 待匹配文本
    :return: 匹配上的文本
    """
    match_list = es_object.query(text, "ir_base")
    if match_list:
        return match_list[0]
    else:
        return None
    
def match_by_embedding(text):
    """
    基于Embedding相似度进行检索，利用HNSW进行KNN查询
    :param text: 待匹配文本
    :return: 匹配上的文本
    """
    labels, distance = hnsw_object.query(text)
    if labels:
        return labels[0]
    else:
        return None
```

上述函数`match_by_es`和`match_by_embedding`分别是基于的字符相似度匹配和基于Embedding的向量相似度匹配，详细可参考7.2.5节，这里不再赘述。

### 3. 基于机器阅读理解的问答

基于机器阅读理解（MR）的问答，是QA服务的补充。这里基于百度公开的`DuReader`中文阅读理解数据集，及相应的基线模型（`BiDAF`和`Match-LSTM`）作为示例，实现基于机器阅读理解的问答模块。

由于机器阅读理解属于深度学习模型，依赖于更大计算资源，适合单独微服务化运行，而不适合与问答系统服务集成部署。首先在`TensorFlow`环境中部署运行`DuReader`开源基线模型，这里可以选用`TensorFlow`官方推荐的模型服务化方式`TensorFlow Serving`。然后在问答服务中采用`POST`方式调用该模型服务，接口传入问句，返回满足置信度的答句。具体代码如下：

```
def handler(user_id, nlu_dict, dst):
    """
    基于机器阅读理解的问答处理函数
    :param user_id: 用户id
    :param nlu_dict: NLU执行结果字典
    :param dst: 历史对话状态结果
    :return:
    """
    text = nlu_dict["input"]
    try:
        body = {
            "question": text
        }
        r = requests.post('http://192.168.1.100:9000', data=json.dumps(body), timeout=2)
        rst = r.json()
        return rst["data"]["answer"]
    except Exception as e:
        return None
```

### 7.3.3 闲聊

本节给出闲聊的两个示例，分别是打招呼和联想，代码位于`ch7/chat/hello_chat.py`和`ch7/chat/association_chat.py`中。

打招呼处理函数如下所示。

```
def handler(user_id, nlu_dict, dst):
    """
    打招呼处理函数
    :param user_id: 用户id
    :param nlu_dict: NLU执行结果字典
    :param dst: 历史对话状态结果
    :return:
    """
    # 组装返回结果
    rst = {
        "service": "hello_chat"
    }
    return rst
```

在联想处理函数中，根据用户的输入问句，调用第6章提到的联想功能，获取其处理结果，并组装成最后的返回结果。具体细节可以参照第6章的相关内容。

```
from ch6.association.main_handler import sent_handler
    
def handler(user_id, nlu_dict, dst):
    """
    联想闲聊处理函数
    :param user_id: 用户id
    :param nlu_dict: NLU执行结果字典
    :param dst: 历史对话状态结果
    :return:
    """
    text = nlu_dict["input"]
    rst = None
    ans = sent_handler(text, user_id)
    if ans:
        # 组装返回结果
        rst = {
            "service": "association_chat.py",
            "nlg": ans
        }
    return rst
```

* * *

[1] https://github.com/Delgan/loguru。

[2] https://github.com/baidu/DuReader。

[3] https://github.com/tensorflow/serving。

## 7.4 自然语言生成

如上文所提到的，对于一个问答系统，根据用户问句获取数据库中的结果之后，需要对结果进行一定改写，让回复更加自然。在某些场景下，还可以加入对用户的理解，以及其他相关因素。比如，在询问天气时，如果气温比今天降低很多，还有暴雨预警，则可以根据用户的行程，主动加入一些关怀的句子，如“明天暴雨，最高气温11度，最低气温3度。记得多穿点衣服，出门要带伞哦”。

本节给出的示例是基于模板的自然语言生成，读者可以根据具体需求，添加更为丰富的策略，生成其他种类的回复。本节代码位于`ch7/nlg/nlg_handler.py`中。

```
import random
    
hello_chat_template = [
    '你好',
    'hi',
    'hello',
    '很高兴见到你'
]
    
qa_template = [
    '答案是{}',
    '正确答案是{}',
    '{}'
]
```

以上代码给出的分别是打招呼的回复模板以及问答的回复模板。

```
def nlg_main_handler(dst, nlu_dict):
    """
    dst为空表明可用服务无返回结果，进入兜底，否则根据不同的服务名称选择NLG模板
    :param dst: dst结果
    :param nlu_dict: NLU执行结果字典
    :return: 添加NLG的最终返回结果
    """
    if dst:
        service = dst["service"]
        if service == "hello_chat":
            nlg = random.choice(hello_chat_template)
        elif service in ["bot_qa", "common_qa", "medicine_qa", "user_qa"]:
            nlg = dst["nlg"]
            dst["nlg"] = random.choice(qa_template).format(nlg)
        rst = dst
    else:
        # 兜底安全回复
        rst = {
            "service": "no_service",
            "nlg": "这是兜底回复"
        }
    return rst
```

如果`dst`为空，则转为代码最后的安全回复。否则，根据不同的服务名称，如`hello_chat`对应的NLG内容是从打招呼回复模板中，随机选取一个模板进行回复。如果是`qa`，则在问答的回复模板中，选取随机模板进行回复。

## 7.5 服务化

本节给出服务化代码，基于`tornado`服务框架构建后端应用，可简便地接入如微信公众号等应用场景中，位置在`ch7/server.py`。

