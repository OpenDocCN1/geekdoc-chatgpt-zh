[PRE0]

[PRE1]

已加载：'……山西大山中……的唐代木构建筑……一生必去的一个地方'已加载：'……私人豪宅，主人靠卖豆腐起家……比故宫的面积大2倍'已加载：'……实拍宋代的地下宫殿……内部有使用了2000年的冰箱'查询：找出观看次数超过100000的视频标题：'……实拍宋代的地下宫殿……内部有使用了2000年的冰箱'观看次数：263138发布日期：2024-01-27 00:00:00标题：'……私人豪宅，主人靠卖豆腐起家……比故宫的面积大2倍'观看次数：907279发布日期：2023-11-11 00:00:00查询：显示最新发布的视频标题：'……实拍宋代的地下宫殿……内部有使用了2000年的冰箱'观看次数：263138发布日期：2024-01-27 00:00:00

Self-query Retriever 借助元数据的配置，能够把日常口语式的检索需求映射成结构化查询条件，比如用户说“找出观看次数超过10万的视频”，系统就能自动转化为可执行的过滤规则。

内部产生的解析片段里出现的字段名 view_count、比较符 gt 以及数值 100000，全是模型从自然提问中自行抽取出来的。

[PRE2]

看实际运行效果，Self-query Retriever 既能精准挑出符合观看量门槛的视频，也能顺带筛选出最新上架的内容。但是稳定性不足，比如当问题变成“找出2024年1月发布的视频”时，咖哥在实测中就发现答案会出错。

一旦出现偏差，就必须钻到 LangChain 的源码里手工排查。这也是社区对 LangChain 的常见吐槽：它提供了好用的组件，却不能保证万无一失。所以这类框架工具更像思路参考，到了生产环境还得基于它的设计自己造一个更稳的版本。

### 5.2 查询翻译：让用户意图被更准确地理解

前面讲的几种构建查询的方式，主要是借助大模型生成不同于向量检索的结构化语句，去访问结构化数据源。而接下来要说的查询翻译，重点在于用提示工程对用户原始输入做语义层面的再加工。

实际使用中，原始提问往往口语化、带噪声、表述不完整，或者覆盖不到所有检索维度。这时就需要重写、拆分、澄清、扩展等手段，把问题打磨得更完整，从而提升命中率。

#### 5.2.1 查询重写：把松散提问整理成规范表达

查询重写就是把用户的原始说法改写成检索系统更容易理解的表述，进而提升召回和准确度。

##### 1. 用提示模板驱动模型改写

最简单的做法是自建提示模板，让大模型帮你把口语提问清洗成标准问法。下面给一个改写的例子。

[PRE3]

原始查询：那个，我刚开始玩这个游戏，感觉很难，在普陀山那一关，嗯，怎么也过不去。先学什么技能比较好？新手求指导！

重写查询：在普陀山关卡，新手应该优先学习哪些技能？

改写后的问句去掉了口水话，长度也变短了，检索时更容易命中玩家真正想要的攻略。

##### 2. 借助 RePhraseQueryRetriever 一键改写

LangChain 官方自带了一个现成的组件 RePhraseQueryRetriever，它内部已经配好提示，能直接把用户的杂乱提问整理成简洁的检索式。

下面的片段演示了这个组件的典型用法。

[PRE4]

.split_documents(data)

embed_model = HuggingFaceEmbeddings(model_name="BAAI/bge-small-zh")

vectorstore = Chroma.from_documents(documents=all_splits, embedding=embed_model)

###### 设置RePhraseQueryRetriever，用大模型重构查询

llm = ChatDeepSeek(model="deepseek-chat", temperature=0)

retriever_from_llm = RePhraseQueryRetriever.from_llm(

    retriever=vectorstore.as_retriever(),

    llm=llm

)

query = "那个，我刚开始玩这个游戏，感觉很难，在普陀山那一关，嗯，怎么也过不去。先学什么技能比较好？新手求指导！"

###### 调用RePhraseQueryRetriever进行查询重写

docs = retriever_from_llm.invoke(query)

INFO:langchain.retrievers.re_phraser:Rephrased question: Vectorstore query: "普陀山关卡 新手技能推荐 游戏攻略" Explanation: - Removed conversational fillers ("那个", "嗯") and emotional expressions ("感觉很难", "怎么也过不去", "新手求指导"). - Kept key game-specific terms: - Location: "普陀山" (关卡/level implied) - Core need: "技能推荐" (skill recommendations) - Context: "新手" (beginner) and "游戏攻略" (game guide) - Structured for optimal retrieval of strategy guides or skill trees relevant to the early-game hurdle.

从日志可以看到，改写后的检索词干干净净，去掉了“那个”“嗯”等口头填充，也剔除了“感觉很难”“怎么也过不去”等情绪化表达。保留的核心是关卡名“普陀山”、需求“技能推荐”、身份“新手”和场景“游戏攻略”，整体结构更利于命中攻略类文档。只是这里得到的新 Query 是英文，因为 RePhraseQueryRetriever 内部默认提示是英文写的。

如果想让输出保持中文，可以自定义提示词并传给 from_llm 方法。具体怎么改，咖哥就不展开了，这正是阅读 LangChain 文档 langchain\retrievers\re_phraser.py 并理解类设计的练习题。

#### 5.2.2 查询分解：把一个问题拆成多视角子问题

相比只做标准化，查询分解会把一个复杂意图拆成若干子问题，从不同角度分别检索，让结果集更全面。

[PRE5]

用提示词让模型做分解并不复杂，这里就不贴模板了。LangChain 提供的 MultiQueryRetriever 也能自动从多个视角生成一组变体查询。

每个变体都会去向量库里拉一次文档，最后把所有结果合并去重，就能得到比单次检索更丰富的集合。

[PRE6]

INFO:langchain.retrievers.multi_query:Generated queries : ['1. 这款游戏的难度级别是怎样的？有多少关卡？在普陀山那一关有什么窍门可以分享吗？', '2. 对于新手，应该优先学习哪些技能才能更好地应对游戏中的挑战？', '3. 我在玩这款游戏时感觉很困难，有没有一些建议或技巧可以帮助我顺利通过普陀山那一关？']

上述流程会自动产出不同角度的提问，并把它们的召回结果汇总，整体文档数量通常会显著提升。

需要更个性化的表达时，还可以自己改提示模板，比如要求生成 5 个不同风格的问法，甚至指定语气。