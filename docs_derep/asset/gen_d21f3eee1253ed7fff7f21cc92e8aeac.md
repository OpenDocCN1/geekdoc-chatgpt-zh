文献检索不踩坑：用 ChatGPT 搭出精准搜索公式

[TIBA] OR "adulthood" [TIBA] OR "middle aged" [MeSH] OR "middle aged" [TIBA] OR "TIBA" OR "middle age" [TIBA] OR "mid-dle-age" [TIBA] OR "older adult" [TIBA] OR "older adults" [TIBA] OR "elderly" [TIBA] OR "seniors" [TIBA]) AND ("adverse childhood experiences" [MeSH] OR "adverse childhood experience" [TIBA] OR "adverse childhood experiences" [TIBA] OR "ACEs" [TIBA] OR "childhood adversity" [TIBA] OR "childhood adversities" [TIBA] OR "childhood trauma" [TIBA] OR "childhood traumas" [TIBA] OR "child maltreatment" [MeSH] OR "child maltreatment" [TIBA] OR "child abuse" [MeSH] OR "child abuse" [TIBA] OR "child neglect" [MeSH] OR "child neglect" [TIBA]) AND English[lang] AND "humans" [MeSH]

先用 ChatGPT 推荐的检索式在 PubMed 跑了一遍，结果出来 78 篇文献。思路相通，下面换到 Web of Science，看看怎么提炼一个可复用的检索构建指令。

提示词

作为长期做文献综述的资深馆员，擅长从 PubMed、Web of Science 等数据库里精准抓取信息。请为我设计一个全面、准确且贴合研究问题的检索策略，用于在 Web of Science 中系统定位相关论文。

要让检索式真正映射研究问题，请按下面思路来：

- 把研究问题拆成两个及以上核心概念
- 针对每个概念列出近义关键词和受控词表
- 同一概念内部的词用布尔 OR 合并
- 不同概念之间再用布尔 AND 连接

举例说明，问题是“体育活动干预中人工智能的应用”。先锁定三个概念：人工智能、体育活动、干预。人工智能可扩展为“机器学习”“神经网络”“强化学习”；体育活动可扩展为“运动活动”“久坐行为”“行走”；干预可扩展为“试验”“治疗”“计划”。先用 OR 把每个概念内部的词串起来，再用 AND 把概念之间连起来。

示例

研究问题：体育活动干预中人工智能的应用

关键概念：人工智能；体育活动；干预

搜索算法：

[PRE0]

下面给出一段需要落地的检索需求。

## 第五章 文献检索与筛选操作实务

研究问题：参与体育活动的频率与强度，对经历过不良童年经历的成年人重度抑郁障碍的出现与严重程度会产生怎样的影响？

核心概念：体育活动；重度抑郁障碍即重度抑郁症；成年群体；不良童年经历 ACEs

搜索算法：

[PRE1]

实际操作中，检索式定稿前通常要根据命中文献反复修正和回测。为了简化叙述，这里假设检索已经完成，进入下一环节——按照预设纳入与排除标准对标题摘要进行初筛。

## 第五章 文献检索与筛选操作实务

以 Cochrane 综述的做法为例，标题摘要的筛选一般由至少两名评审者独立判断，分歧通过讨论达成一致。

这里可以探讨一个延伸问题：能否让 ChatGPT 来做标题摘要的初筛并给出理由。我们就看看它在这一步能怎么帮忙。

◎ 提示词

我正在做系统综述，已从数据库中拿到一批同行评议论文。请依据我的研究问题和纳入排除标准，协助判断每篇论文是否应纳入。需明确给出纳入或排除的结论，并用一句话说明理由。下面附两个示范。

研究问题：对经历 ACEs 的成年人而言，体育活动参与的频率与强度如何影响重度抑郁症的发生与严重程度？

纳入标准，研究需同时满足：① 设计为实验类如随机对照试验、前后测干预或交叉试验，或观察类如横断面、前瞻性、回顾性队列；② 对象为 18 岁及以上且有 ACEs 暴露的成年人；③ 暴露因素为体育活动或锻炼；④ 结局为重度抑郁障碍或重度抑郁症；⑤ 文献类型为原创实证、经同行评议期刊论文；⑥ 语言为英文。

排除标准，满足其一即剔除：① 结局并非重度抑郁症；② 研究对象为 18 岁以下儿童青少年；③ 非英文；④ 文献类型为书信、社论、方案、个案报告、综述或定性研究如访谈、焦点小组。

示例1

Title: The associations between adverse childhood experiences, physical and mental health, and physical activity : a scoping review( Hadwen et al.,2022)

Abstract 概要：背景认为 ACEs 与成年后身心健康受损、低活跃度相关，但关联尚不清晰。目的厘清 ACEs、身体活动与身心健康的文献图景。方法按 PRISMA-ScR 执行范围综述，检索 MEDLINE、Scopus、SPORTDiscus、PsycInfo。结果纳入 18 篇，17 篇观察性、1 篇随机对照。多数为横断面、自报测量。结论提示低活跃度人群中 ACEs 与不良身心健康的关联更明显，但证据多为横断面自报，尚需更严谨研究确认身体活动能否预防或改善 ACEs 背景下的健康问题。

决定和说明：排除。该文为范围综述，不属于原创实证研究。

示例2

Title: Physical activity mitigates the link between adverse childhoodexperiences and depression among U. S. adults (Royer & Wharton, 2022)

Abstract 概要：背景介绍 ACEs 涵盖忽视、虐待、家庭功能问题等，过去研究显示 ACEs 增加抑郁风险，而身体活动可缓解抑郁，但两者在美国成年人中的中介关系缺乏明确证据。方法利用 2020 年行为风险因素监测系统数据，样本 117,204 名 18 岁及以上美国成年人，覆盖 21 个州及哥伦比亚特区，采用卡方检验和多变量逻辑回归分析 ACEs 与抑郁、ACEs 与身体活动、身体活动与抑郁的关联，并检验身体活动的调节作用。结果显示 ACEs 与低身体活动和高抑郁均相关，回归中 ACEs 与抑郁正相关，ACEs 与身体活动、身体活动与抑郁呈负相关，身体活动对 ACEs 与抑郁的关联具有缓解作用。