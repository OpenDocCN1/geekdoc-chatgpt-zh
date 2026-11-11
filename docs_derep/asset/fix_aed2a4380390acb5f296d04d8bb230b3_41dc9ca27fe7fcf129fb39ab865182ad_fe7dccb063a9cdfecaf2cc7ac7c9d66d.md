对话关联打分机制拆解

这套打分思路的核心是给每一个备选主题算一个加权总分，总分由四类信号叠加而成：时效热度、用户历史偏好、角色设定倾向、对话近期关联。先看热度如何按时间衰减，再看四类记忆的读写方式，最后看整体流水线是如何把它们串起来的。

热度信号按与当前时刻的间隔分档，间隔越短权重越高，同一话题下会保留分数最高的事件细节返回。
                score = 0.6  
            elif time_interval < 3600*24*5:  
                score = 0.4  
            else:  
                score = 0.2  
            if score >= max_score:  
                max_score = score  
                event_detail = event_info[0]  
        return [event_detail, max_score]  
[PRE0]
为了让话题快速命中事件，系统维护一个话题到事件的映射表，一个话题可能对应多条带时间戳的事件记录。
topic_event_mapping = {  
    "女篮": [  
        ["女篮积极备战", 1565929940],  
        ["女篮大胜波多黎各", 1566163045]  
    ],  
    "波多黎各": [  
        ["女篮大胜波多黎各", 1566163045]  
    ]  
}  
[PRE1]
热度表不是静态的，刷新时先清空内存再从数据库拉取最新结果写入。
def update_hot_event():  
    topic_event_mapping.clear()  
    update_rst = synchronize_from_db("hot_event")  
    topic_event_mapping.update(update_rst)  
[PRE2]
用户历史偏好也放在内存字典里，更新时直接合并，查询命中则返回详情并贡献固定分值。
user_related_topic_dict = {}  
def update_user_related_entity():  
    update_rst = synchronize_from_db("user_related_entity")  
    user_related_topic_dict.update(update_rst)  
def filter_by_user_profile(user_id, topic):  
    related_topic_dict = user_related_topic_dict.get(user_id, None) 
    topic_detail = related_topic_dict.get(topic, None)  
    return [topic_detail, 1]  
[PRE3]
下面是一份典型的用户画像结构，包含基础属性和兴趣标签，用于后续匹配。
user_related_topic_dict = {  
    "user_id": {  
        "性别": "男",  
        "运动": "健身房运动两天",  
        "音乐": "七里香"  
    }  
}  
[PRE4]
机器人角色的偏好同样用字典维护，读写逻辑与用户侧保持一致，命中即计分。
bot_related_topic_dict = {}  
def update_bot_related_entity():  
    update_rst = synchronize_from_db('bot_related_entity')  
    bot_related_topic_dict.update(update_rst)  
def filter_by_bot_profile(bot_id, topic):  
    related_topic_dict = bot_related_topic_dict.get(bot_id, {})  
    topic_detail = related_topic_dict.get(topic, None)  
    return [topic_detail, 1]  
[PRE5]
对话近期关联需要定期维护，超过时间窗口的主题会被剔除，未超时的主题轮次计数自增。
def check_context(user_id):  
    now_time = time.time()  
    if user_id in user_context_topic_dict:  
        context_topic_dict = user_context_topic_dict[user_id]  
        for topic in context_topic_dict:  
            update_time = context_topic_dict[topic][0]  
            turn_past = context_topic_dict[topic][1]  
            sent_past = context_topic_dict[topic][2]  
            if now_time - update_time > time_window:  
                context_topic_dict.pop(topic)  
            else:  
                context_topic_dict[topic] = [update_time, turn_past+1, sent_past]  
[PRE6]
上下文打分采用轮次衰减，轮次越远分值越低，下限为零，同时带回当时的历史句子。
def filter_by_context(user_id, topic):  
    context_topic_dict = user_context_topic_dict.get(user_id, {})  
    data_tuple = context_topic_dict.get(topic, None)  
    if data_tuple:  
        turn_past = data_tuple[1]  
        sent = data_tuple[2]  
        score = 1 - (turn_past * 1.0 - 1) / 10  
        if score < 0:  
            score = 0  
        return [sent, score]  
[PRE7]
当新主题进入对话时，记录当前时间戳、把轮次归零并保存对应的句子。
def update_context(user_id, topic, sent):  
    now_time = time.time()  
    if user_id in user_context_topic_dict:  
        context_topic_dict = user_context_topic_dict[user_id]  
        context_topic_dict[topic] = [now_time, 0, sent]  
    else:  
        user_context_topic_dict[user_id] = {topic: [now_time, 0, sent]}  
[PRE8]
整体流水线先基于图谱一跳和词向量扩增备选集合，再对每个备选依次调用热度、用户、角色、上下文四路过滤器累加分数，最后输出分数映射和明细映射。
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
22.         # 排序因素: 与 bot 人设的关联程度  
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