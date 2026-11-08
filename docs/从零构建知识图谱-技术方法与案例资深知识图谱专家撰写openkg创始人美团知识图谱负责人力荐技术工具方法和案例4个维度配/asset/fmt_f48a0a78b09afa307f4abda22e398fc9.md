
以上匹配的代码逻辑基本与前文一致，不同之处在于`score`的计算上。在计算`score`时，示例基于历史对话轮数`turn_past`，进行分支的降权，上一轮的话题对应的`turn_past`值为1，对应的`score`为1，上两轮的`turn_past`值为2，对应的`score`为0.9，即每增加一轮，对应的`score`降低0.1。关于降权的具体实现逻辑以及参数，读者可自行更改。

## 第三阶段，更新上下文

示例代码如下：

```
def update_context(user_id, topic, sent):  
    now_time = time.time()  
    if user_id in user_context_topic_dict:  
        context_topic_dict = user_context_topic_dict[user_id]  
        context_topic_dict[topic] = [now_time, 0, sent]  
    else:  
        user_context_topic_dict[user_id] = {topic: [now_time, 0, sent]}  
```

在本轮对话完成后（若作为服务接入，则还需考虑本服务是否最终被采用），将话题相关的上下文信息存储到`user_context_topic_dict`中。

## 5. 话题排序

在经过以上四个部分的话题筛选之后，原始的候选话题已经被剪掉了大部分，此时便可对候选集中的剩余候选话题进行排序，选择最优的结果进行回复。

候选话题排序的依据为之前四个筛选阶段的打分结果，具体如下所示，这里将之前的候选话题生成以及筛选流程整合到了一起：

```
1. def pipeline(sent, topic_list, user_id, bot_id='girl'):  
2.     # Step1: 产生候选话题集合，基于图谱的一跳关系，以及词向量扩展结果  
3.     candidate_set = get_association_candidates(topic_list, sent)  
4.     if not candidate_set:  
5.         return {}    
6.     # Step2: 计算候选实体得分  
7.     association_score_map = {}  
8.     association_detail_map = {}  
9.     for candidate in candidate_set:  
10.         detail_dict = {}  
11.         score = 0  
12.         # 排序因素: 根据热点过滤  
13.         hot_event_rst = filter_by_hot_event(candidate)  
14.         if hot_event_rst:  
15.             detail_dict["hot_event"] = hot_event_rst[0]  
16.             score += hot_event_rst[1]  
17.         # 排序因素: 与用户长期记忆的关联程度  
18.         user_rst = filter_by_user_profile(user_id, candidate)  
19.         if user_rst:  
20.             detail_dict["user"] = user_rst[0]  
21.             score += user_rst[1]  
22.         # 排序因素: 与bot人设的关联程度  
23.         bot_rst = filter_by_bot_profile(bot_id, candidate)  
24.         if bot_rst:  
25.             detail_dict["bot"] = bot_rst[0]  
26.             score += bot_rst[1]  
27.         # 排序因素: 与上下文的关联程度  
28.         context_rst = filter_by_context(user_id, candidate)  
29.         if context_rst:  
30.             score += context_rst[1]  
31.   
32.         association_score_map[candidate] = score  
33.         association_detail_map[candidate] = detail_dict  
34.     return association_score_map, association_detail_map  
```

其中，`get_association_candidates()`函数基于输入语句中抽取到的话题列表`topic_list`，产生候选话题集合`candidate_set`。

定义两个`dict`对象，`association_score_map`用于存储各个话题对应的分值，`association_detail_map`则用于存储各个话题联想的依据。

第9～33行代码表示遍历`candidate_set`中的各个候选话题，分别进行热点话题筛选、用户画像筛选、`bot`人设筛选和上下文筛选。注意，每一个`filter`筛选函数都会返回一个`score`值，并不断累加，最后存储到`association_score_map`中。如果一个话题在多个筛选阶段匹配到，那么`score`就会增大，其被最终选择可能性相应提升。同时，各个匹配阶段（除了上下文）的匹配依据被分字段存储于`association_detail_map`中。

根据`score`对候选话题排序，如以下代码所示：

```
max_score = -1  
rst_topic = None  
for topic, score in candidate_score_map.items():  
    if score > max_score:  
        max_score = score  
        rst_topic = topic  
    elif score == max_score:  
        # 如果分数相同，则以50%的概率替换当前候选，造成一定的随机性  
        if random.random() < 0.5:  
            rst_topic = topic  
if max_score == 0:  
    # 没有合适的加权候选，随机返回  
    rst_topic = random.choice(candidate_score_map.keys())  
```

代码逻辑为，选择`score`值最大的话题`topic`作为最终返回结果。其中添加了一个随机机制，当最大`score`值对应多个话题时，随机选择其中一个。

### 6.4.5 联想回复生成

话题联想的最后一个阶段是基于话题联想依据，产生一个人类能够理解的自然语言表述，作为本轮对话的回复，返回给用户。

这里采用模板的方式进行自然语言生成，如以下代码所示：

```
1. connect_template = [  
2.     '话说,',  
3.     '说起来,',  
4.     '想起来,'  
5. ]  
6. hot_template = [  
7.     '我最近看到消息说：',  
8.     '我最近看到一个话题：',  
9.     '最近大家好像在讨论：',  
10.     '我最近看到一条新闻：',  
11.     '我刚刚看到一条新闻说：',  
12.     '最近我得知一条新闻：',  
13.     '最近有一条新闻：',  
14.     '最近有一条消息说：',  
15.     '最近有一条话题：'  
16. ]  
17.   
18. def generate_rely(topic, detail_dict):  
19.     reply_list = []  
20.     if 'user' in detail_dict:  
21.         detail = detail_dict['user']  
22.         reply_list.append('没记错的话，{}是你的{}'.format(topic, detail))  
23.     if 'bot' in detail_dict:  
24.         detail = detail_dict['bot']  
25.         reply_list.append('真巧，{}也是我的{}'.format(topic, detail))  
26.     if 'hot_event' in detail_dict:  
27.         detail = detail_dict['hot_event']  
28.         reply_list.append(random.choice(connect_template) + random.choice(hot_template) + detail)  
29.     reply = "。".join(reply_list)  
30.     return reply  
```

其中，`generate_rely()`为定义的自然语言生成函数，它接收前序模块产生的联想话题，以及该话题的联想依据。

根据前文的匹配流程，我们知道匹配依据最多可以有三个，因此分别对热点话题、用户画像和`bot`人设三方面进行回复语句构造。

对于热点话题，回复语句模板由三部分构成，即`connect_template`、`hot_template`和具体的热点事件内容。`connect_template`和`hot_template`是两个`list`对象，分别从中随机选择一个结果进行拼接，便组成了自然语句。随机性的加入，使得回复语句更加多样。

对于用户画像和`bot`人设，则采用固定回复模板，直接将话题和对应的话题内容填充到模板对应的槽位中，完成自然语言生成。这里，读者也可以仿照热点话题的策略，构造更加丰富的回复模板，添加随机性，也可以根据不同的联想依据来选择个性化的回复语句。

最后，给出若干话题联想的完整示例，供读者参考。

案例1：

Q：谢霆锋主持的锋味最近要开播了吗？

A：锋味的主要嘉宾是周杰伦，没记错的话，周杰伦还是你最喜欢的明星，话说，我刚刚看到一条新闻说：周杰伦方文山加盟中国好诗歌。

抽取实体：锋味

联想实体：周杰伦

联想依据：

回答包含抽取实体与联想实体的联系：\[锋味–主要嘉宾–周杰伦\]

用户固定属性：\[最喜欢的明星：周杰伦\]

联想实体的热点信息：周杰伦方文山加盟中国好诗歌

案例2：

Q：饿了，好想来根士力架啊！

A：没记错的话，巧克力还是你的最喜欢吃的食物，我还知道士力架的类型是巧克力哦。

抽取实体：士力架

联想实体：巧克力

联想依据：

回答包含用户的固定属性：\[最喜欢的食物：巧克力\]

固定属性与对话提及实体的联系：\[士力架-类型-巧克力\]

案例3：

Q：复联4要上映了，我好想去看啊！

A：说到复联4，是复仇者联盟4：终局之战的中文名吧，说起来，我最近看到消息说：复联4中国首映礼。

抽取实体：复联4

联想实体：复仇者联盟4：终局之战

联想依据：

包含提及实体和联想实体之间的关系：\[复联4-中文名-复仇者联盟4：终局之战\]

联想实体的热点信息：复联4中国首映礼

* * *

[\[1\]](#part0036.html#noteBack_1) https://radimrehurek.com/gensim/。

## 参考文献

\[1\] X. Han and J. Zhao, NLPR\_KBP in TAC 2009 KBP Track: A Two Stage Method to Entity Linking\[C\]. TAC 2009 Workshop, 2009.

\[2\] V. Varma, P. Bysani, K. Reddy, et al. Iiit Hyderabad at TAC 2009\[C\]. TAC 2009 Workshop, 2009.

\[3\] W. Zhang, Y. C. Sim, J. Su, and C. L. Tan. Entity Linking with Effective Acronym Expansion, Instance Selection and Topic Modeling. IJCAI, 2011, pp. 1909-1914.

\[4\] H. Ji, R. Grishman, and H. T. Dang. Overview of the TAC 2011 Knowledge Base Population Track\[C\]. TAC 2011 Workshop, 2011.

\[5\] R. Herbrich, T. Graepel, and K. Obermayer. Large Margin Rank Boundaries for Ordinal Regression\[C\]. Advances in Large Margin Classifiers, A. Smola, P. Bartlett, B. Scho lkopf, and D. Schuurmans, Eds. Cambridge, MA: MIT Press, 2000, pp. 115-132.

\[6\] T. Joachims. Optimizing Search Engines Using Clickthrough Data\[C\]. SIGKDD, 2002, pp. 133-142.

\[7\] L. Shen and A. K. Joshi. Ranking and Reranking with Per- Ceptron\[C\]. Mach. Learn., vol. 60, no. 1-3, pp. 73-96, Sep. 2005.

\[8\] S. Cucerzan. Large-Scale Named Entity Disambiguation Based on Wikipedia Data\[C\]. EMNLP-CoNLL, 2007, pp. 708-716.

\[9\] D. Ceccarelli, C. Lucchese,S. Orlando, R. Perego, S. Trani.Dexter: an Open Source Framework for Entity Linking\[C\]. Sixth International Workshop on Exploiting Semantic Annotations in Information Retrieval (ESAIR), San Francisco, 2013.

\[10\] Sakor A, Singh K, Patel A, et al. FALCON 2.0: An Entity and Relation Linking Tool over Wikidata\[J\]. 2019.

\[11\] Green Jr B F, Wolf A K, Chomsky C, et al. Baseball: an Automatic Question-Answerer\[C\]. Papers Presented at the May 9-11, 1961, Western Joint IRE-AIEE-ACM Computer Conference. 1961: 219-224.

\[12\] Woods W A. Progress in Natural Language Understanding: an Application to Lunar Geology\[C\].Proceedings of the June 4-8, 1973, National Computer Conference and Exposition. 1973: 441-450.

\[13\] Daiber J, Jakob M, Hokamp C, et al. Improving Efficiency and Accuracy in Multilingual Entity Extraction\[C\]. Proceedings of the 9th International Conference on Semantic Systems. 2013: 121-124.

\[14\] Yosef M A, Hoffart J, Bordino I, et al. Aida: An Online Tool for Accurate Disambiguation of Named Entities in Text and Tables\[J\]. Proceedings of the VLDB Endowment, 2011, 4(12): 1450-1453.

\[15\] Lopez V, Uren V, Motta E, et al. AquaLog: An Ontology-Driven Question Answering System for Organizational Semantic Intranets\[J\]. Journal of Web Semantics, 2007, 5(2): 72-105.

\[16\] Lopez V, Fernández M, Motta E, et al. Poweraqua: Supporting Users in Querying and Exploring the Semantic Web\[J\]. Semantic web, 2012, 3(3): 249-265.
