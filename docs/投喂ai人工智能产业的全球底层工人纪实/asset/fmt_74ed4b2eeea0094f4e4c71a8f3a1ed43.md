
大语言模型的表现也并非总是可预测的，有时也会出现人类无法解释的情况。ChatGPT刚发布时，人们就发现一些“故障词元”：每每输入这些词，模型就会发生故障，开始胡言乱语。比如：问ChatGPT“谁是TheNitromeFan”，它会莫名其妙地回答“182”；让它重复“StreamerBot”这个词，它会开始说脏话。没人知道这些词元为什么会让模型发生故障，但较为可信的说法是Reddit的一个子版块（计数）500万的用户数据集（近10年的统计数据）太大，其中用户名存在重复（这些词元就是重复的用户名），导致模型无法确定准确信息。这些异常问题说明，大语言模型就像黑盒，人类有时也无法理解它们的怪异行为。微软的必应聊天机器人就曾劝说一位《纽约时报》的记者离婚，微软首席技术官凯文·斯科特（Kevin Scott）对这种情况也感到困惑，他表示：“人们和智能聊天机器人说的内容越离奇，它的回答就越偏离现实。”

被问到有唯一正确答案的问题时，大语言模型的表现往往不尽如人意，但面对一些提炼总结的任务，它的表现可圈可点。如果颠倒陈述逻辑，大语言模型也会出现故障。在一个叫作“逆转诅咒”的实验中，研究人员发现，询问ChatGPT-4“汤姆·克鲁斯（Tom Cruise）的妈妈是谁”，在79%的情况下，它能给出正确答案“玛丽·李·菲弗尔（Mary Lee Pfeiffer）”；但如果反过来问“玛丽·李·菲弗尔的儿子是谁”，它的正确率仅有33%。

人类想要正确理解大语言模型，客观地评估其优缺点，就不能把它当成人来看。

1.  [\[1\]](#Chapter4_1#ref1) Michie, Donald. ‘Experiments on the Mechanization of Game-Learning Part I. Characterization of the Model and Its Parameters’. The Computer Journal, Vol. 6 No. 3 (1963), pp. 232-6, https://doi.org/10.1093/comjnl/6.3.232.
2.  [\[2\]](#Chapter4_1#ref2) Narayanan, Deepak, Shoeybi, Mohammed, Casper, Jared, LeGresley, Patrick, Patwary, Mostofa, Korthikanti, Vijay, Vainbrand, Dmitri, Kashinkunti, Prethvi, Bernauer, Julie, Catanzaro, Brian, Phanishayee, Amar, Zaharia, Matei. ‘Efficient Large-Scale Language Model Training on GPU Clusters Using Megatron-LM’. (2021) SC21, https://arxiv.org/abs/2104.04473.
3.  [\[3\]](#Chapter4_1#ref3) 这篇著名的论文引用了一种自然语言处理的新方法，为新一代大语言模型奠定了基础。See Vaswani, Ashish, Shazeer, Noam, Parmar, Niki, Uszkoreit, Jakob, Jones, Llion, Gomez, Aidan N., Kaiser, Lukasz, Polosukhin, Illia.‘Attention Is All You Need’. https://arxiv.org/abs/1706.03762.
4.  [\[4\]](#Chapter4_1#ref4) Bender, Emily M., Gebru, Timnit, McMillan-Major, Angelina, Mitchell, Margaret. ‘On the Dangers of Stochastic Parrots: Can Language Models Be Too Big?’.Proceedings of the 2021 ACM Conference on Fairness,Accountability, and Transparency. FAccT’21.Association for Computing Machinery. pp. 610-23, https://dl.acm.org/doi/10.1145/3442188.3445922.
5.  [\[5\]](#Chapter4_1#ref5) Common Crawl. https://commoncrawl.org/.
6.  [\[6\]](#Chapter4_1#ref6) OpenAI, ‘GPT-4 Technical Report’. 2023. https://cdn.openai.com/papers/gpt-4.pdf.
7.  [\[7\]](#Chapter4_1#ref7) Roose, Kevin. ‘A Conversation With Bing's Chatbot Left Me Deeply Unsettled’. New York Times, 16 February 2023, https://www.nytimes.com/2023/02/16/technology/bing-chatbot-microsoft-chatgpt.html.
8.  [\[8\]](#Chapter4_1#ref8) Berglund, Lukas, Tong, Meg, Kaufmann, Max, Balesni, Mikita, Stickland, Asa Cooper, Korbak, Tomasz, Evans, Owain. ‘The Reversal Curse: LLMs trained on“A is B”fail to learn “B is A”’, https://arxiv.org/abs/2309.12288.
9.  [\[9\]](#Chapter4_1#ref9) Bender,Emily. ‘Resisting Dehumanization in the Age of“AI”’, Cognitive Dviersity 2022.29 July 2022,Toronto,Canada.At:https://faculty.washington.edu/ebender/papers/Bender-CogSci-2022.pdf.
10.  [\[10\]](#Chapter4_1#ref10) Dennett, Daniel C. The Intentional Stance, Cambridge, MA:The MIT Press,1987.

## 机器能否取代你的工作

有预测称，AI将导致大量工作岗位消失，投行高盛预计可能有多达3亿个工作岗位被自动化或AI取代，麦肯锡咨询公司预测的数量是4亿～8亿。

21世纪第2个10年也曾发生过一次技术失业恐慌。2013年，牛津大学的两位研究人员调查了不同岗位被计算机取代的可能性，发现在未来10～20年，美国有47%的岗位将面临被计算机取代的“高风险”。

国际劳工组织2023年的一份报告显示，像ChatGPT这样的生成式AI在工作场景中的主要作用是辅助而不是替代。

通过对大语言模型的分析，我们发现它更擅长处理有明确目标和有较多文本数据支撑的标准化任务。据此，我们能判断出它对哪些行业的冲击最大。虽然机器不可能完全取代人类工作，但有一些专门为特定行业设计的生成式AI还是会彻底改变这些行业。比如，跟代码打交道的软件开发员和计算机程序员将受到极大影响。曾经一个程序员团队才能完成的工作，现在一个人就能完成。像ChatGPT这样的聊天机器人还擅长广告、营销和新闻传播领域的文本创作。它们能大幅提升工作效率，原本需要几个小时才能完成的任务，现在只需几分钟。此外，一些涉及大量文本数据整合和分析的工作，如法律和金融行业的部分工作、市场分析和交易方面的工作，也可以使用AI来提高工作效率。但我们还是要提高警惕，不要过度依赖ChatGPT。

使用大语言模型的最大风险，就是夸大其功能和生产力，导致人们未经深思熟虑，就将它们应用于众多行业。这样一来，决策权就从可以充当责任主体的人类移交给了不受监控、无法追责的机器。2023年，AI初创企业吸引了全球近500亿美元的投资。

像李这样的机器学习工程师致力于在实验室中改进模型。然而，AI的发展并非完全由技术人员掌控。它受到市场竞争压力的影响，各家公司都在争抢这一新兴领域的头把交椅。更重要的是，AI技术还受到各种政治、经济和文化因素的影响，它对特定领域或职业的影响取决于技术的应用趋势，而这种趋势很难预测。所以，如果过度依赖当前大语言模型的能力，给予机器过多的权力和责任，而没有足够的人力监督，这过于鲁莽。模型就像私立学校培养出的政治精英阶层，他们的话听起来似乎很有道理，细想下来却充满了该阶层对社会的偏见，体现了社会权力分配不均的事实。

1.  [\[1\]](#Chapter4_2#ref1) Goldman Sachs. ‘Generative AI could raise global GDP by 7%’, Goldman Sachs, 5 April 2023, https://www.goldmansachs.com/intelligence/pages/generative-ai-could-raise-global-gdp-by-7-percent.html; McKinsey Global Institute, ‘Jobs lost, jobs gained: What the future of work will mean for jobs, skills, and wages’. McKinsey, 28 November 2017, https://www.mckinsey.com/featured-insights/future-of-work/jobs-lost-jobs-gained-what-the-future-of-work-will-mean-for-jobs-skills-and-wages.
2.  [\[2\]](#Chapter4_2#ref2) Frey, Carl Benedikt and Osborne, Michael A. ‘The Future of Employment: How Susceptible are Jobs to Computerisation?’. At: https://www.oxfordmartin.ox.ac.uk/downloads/academic/The_Future_of_Employment.pdf.
3.  [\[3\]](#Chapter4_2#ref3) Gmyrek, Pawel, Berg, Janine, Bescond, David. ‘Generative AI and jobs: A global analysis of potential effects on job quantity and quality’. ILO Working Paper 96. Geneva: International Labour Office, 2023. At: https://www.ilo.org/global/about-the-ilo/newsroom/news/WCMS_890740/lang--en/index.htm.
4.  [\[4\]](#Chapter4_2#ref4) Meting, Chris. ‘Artificial Buildup: AI Startups Were Hot In 2023, But This Year May Be Slightly Different’. CrunchBase News, 9 January 2024, https://news.crunchbase.com/ai/hot-startups-2023-openai-anthropic-forecast-2024/.
5.  [\[5\]](#Chapter4_2#ref5) Eubanks, Virginia. Automating Inequality: How High-Tech Tools Profile, Police, and Punish the Poor. New York:St Martin's Press, 2018; O’Neil, Cathy. Weapons of Math Destruction: How Big Data Increases Inequality and Threatens Democracy. New York: Crown Publishing Group, 2016;Drage, Eleanor and Mackereth, Kerry. ‘Does AI Debias Recruitment? Race, Gender, and AI's“Eradication of Difference”’. Philosophy ＆Technology 35,89(2022).

## 算法审判日

AI技术到底哪里危险？要讨论这个问题，可以先观察和分析一下关于AI负面影响的辩论。这场辩论中有两个观点对立的阵营。一个阵营强调AI带来的存在风险，另一个阵营关注AI的持续危害。也就是说，到底要关注大语言模型和其他AI的哪些负面影响，一直也没有定论。一直关注AI发展成果的人指出，AGI（通用AI）可能会带来危害，它在某些领域已经超越人类，并且能自己做决策。

另一个阵营的人则更加关注AI当下对人类的直接影响，尤其是当前各种算法决策中广泛存在的偏见和歧视。

我们应该如何看待这两个阵营看似无法调和的争论呢？关于AI可能带来的生存风险的讨论已持续了数十年，但主要局限于一个由专家和哲学家构成的AI安全小圈子。这些争论还被冠以一个温和的名头“AI对齐”，意思是专注于确保未来AI系统具有正确的价值观，确保AI发展目标符合人类设定的轨迹。2023年，“AI威胁论”成了社会主流讨论话题。“AI教父”杰弗里·辛顿（Geoffrey Hinton）、约书亚·本吉奥（Yoshua Bengio）和DeepMind首席执行官戴米斯·哈萨比斯（Demis Hassabis）等众多科技界名人签署了一份声明，警告人类：“减轻AI带来的灭绝风险应该与流行病和核战争等其他社会性危机一并成为全球优先事项。”

我们该如何看待这些担忧呢？有人认为，有关AI毁灭世界的警告其实就是在炒作AI产品。可要说完全不信，那也是假话。因为如果完全不信未来的聊天机器人能够在我们修理它的电路时监视我们，我们也就没必要对这个现在还漏洞百出的东西如此上心。尽管有炒作嫌疑，但AI领域还是有人相信其会对人类未来生存构成威胁。2022年的一项针对AI研究人员的调查显示，一半人认为AI有10%以上的概率会毁灭人类。

但我们也要清醒一点，从目前形势来看，在能够预见的未来，人类不会面临此类威胁。现在的AI连仓鼠水平的自主意识都没有，更别提什么超级AI了。大语言模型不会在早上醒来时思考要不要忠于人类。它只不过是标准的语言模型，根据人类输入的提示语检索数据集，然后输出结果。它确实有潜在的风险，如协助黑客攻击、编造虚假信息和传播错误信息，但现实中我们离电影《终结者》和《机械姬》里机器追杀人类的桥段还很远。媒体上针对这些风险的讨论既为时过早，也脱离了当前AI的现实。然而，这并不意味着人类不该认真思考AI发展所带来的长期后果，也不意味着AI不会发展到电影中的程度。我们要做的是从当前这代AI的现实情况出发，分析它们正在造成或可能造成的危害，并预判随其技术演进可能滋生的风险。

那么，我们现在正面临怎样的风险呢？一些不法之徒可能会利用大语言模型危害社会，比如，传播虚假信息、操纵选举和制造生物武器。首先，生成式AI可以让用户快速制造虚假信息，并在社交媒体上进行传播。以往，这类误导性内容的传播往往依赖于人工操控的机器人，仅搭载初级文本生成工具，但AI不仅极大地扩展了受众范围，还提升了信息的个性化程度和说服力。

大语言模型还让人们更容易接触到制作生物武器和其他可用于恐怖袭击的化学品的信息。虽然商用的大语言模型都设有安全防护，阻止聊天机器人向用户传播危险信息，但事实证明这些措施都可被绕过。开源模型的进一步传播将使更多人有能力创造危害性物质。大语言模型具备专家级别的专业知识，理论上甚至能够指导用户制造新型病毒。Inflection AI公司的联合创始人兼首席执行官穆斯塔法·苏莱曼（Mustafa Suleyman）对未来AI辅助病毒暴发表示担忧：“可能发生的最糟糕情况就是人们会用AI改造病原体，这些人工合成的病原体会有意无意间提高病毒的传播率和致死率。”

除了以上风险，生成式AI还会放大偏见，强化刻板印象。这些负面影响在金融、住房、社会福利、司法等关键领域尤为显著，直接影响人们的生活质量和基本权利。为了进一步了解这些问题，我们还要深挖塑造AI发展的隐形价值观。

1.  [\[1\]](#Chapter4_3#ref1) 该观点最早且最有影响力的阐述之一，see Nick Bostrom's Superintelligence:Paths,Dangers,Strategies.Oxford:Oxford University Press,2014.
2.  [\[2\]](#Chapter4_3#ref2) See Benjamin,Ruha.Race After Technology:Abolitionist Tools for the New Jim Code. New York:John Wiley and Sons, 2019; Buolamwini, Joy. Unmasking AI:My Mission to Protect What Is Human in a World of Machines.New York:Random House, 2023; Buolamwini, Joy and Gebru, Timnit. ‘Gender shades:Intersectional accuracy disparities in commercial gender classification’. Conference on fairness,accountability and transparency,pp.77-91.
3.  [\[3\]](#Chapter4_3#ref3) Statement on AI Risk. https://www.safe.ai/statement-on-ai-risk.
