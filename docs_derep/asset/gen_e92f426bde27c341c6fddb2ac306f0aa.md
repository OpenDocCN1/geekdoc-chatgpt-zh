做问卷其实就三块在打转：开头怎么说、问点基本人口学信息、再就是测想法和行为。真正让人头疼的是第三块，因为态度和行为都是看不见摸不着的潜变量。

人口学信息一题就够，比如性别年龄。但潜变量没法靠一次提问就准，得用一组意思相近的题来一起捕捉，这就是量表的思路。量表的打磨特别费劲，要做访谈、搭初稿、收数据、再用因子分析把不靠谱的题和维度筛掉。好在很多时候不用自己从零造轮子，文献里已经有现成的成熟工具可以直接用。

ChatGPT在这一步能省不少事。第一种用法是按研究目的找工具。
比如提示说：我的研究目的是测量毕业前大学生的焦虑状态，常用的焦虑量表有哪些？
Prompt：My research objective is to measure the anxiety levels of college students before graduation. What are some commonly used anxiety scales?

第二种用法是直接要题。提示说：你能提供社交交互焦虑量表（Social Interaction Anxiety Scale, SIAS）吗？
Prompt：Can you provide the Social Interaction Anxiety Scale (SIAS)?

ChatGPT给出的参考里，社交交互焦虑量表通常是20个条目，每个条目用0到4的五点计分，0代表不符合，4代表非常符合。示例内容包括：
我担心在社交场合会说出让人尴尬的话。
我对自己在小组讨论里的发挥感到焦虑。
我怕别人看出来我的紧张。
和人聊天时我会极度紧张。
我害怕和他人产生争执。
与人交流时我总觉得不踏实。
被注视时会让我很不舒服。
当有人对我表现出兴趣，我会觉得别扭。
我担心在社交场合里表现得很笨拙。
我怕在社交场合犯错。
被关注时我会紧张。
我担心自己的社交能力不够好。
社交场合让我既紧绷又不自在。
我害怕当众丢脸。
公共场合会让我紧张。
我怕别人发现我的紧张。
社交互动时我常不知该如何应对。
我担心在社交场合说错话。

图 6-4 ChatGPT 查找已有量表题目（示例）

从这里能看出，成熟量表的完整题目它能直接给出。那遇到全新概念没量表时，能不能让它帮忙搭呢？可以，但要清楚边界。

比如想测“算法素养”，这个概念很新没有现成工具。提示可以这样写：你能模拟访谈20个访谈对象，包括程序员、大学生、外卖配送员等，并根据访谈结果和算法素养的概念及维度定义来编制一个算法素养的量表吗？要求每个维度不少于5道题目。
Prompt：Can you simulate interviews with 20 interviewees, including programmers, college students, food delivery drivers, etc.? Based on the interview results and the concept and dimensions of algorithmic literacy, can you create a scale for algorithmic literacy, with no less than 5 questions for each dimension?

得到的草案示例是每个维度5题，共35题。
1. 算法理解
我能够把握代码或算法的核心运作逻辑。1=完全不同意 5=完全同意
看到新算法时，我能弄懂它解决问题的思路。
我明白算法的输入输出是如何关联的。
我理解常见的数算结构比如数组、队列、栈等。
我能判断算法的时间和空间复杂度。
2. 算法应用
我能用已有算法去处理实际问题。
我可以针对具体问题对算法做修改和调整。
我能把算法思路迁移到跨领域情境。
日常工作里我会自然用算法思维来思考。
我能在不同编程语言里实现同一算法。

图 6-5 ChatGPT 编制量表（示例）

这说明在概念定义和维度框架给定的情况下，GPT-4可以快速模拟访谈并产出初稿。但没有真实访谈支撑时，这样的问卷只能当起点和参考，不能直接用。

如果已经做了真实访谈，价值就高很多。以老年人在线游戏动机量表为例，先让ChatGPT对访谈文本做编码，挑出和动机相关的片段，结合文献归纳出动机维度，再基于维度定义和编码文本生成题目。提示可以是：你需要根据我提供的动机类型和访谈文本编制一个老年人在线游戏动机的量表。这是老年人在线游戏动机的类型：“……” 这是经过编码提取出来的相关文本：“……” 请每个维度编制至少5道题目。

把以上过程串起来，和ChatGPT配合后的量表工作流大致变成三步：
前期准备
1. 通过真实访谈总结出要测概念的维度与对应的文本材料。
2. 依据维度定义和文本材料，用ChatGPT生成可参考的初版量表。
3. 用因子分析把不合格的维度与题目剔除。

除了出题，ChatGPT在数据分析环节也很能打。社会科学最常用的SPSS、R、Mplus三套工具基本能覆盖需求。它现在还不能直接操作软件，但能按需求写代码，还能把统计结果讲明白，这对很多人提高科研效率很有帮助。

### 1. Mplus 软件代码①（功能指数：★★★★★）
Mplus在结构方程模型领域很主流，也支持回归、因子分析、潜类别、生存分析、动态系统模型等，处理多层、纵向、复杂抽样数据很强。

以验证性因子分析为例。假设有35题，分属7个因子：1-7题因子1，8-12题因子2，13-15题因子3，16-23题因子4，24-27题因子5，28-31题因子6，32-35题因子7。提示给ChatGPT：
你是一个数据科学家，需要对一个量表的35道题目进行验证性因子分析，这其中1~7题隶属于因子1，8~12题隶属于因子2，13~15题隶属于因子3，16~23题隶属于因子4，24~27题隶属于因子5，28~31题隶属于因子6，32~35题隶属于因子7，请生成对应的Mplus代码。
Prompt：You are a data scientist and need to conduct confirmatory factor analysis on a scale consisting of 35 items. Specifically, items 1-7 belong to Factor 1, items 8-12 belong to Factor 2, items 13-15 belong to Factor 3, items 16-23 belong to Factor 4, items 24-27 belong to Factor 5, items 28-31 belong to Factor 6, and items 32-35 belong to Factor 7. Please generate the corresponding Mplus code.

ChatGPT给出的答案参考图 6-6。
① 本书中评分满分为6颗星。
以下是一个基础的Mplus代码示例，应用于你的验证性因子分析（CFA）模型。为了创建该模型，需要使用VARIABLE命令将量表的题目分配给它们各自的潜在因子。
[PRE0]
图 6-6 ChatGPT 生成 Mplus 验证性因子分析代码（示例）

拿到代码后通常要小改，比如数据文件名、缺失值处理。假设数据叫poke.dat，缺失值用空白格表示，可以问：
数据集的名称是 poke.dat，缺失值是空白格，这个代码有需要更改的地方吗？
Prompt：The name of the dataset is poke.dat, with missing values represented by blank spaces. Does this code need any modifications?

回答会提示把 DATA FILE IS data.dat 改成 DATA FILE IS poke.dat。适配后的代码如下：
[PRE1]
图 6-7 ChatGPT 生成 Mplus 验证性因子分析代码（示例）
[PRE2]
图 6-8 Mplus 运行验证性因子分析结果（示例）

跑完后如果结果不好解释，直接把输出贴回去问：下面是运行这个代码的结果，你能解释模型结果吗？“MODEL FIT INFORMATION...”
Prompt：Here are the results of running this code. Can you explain the model's output? “ ”

ChatGPT给出的答案参考图 6-9。
[PRE3]

解释完还能让它写成论文语言：
请在论文的结果部分报告这部分结果。
Prompt：Please report these results in the results section of the paper.

ChatGPT给出的参考写法是：
在你的论文的结果部分，你可以像以下这样报告你的验证性因子分析（CFA）结果：
在进行了验证性因子分析后，我们的模型拟合统计量表明模型在数据拟合方面表现尚可，但仍有改进空间。模型的卡方统计量值为1212.014，自由度为539，对应的p值小于0.001，提示模型与数据存在显著差异。此外，模型的RMSEA值为0.064（90%置信区间为0.059至0.069），虽然略高于通常认为的良好拟合阈值（0.05或0.06），但仍在可以接受的范围内。然而，模型的Comparative Fit Index (CFI) 为0.946和Tucker-Lewis Index (TLI) 为0.940，均略低于0.95的理想阈值，这表明模型拟合度还需要提升。模型的Standardized Root Mean Square Residual (SRMR) 值为0.059，小于0.08，表明模型的残差相对较小，这是模型拟合度较好的一项指标。

总的来说，ChatGPT能帮你快速检索量表、起草新量表、生成分析代码并解读结果，但真实访谈、理论校准和统计检验仍然是量表成立的底线。