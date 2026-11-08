再来看编号②所处的应用区间。这类场景对文字产出有着很强的预设约束，只是任务对语言准确性、语法规范以及模式识别的依赖更高，因而对模型能力与算力投入的要求也更苛刻。常见的代表有语音识别、图像识别以及代码补全工具等。

前面提及的两大类，虽然表面上在“输出”文字，但产出都被严格的预设所框定，实质上并不具备创造性，因此不能归入 AIGC 范畴。

往横轴上方推移，编号③所覆盖的应用，人类对产出文本不再存在非黑即白的对错判断，却仍保留明显的预期指向，甚至可以用 0 到 10 的评分尺度来衡量其好坏。这正是机器学习训练时所依赖的人工标注数据。尽管这类任务往往需要相对复杂的学习模型来完成，但其边界十分清晰，只能在给定输入的范围内做既定处理，无法跳脱输入本身。某种程度上它们给人一种认知和表达能力较强的观感，所以在一些场合下会被误放进 AIGC 的讨论里，但本质上并不属于 AIGC。

到了编号④的区间，则出现了对话机器人与文本创作工具等形态，最典型的就是 OpenAI 在 2022 年 11 月 30 日推出的 ChatGPT。得益于训练语料的极大扩张和大模型强大的学习泛化能力，其生成结果呈现出多样且难以预料的特点。开发者还能依据使用场景调节生成策略，让文本样式更丰富多元，这正是典型的 AIGC 形态。

## 3.2 拆解文本生成技术原理

### 3.2.1 1950—1970 年，NLP 初露锋芒

自然语言处理诞生于 20 世纪 50 年代，是人工智能与语言学交叉下的分支，核心在于对自然语言的理解、认知与生成。其萌芽可以追溯到二战期间，受利益驱动，人们开始重视语言破译，并期望构建能够理解人类语言并“写”出内容的机器，而 1946 年第一台通用计算机的面世为这一设想提供了硬件可能。

1950 年至 1970 年是 NLP 的起步期。1950 年，艾伦·麦席森·图灵在《计算机器与智能》一文中提出图灵测试，用以衡量机器是否具备类人智力。1951 年，牛津大学计算机教授克里斯托弗·斯特雷奇完成历史上首个跳棋程序。到了 70 年代中期，亚瑟·塞缪尔研制的跳棋程序经“机械学习”后，棋艺已达业余爱好者水平。1954 年初，美国乔治城大学借助计算机将 40 多条俄文句子自动译为英文，开启了现代机器翻译的序幕。两年后的达特茅斯会议正式提出人工智能概念，该年被视作 AI 元年。

60 年代，研究者开始探究 AI 的语法与语义，语法树、句法分析与语言模型等关键方法相继出现。1964 年，约瑟夫·维森鲍姆打造出 Eliza 机器人，实现了人与计算机以文字对话，或可视作对话机器人的雏形，也是最早能应对图灵测试的程序之一。

不过发展并非一帆风顺。70 年代受底层技术限制，AI 进展缓慢，许多承诺落空，社会关注度下降。机器翻译频繁出错引发负面舆论，外部资金支持随之减少，行业进入低谷。

### 3.2.2 1980—2010 年，NLP 的寒冬与机遇并存

1980 至 2010 年是 NLP 承上启下的阶段。算力既是束缚也是触发新方法的契机。在这三十年里，研究者不断提升生成文本的流畅度与复杂度，并逐步把技术推向商业应用，为后续更逼真智能的文本生成奠基。

80 年代，随着算力提升，研究者开始尝试神经网络模型与语言模型进行大规模文本生成，实质仍依赖大量手工规则，计算机只是机械执行。这一局面延续到 80 年代末，机器学习算法的出现让 NLP 进入新思路。

90 年代，AI 在机器学习、自然语言理解与翻译、虚拟现实、游戏等领域取得重要进展。研究者打造出更逼真的语言生成系统，能够借助统计推理对未见词、异常结构或错误输入给出更稳健的建模。90 年代后期，深度学习技术开始被用于生成更流畅易读的文本；伴随网络与算力基础设施完善，文本生成技术逐步在商业场景落地。

进入 21 世纪初期，更复杂的高质量神经网络，如循环神经网络与长短期记忆网络，被用于文本生成。让 AI 生成内容与人类创作协同协作以提升流畅性也成为议题，例如 2005 年基于网络行为或媒体使用数据的推荐技术将 AI 引进营销。约在 2010 年前后，表示学习与神经网络风格的机器学习方法在 NLP 中广泛普及，问答准确率令人惊讶，基于深度神经网络的方法由此形成区别于传统统计 NLP 的新范式。

### 3.2.3 2010—2019 年，技术迸发与沉淀

自 2010 年起，算力持续增强，2014 年 GAN 模型的提出与演进让 AI 发展进入爆发节点。2016 年，谷歌 DeepMind 团队的阿尔法围棋成为首个击败职业围棋选手、首个战胜围棋世界冠军的 AI 机器人，如图 3-2 所示。2017 年，小冰公司发布全球首部完全由 AI 创作的诗集《阳光失了玻璃窗》，公众已很难界定 AI 与人类智慧的边界，也难以分辨创作主体。

### 3.2.4 2019 年至今，AIGC 进入寻常百姓家

2022 年 ChatGPT 的面世在业界引发震动，也让普通用户首次切身感受到 AI 带来的生活便利。随之 AIGC 浪潮全面兴起。谷歌 CEO 桑达尔·皮查伊宣布推出“ChatGPT 竞品” Bard，微软则宣布将 ChatGPT 接入搜索引擎 Bing。文本生成技术迎来历史性高点，但这只是开端。随着大语言模型愈发成熟，未来将在更多场景中惠及更多人。

在把握文本生成基本原理后，下面简要介绍其在传媒、教育、办公等领域的落地情况。

## 3.3 文本类 AIGC 在传媒场景中的应用

### 3.3.1 社交媒体文案：Jasper

当下社交媒体几乎无处不在，朋友圈、博客的更新常让人花费心思。AIGC 应用可以分担这部分工作，例如颇具知名度的 AI 写作工具 Jasper。它提供 50 多个写作模板，能够协助撰写博客或社交媒体文案。据其 CEO 称，2021 年该平台服务了超过 70 000 名客户，营收达 4500 万美元。

#### 1. 生成个人介绍
Jasper 作为头部文本类 AIGC 产品，订阅价格较高，约 59 美元/月。下面以实际体验说明其能力。其模板覆盖 Twitter、TikTok、Instagram 等主流平台。个人简介的吸引力至关重要，如图 3-3 所示，用户需在标识 1 处填入背景经历信息，可在标识 2 处选择语气风格，如风趣、礼貌或略显失望等，也可在标识 3 处指定第一人称或第三人称视角。设定完成后，右侧标识 4 处即呈现由系统生成的简介。

从结果看，Jasper 能把输入的标签转化为通顺的文字。有趣的是，输入材料并未使用任何形容词或身份描述，输出却出现了“这是一个多才多艺的年轻企业家”的赞美。当时选用“风趣”语气，输出文本使用了多个感叹句和活泼表达，符合预设风格。

#### 2. 生成社交媒体长文案
长文案是社交媒体的核心。仅输入“Snowboarding in the best ski resorts in Xinjiang”，Jasper 便生成了一段约四行的丰富描述，描绘在新疆滑雪之旅中令人震撼的景致与惊险的雪坡，如图 3-4 所示。除吸引人的描写外，系统还会加入提升点击率的小技巧，如提示查看更多度假村信息，并在结尾自动添加相关标签“SkiXinjiang”以扩大曝光。

#### 3. 生成社交媒体短文案
日常发微博或朋友圈往往需要精炼短句，Jasper 也支持此需求。同样输入“Snowboarding in the best ski resorts in Xinjiang”，这次系统输出了十条约一句话长度的文案，每条差异明显，例如“Time to take my snowboarding skills to the next level and shred those slopes in Xinjiang”、“Nothing beats carving down snow-covered mountains at the top ski resorts in Xinjiang”。这种调性颇具“凡尔赛”感，满足了用户在社交平台展示优越感的需求。

> ① 凡尔赛是网络流行语，指借委婉方式彰显自身优越。

### 3.3.2 新闻写作：Quakebot、CNET
新闻的核心是客观、详实地记录事实，而非创意与美感。AIGC 可以快速承接此类工作，在获取事件信息后生成多风格稿件，在“抢头条”的竞争中提升效率与竞争力。

Quakebot 由《洛杉矶时报》开发，用于即时地震报道。一旦美国地质勘探局 USGS 发布地震警报，机器人便从报告中抽取数据填入预设模板。2013 年 3 月，它首次报道加州南部 4.4 级地震而获得关注。2014 年 3 月 17 日洛杉矶发生 4.4 级地震，Quakebot 三分钟内完成撰写与发布。

CNET 是美国科技新闻主要媒体之一。2022 年底起开始使用 AIGC 撰稿，引发争议。初期文章直接发布，后发现存在低级错误。例如一篇复利文章写道“把 10 000 美元存一年，利率 3% 将获得 10 300 美元的额外收益”。显然正确收益应为 300 美元，系统却把本息总额误作收益。CNET 事后进行人工校对，后续对 AI 生成稿件均实施人工审核。

### 3.3.3 剧本撰写：海马轻帆
海马轻帆是国内专注于影视内容智能创作与评估的公司，2021 年年中推出小说改编剧本功能。2020 年后大量热播剧源自小说改编，市场需求旺盛。其生成的剧本创作的微短剧《契约夫妇离婚吧》在快手上线后，截至 2023 年 2 月 3 日播放量突破 1 亿。该公司还提供剧本智能评估服务，已为《流浪地球》《你好，李焕英》等多部头部作品提供质量与价值判断支持。

## 3.4 文本类 AIGC 在教育场景中的应用

### 3.4.1 文章撰写：EssayGenuis
写作与论文常是学生的难点。随着 AIGC 发展，撰写负担被极大减轻。EssayGenuis（EG）聚焦作文与论文，可改写段落、补全句式甚至生成整篇新文章。其擅长的领域涵盖经济学、心理学、人类学等文科，也包括数学、物理学和化

#### 1. 生成主题文章
以当前工具的写作能力为例。EG 首页极为简洁，仅有一个输入框用于指定主题。笔者以“Combination of AI and Web3”即 AI 与 Web3 结合为主题进行测试，如图 3-6 所示。

[PRE0]

一篇优质文章离不开清晰结构。EG 能依据主题自动生成框架。在该主题下，系统给出“I: Introduction”介绍、“II: Definition of AI and Web3”定义、“III: Benefits of Combining AI and Web3”好处、“IV: Challenges of Combining AI and Web3”挑战、“V: Potential Applications of Combining AI and Web3”潜在应用。如图 3-7 所示，结构简明，覆盖了主题的核心讨论点。

随后系统会据主题生成正文，如图 3-8 所示。经核查，实际文本与预生成结构并不完全对应，主要围绕 AI 与 Web3 的概念及结合优势展开，未涉及挑战与潜在应用。此外 EG 不支持像 ChatGPT 那样指定字数，篇幅有限且深度不足。理想的探讨应更深入分析二者结合带来的新特征，而 EG 的优势说明更多停留在各自带来的好处层面。

[PRE1]

图 3-7

#### Combination of AI and Web3
Artificial intelligence (AI) is a broad field of computer science that deals with the design and development of intelligent computer systems. Web 3.0 is the next generation of the World Wide Web, where users will have more control over their data and be able to interact with each other in new ways. The combination of these two technologies has the potential to revolutionize the way we use the internet.

AI can be used to improve the usability of the web by making it easier to find information and navigate websites. It can also be used to personalize the web experience for each user, providing them with tailored content and recommendations. Web 3.0 will provide a more decentralized and user-friendly web, where users will be able to control their own data and interact with each other directly.

The combination of AI and Web 3.0 has the potential to change the way we use the internet for the better. By making the web more user-friendly and personalized, we can make it more useful for everyone. In addition, the decentralization of the web will give users more control over their data and privacy, and allow them to interact with each other in new and innovative ways.

图 3-8