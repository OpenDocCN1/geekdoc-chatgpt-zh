代码迁移后的二次校对与优化：得到初版转换结果之后，建议把这段代码再喂给 DeepSeek 做一次全面体检，请它揪出语法上的漏洞、逻辑上的不通顺，并给出可落地的改进方案。例如让模型重点排查 Java 段落里有没有变量未先赋值的风险，或者是否存在更省资源、更清晰的表达方式，以此来确保最终交付的代码是可靠且高效的。

目标语言特性的迁移学习：在从一种语言搬到另一种语言的过程中，难免会碰到只有目标语言才有的特有机制。这时可以借助 DeepSeek 来做快速补课。比如 Java 的注解机制在 Python 里并没有一模一样的对应物，DeepSeek 能够解释注解在 Java 中的典型使用场景和工作方式，并指导用户在转换后的代码里找到合理的等价实现思路。

##### 16.4.2 实操案例
下面就用“把代码从 A 语言搬到 B 语言”这件事来演示完整的操作路径。

第 1 步，把需要迁移的源代码完整贴给 DeepSeek。

第 2 步，打开 · 深度思考 · 与 · 联网搜索 ·，在输入框里给出清晰的转换指令，如图 16-4 所示。

[PRE0]

第 3 步，点击 ⬆，等待片刻，DeepSeek 就会返回重写后的代码以及相应的说明文档。因为内容较长，这里就不再完整展示了。

> AI 能让教育像 Netflix 一样个性化。

——萨尔曼·可汗（可汗学院创始人）

### 第 17 章 辅助讲解题目

#### 17.1 语文题目讲解
语文学习的核心在于阅读理解力、表达能力和文学素养的协同提升。做阅读题，要学会拆解文本、辨析词句、抓住主旨，并在语感与信息抽取上练习；写作文题则需要把平时的积累和技巧结合起来，从审题、立意到结构安排来锻炼书面表达；诗词鉴赏题则侧重意境、情感和艺术手法的把握，帮助学生更好地体会传统文化。

##### 17.1.1 详细讲解语文题目
DeepSeek 作为一款强通用模型，在语文答题上能给出多维度的支持，无论是诗词赏析、文本阅读还是写作指导，都可以成为学习者的有效助手，明显提高学习效率。

##### 17.1.2 实操案例
下面以“让 DeepSeek 深度解析语文题目”为例来说明。

第 1 步，确定解题目标，这里以解析李白《将进酒》的豪放气质与情感层次为目标。

第 2 步，开启 · 深度思考 · 与 · 联网搜索 ·，在对话框中输入相应的提示词，如图 17-1 所示。

[PRE1]

第 3 步，点击⬆，稍作等待，DeepSeek 就会生成一份完整的分析报告。

《将进酒》的豪放特质与情感表达可以从以下几个角度展开。

###### 一、喷薄而出的狂放气象
起句“黄河之水天上来”借用天体运行的磅礴之力来比喻生命的奔涌，随后“朝如青丝暮成雪”把时间压缩，呈现出对生命无常的焦虑与突破的渴望，二者形成的张力让全诗充满冲击感。

###### 二、悖论修辞中的生命哲学
“天生我材必有用，千金散尽还复来”表面是自信，实则构建有用与无用的辩证视角，通过对物质存在的不断超越来达到精神上的自由。

###### 三、醉酒叙事的解构艺术
“钟鼓馔玉不足贵”直接颠覆世俗价值排序，“主人何为言少钱”则以反讽化解宾主关系。诗人把宴席改造成生命狂欢的现场，用论辩式的语言瓦解常规规则。

###### 四、复调式情感结构（非单向豪迈）
- （1）表层狂欢：“会须一饮三百杯”的豪饮书写
- （2）深层悲郁：以酒神精神对抗“寂寞”，“古来圣贤皆寂寞”成为佐证

这首诗的价值不只在外在的豪迈，更在于它构建了一个带有现代质询意味的生存寓言：当知识分子的理想受阻，既不向世俗妥协，也不沉溺绝望，而是以诗性智慧搭建起流动的精神家园。这种狂欢式的反抗，成为中国文人传统中独特的文化原型。

#### 17.2 数学题目讲解
人工智能解决数学问题的方式多样：大模型可以直接对自然语言题干进行推理作答；专业计算软件如 Mathematica 可处理符号与数值运算；还有基于图像识别的解题 App，能拍照识别题目并给出步骤解析，满足不同阶段的学习需求。

##### 17.2.1 提供数学解题思路
使用 DeepSeek 解数学题的常规流程如下：

- 明确问题：用准确、完整的自然语言把题干输入模型。代数求解要写全方程，几何证明要描述图形条件和待证结论。
- 提出问题：在交互界面直接提问，例如“请解这个方程”“如何证明该几何结论”。
- 解读回答：模型会给出答案和步骤。用户需仔细阅读其推理逻辑和方法，内化解题思路。

##### 17.2.2 实操案例
这里以一道数学选择题为例演示。

圆 $x^2 + y^2 - 2x + 6y = 0$ 的圆心到直线 $x - y + 2 = 0$ 的距离为（ ）。

A. $2\sqrt{3}$ B. 2 C. $3\sqrt{2}$ D. $\sqrt{6}$

图 17-2 实操案例图

第 1 步，上传题目图片。

第 2 步，开启 · 深度思考 ·，在对话框中输入提示词，如图 17-3 所示。

[PRE2]

第 3 步，点击 ⬆，稍等，DeepSeek 会给出答案与解析，如图 17-4 所示。

[PRE3]

#### 17.3 英语题目讲解
英语学习涉及词汇、语法、听、说、读、写。词汇靠积累，语法提供表达框架；听力训练捕捉关键信息；阅读理解考查文本剖析能力；写作要求用词准确、逻辑清晰；口语则强调实际运用。

##### 17.3.1 解析英语题目
DeepSeek 在英语学习中能提供全方位解题支持，从词汇积累到语法理解，再到听说读写综合提升，都可以帮助学习者找到突破口，提高效率与成绩。

##### 17.3.2 实操案例
下面以一道英语阅读理解题为例。

Walking home from school, I am enjoying my day. Each day I stop to hear music coming from the ivy (常春藤). Sparrow (麻雀) song, a loud glorious sound. The ivy is pulsing below a leafless tree, decorating around the lower trunk like a celebration. Nervous flittings, in and out, pecking at the branches. This ivy is a home for them, harboring an entire flock. But this scene is no longer ubiquitous. Numbers have declined by almost 70% in the UK since 1970. Sparrows make their homes near human places and finding a tree like this is a blessing. Happily, the ivy is still full of chatter and twitter.

In Greek mythology, sparrows are sacred and associated with the goddess Aphrodite, symbolizing true love and spiritual connection. I wonder how many people look at sparrows and feel that depth of connection. All birds live brightly in our imagination, connecting us to the natural world, opening up all kinds of creativity.

As I stand there, fluffed-up sparrows in conversation among themselves, there is a spark. Noticing nature is the start of it all. Slow down to listen, to watch. Take the time, despite mountains of homework.

Take the time to observe nature, to immerse oneself in its patterns, structures, happenings and rhythms. It is how mathematicians and scientists are nurtured. Alan Turing studied the patterns in nature: the arrangement of petals on the flower, spots on a leopard and stripes on a zebra. He was looking for a mathematical formula (公式) for the development of cells in living things. Nature sparks creativity. All we have to do is start asking the question, Why? The way my mind whirls and whirls in nature, or even when "daydreaming" is way more productive than the work I do in school.

I consolidate myself by thinking and thinking while intensely watching the movements of sparrows. Who knows where watching sparrows will lead?

1. What do I do on the way home from school?

A. Clean up the trunk.

B. Decorate the tree.

C. Listen to pop music.

D. Observe the sparrows.

图 17-5 英语阅读理解

第 1 步，上传题目图片。

第 2 步，开启 · 深度思考 ·，在对话框中输入提示词，如图 17-6 所示。

[PRE4]

第 3 步，点击 ⬆，稍等，DeepSeek 会形成完整的阅读解析。

英语阅读题目答案及解析：

**D. Observe the sparrows.** （观察麻雀）

理由：

首段明确写到放学路上作者会停下来聆听麻雀的歌声，并细致描写麻雀在常春藤中的穿梭啄食行为，可见核心行为是观察麻雀，而非清理树干、装饰树木或听流行音乐。

总结：

本题重在段落主旨归纳，需结合文中具体细节与逻辑关联作答。

#### 17.4 物理题目讲解
物理题聚焦运动规律与公式运用。力学题需分析受力、套用牛顿定律；电学题结合欧姆定律理解电流电压关系。学习时要吃透实验原理，学会建模，把物理知识迁移到实际问题。

##### 17.4.1 讲解物理原理，展示物理知识
DeepSeek 能从概念到计算多维度辅助物理学习。物理概念抽象、推导繁琐，借助模型化解析能更快找到突破口，无论基础公式还是综合题都有帮助。

使用时要注意：不能照搬答案，要理解原理与推导；题目条件必须描述准确；它只是辅助，不能替代实验和自学。

##### 17.4.2 实操案例
下面以一道物理题为例，演示如何获取解题思路。

[PRE5]

第 1 步，上传题目图片。

第 2 步，开启 · 深度思考 ，在对话框中输入提示词，如图 17-8 所示。

[PRE6]

图 17-8 实操案例图

第 3 步，点击 ，稍等，DeepSeek 会给出答案，如图 17-9 所示。

1. 明确初速度$v_0 = 10\text{m/s}$，末速度$v = 0\text{m/s}$，时间$t = 2\text{s}$。

2. 用平均速度公式$\bar{v} = \frac{v_0 + v}{2}$，得$\bar{v} = \frac{10 + 0}{2} = 5\text{m/s}$。

3. 由$x = \bar{v}t$，算出$x = 5 \times 2 = 10\text{m}$，选 B。

图 17-9 实操案例图

#### 17.5 化学题目讲解
化学学习围绕元素性质、反应规律和实验操作。元素化合物题考查记忆与应用，反应题考查原理与配平；实验题则培养观察、动手和科学思维。

##### 17.5.1 分析化学题目，提供化学解题方法
DeepSeek 可帮助梳理知识脉络，快速定位解题关键点，无论是概念辨析还是实验设计与计算都能提供有效支持，提升效率。

##### 17.5.2 实操案例
这里以一道选择题为例。

下列说法不正确的是（ ）。

A. 葡萄糖氧化生成$CO_2$和$H_2O$的反应是放热反应

B. 核酸可看作磷酸、戊糖和碱基通过一定方式结合而成的生物大分子

C. 由氨基乙酸形成的二肽中存在两个氨基和两个羧基

D. 向饱和的NaCl溶液中加入少量鸡蛋清溶液会发生盐析现象

图 17-10 实操案例图

第 1 步，上传题目图片。

第 2 步，开启 · 深度思考 ，在对话框中输入提示词，如图 17-11 所示。

[PRE7]

图 17-11 实操案例图

第 3 步，点击⬆，稍等，DeepSeek 会给出答案。

本题涉及盐析、核酸等化学常识。

A. 葡萄糖氧化生成 CO₂ 和 H₂O 是放热反应，正确。

B. 核酸由磷酸、五碳糖和含氮碱基经磷酸二酯键连接而成，正确。

C. 两个氨基乙酸脱水缩合形成二肽后仅剩一个游离氨基和一个游离羧基，说存在两个氨基和两个羧基是错误的。

D. 饱和 NaCl 溶液中高浓度离子使蛋白质溶解度下降而沉淀，属于盐析，正确。

故选 C。

#### 17.6 生物题目讲解
生物题涵盖生命现象与规律。细胞题帮助建立微观认知，遗传题训练逻辑推理，生态题考查系统结构与功能。实验学习有助于掌握探究方法。

##### 17.6.1 解读生物知识，解答生物疑问
DeepSeek 能助力梳理从细胞到生态系统的庞大知识网，在解题时提供精准的概念支持，无论背诵记忆还是复杂实验分析都能提升成效。

##### 17.6.2 实操案例
这里以一道生物题为例展示解题思路。

[PRE8]

第 1 步，将生物题目上传到 DeepSeek。