
## Twitter API 使用示例

```
In [39]: from twitter import Twitter, OAuth

In [40]: t = Twitter(auth=OAuth(c['twitter']['access_token'],
                                c['twitter']['access_secret_token'],
                                c['twitter']['api_key'],
                                c['twitter']['api_secret_key']),
                     retry=True)  ➊

In [41]: l = t.statuses.home_timeline(count=5)  ➋

In [42]: for e in l:
             print(e['text'])  ➋
         The Bank of England is effectively subsidizing polluting industries in its
          pandemic rescue program, a think tank sa...
         Cool shared task: mining scientific contributions (by @SeeTedTalk @SoerenAuer
          and Jennifer D'Souza)
         Twelve people were hospitalized in Wyoming on Monday after a hot air balloon
          crash, officials said.

         Three hot air...
         Company announcement: Revolut launches Open Banking for its 400,000
          Italian...
          #fintech

In [43]: l = t.statuses.user_timeline(screen_name='dyjh', count=5)  ➌

In [44]: for e in l:
             print(e['text'])  ➌
         #Python for #AlgoTrading (focus on the process) &amp; #AI in #Finance (focus
          on prediction methods) will complement eac...
         Currently putting finishing touches on #AI in #Finance (@OReillyMedia). Book
          going into production shortly.
         Chinatown Is Coming Back, One Noodle at a Time
         Alt data industry balloons as hedge funds strive for edge via @FT |
         "We remain of the view that alternative d...
         He just follow me on Twitter (or LinkedIn). Then you will notice for
          sure when it is out.
```

➊ 连接到 `Twitter` API。
➋ 从我的主时间线检索并打印 5 条（最近的）推文。
➌ 从用户时间线检索并打印 5 条（最近的）推文。
`Twitter` API 还允许搜索，根据搜索可以检索和处理最近的推文。

```
In [45]: d = t.search.tweets(q='#Python', count=7)  ➊

In [46]: for e in d['statuses']:
             print(e['text'])  ➊
         RT @KirkDBorne: #AI is Reshaping Programming – Tips on How to Stay on Top:
         —
         Courses:
         1: #MachineLearning – Jupyte...
         RT @reuvenmlerner: Today, a #Python student's code didn't print:

         x = 5
         if x == 5:
             print: ('yes!')

         There was a typo, namely : after pr...
         RT @GavLaaaaaaaa: Javascript Does Not Need a StringBuilder
          #programming #softwareengineering #bigdata
          #datascience...
         RT @CodeFlawCo: It is necessary to publish regular updates on Twitter
          #programmer #coder #developer #technology RT @pak_aims: Learning to C...
         RT @GavLaaaaaaaa: Javascript Does Not Need a StringBuilder
          #programming #softwareengineering #bigdata
          #datascience...
```

➊ 搜索带有“Python”标签的推文，并打印出最近的 5 条。
还可以收集某个 `Twitter` 用户的大量推文，并以词云的形式创建摘要（参见图 4-3）。下面的 `Python` 代码再次使用了 NLP 辅助函数，如 4.7 节中的 `Python` 代码所示。

```
In [47]: l = t.statuses.user_timeline(screen_name='elonmusk', count=50)  ➊

In [48]: tl = [e['text'] for e in l]  ➋

In [49]: tl[:5]  ➌
Out[49]: ['@flcnhvy @Lindw0rm @cleantechnica True',
          '@Lindw0rm @cleantechnica Highly likely down the road',
          '@cleantechnica True fact',
         '@NASASpaceflight Scrubbed for the day. A Raptor turbopump spin start valve
          didn’t open, triggering an automatic abo...
         '@Erdayastronaut I’m in the Boca control room. Hop attempt in ~33 minutes.']

In [50]: wc = nlp.generate_word_cloud(' '.join(tl), 35,
                    name='../../images/ch04/musk_twitter_wc.png'
                    )  ➍
```

➊ 检索用户 `elonmusk` 的最近 50 条推文。
➋ 收集 `list` 对象中的文本。
➌ 显示最后 5 条推文的摘录。
❹ 生成词云摘要并显示它。

![](Images/image00561.jpeg)

**图 4-3：大量推文的词云摘要**

一旦一个金融从业者定义了“相关金融数据”以超越结构化金融时间序列数据，数据来源在数量、多样性和速度方面看起来似乎就是无限的。从 `Twitter` API 检索推文的方式几乎是即时的，因为在示例中访问了最近的推文。因此，这些以及类似的基于 API 的数据源提供了无休止的非传统数据流。正如前面所指出的，重要的是要准确地指定所需的数据。否则，任何金融数据科学工作都可能很容易淹没在太多的数据或太嘈杂的数据中。

## 4.4　重新审视规范性理论

第 3 章介绍了规范性金融理论，比如 `MVP` 理论和 `CAPM`。在相当长的一段时间里，学生和学者或多或少地受限于学习和研究这些理论本身。如上一节所讨论和说明的，有了所有可用的金融数据，再加上强大的开源数据分析软件（比如 `Python`、`NumPy`、`pandas` 等），将金融理论用于现实世界的检验变得非常简单和直接。这样做不再需要小团队和大研究。一个典型的笔记本、互联网连接和标准的 `Python` 环境就足够了。这就是本节的内容。然而，在深入研究数据驱动的金融学之前，需要简要讨论 `EUT` 背景下的一些著名悖论，以及企业如何在实践中建模和预测个人行为。

### 4.4.1　预期效用与现实

在经济学中，**风险**描述了决策者提前知道未来可能出现的状态及其出现概率的一种情况。这是金融学和 `EUT` 的标准假设。相反，**模糊性**描述了决策者事先不知道的概率，甚至不知道可能的未来状态的情形。**不确定性**包含了这两种不同的决策情况。

在不确定性条件下，分析个体（“经济主体”）的具体决策行为有着悠久的传统。与 `EUT` 等理论所预测的不同，人们已经进行了无数的研究和实验来观察和分析经济主体在面对不确定性时的行为。几个世纪以来，**悖论**在决策理论和研究中发挥了重要作用。

其中一个悖论，即**圣彼得堡悖论**，催生了效用函数和 `EUT` 的诞生。Daniel Bernoulli 在 1738 年提出了这个悖论，并给出了解决方案。这个悖论基于下面的《抛硬币》游戏 ![G](Images/image00562.gif)。经济主体面临这样一个游戏：一枚（完美的）硬币可能被抛无数次。如果第一次抛硬币后正面朝上，那么该主体将获得 1 货币单位的回报。只要看到正面朝上，该主体就会再抛一次硬币，否则游戏结束。如果第二次仍然正面朝上，那么该主体将额外获得 2 货币单位的回报。如果第三次还是正面朝上，那么额外回报是 4 货币单位，第四次是 8 货币单位，以此类推。这是一种风险情况，因为所有可能的未来状态及其相关的概率都是预先知道的。

这个游戏的期望回报是**无限**的。这可以从下面的无限和中看出，其中的每一个元素都是严格为正的：

![\\mathbf{E}(G)=\\dfrac{1}{2}\\times1+\\dfrac{1}{4}\\times2+\\dfrac{1}{8}\\times4+\\dfrac{1}{16}\\times8+\\cdots=\\sum\\limits^{\\infty}\_{k=1}\\dfrac{1}{2^k}2^{k-1}=\\sum\\limits^{\\infty}\_{k=1}\\dfrac{1}{2}=\\infty](Images/image00563.gif)

然而，面对这样的游戏，决策者通常只愿意花**有限**的钱来玩。这样做的一个主要原因是，相对较大的回报只有在相对较小的概率下才会发生。考虑潜在回报 ![W=511](Images/image00564.gif)：

![W=1+2+4+8+16+32+64+128+256=511](Images/image00565.gif)

赢得这种回报的概率很低。确切地说，这只有 ![P(x=W)=\\dfrac{1}{512}=0.001~953~125](Images/image00566.gif)。另外，获得这种回报或更小回报的概率却是相当高的：

![P(x\\leqslant W)=\\sum\\limits^{9}\_{k=1}\\dfrac{1}{2^k}=0.998~046~875](Images/image00567.gif)

换句话说，1000 场游戏中有 998 场的回报是 511 或更小。因此，一个经济主体可能不会下超过 511 的赌注来玩这个游戏。摆脱这个悖论的方法是引入一个**边际效用为正但递减**的效用函数。在圣彼得堡悖论中，这意味着存在一个函数 ![u:\\mathbb{R}\_+\\to\\mathbb{R}](Images/image00568.gif)，它赋予每个正的回报 ![x](Images/image00332.gif)一个实数值 ![u(x)](Images/image00441.gif)。正但递减的边际效用为如下形式：

![\\begin{aligned}&\\dfrac{\\partial u}{\\partial x}>0\\\\&\\dfrac{\\partial^2 u}{\\partial x^2}<0\\end{aligned}](Images/image00569.gif)

如第 3 章所述，其中一个候选函数是 ![u(x)=\\ln(x)](Images/image00434.gif)，具有：

![\\begin{aligned}&\\dfrac{\\partial u}{\\partial x}=\\dfrac{1}{x}\\\\&\\dfrac{\\partial^2 u}{\\partial x^2}=-\\dfrac{1}{x^2}\\end{aligned}](Images/image00570.gif)

那么，预期效用是**有限**的，如下面的无限和的计算所示：

![\\mathbf{E}(u(G))=\\sum\\limits^{\\infty}\_{k=1}\\dfrac{1}{2^k}u(2^{k-1})=\\sum\\limits^{\\infty}\_{k=1}\\dfrac{\\ln(2^{k-1})}{2^k}=\\left(\\sum\\limits^{\\infty}\_{k=1}\\dfrac{(k-1)}{2^k}\\right)\\times\\ln(2)=\\ln(2)<\\infty](Images/image00571.gif)

与无穷大的预期回报相比，![\\ln(2)=0.693~147](Images/image00572.gif)这一预期效用显然是一个非常小的数字。伯努利效用函数和 `EUT` 解决了圣彼得堡悖论。

其他悖论，比如 Allais（1953）提出的**阿莱悖论**，解决了 `EUT` 本身。这个悖论基于有 4 个不同游戏的一个实验，在该实验中，应该对测试对象进行排名。表 4-2 显示了 4 个游戏（![A](Images/image00404.gif)、![B](Images/image00405.gif)、![A'](Images/image00573.gif)、![B'](Images/image00574.gif)）。对 ![(A,B)](Images/image00575.gif)和 ![(A',B')](Images/image00576.gif)进行排序。**独立性公理**假设表中的第一行对 ![(A',B')](Images/image00576.gif)的排序没有任何影响，因为两个博弈的回报是相同的。

**表 4-2：阿莱悖论中的博弈**

| 概率 || 游戏*A* || 游戏 *B* || 游戏 *A'* || 游戏 *B'* |

| --- | --- | --- | --- | --- |

| 0.66 || 2400 || 2400 || 0 || 0 |

| --- | --- | --- | --- | --- |

| 0.33 || 2500 || 2400 || 2500 || 2400 |

| 0.01 || 0 || 2400 || 0 || 2400 |

在实验中，大多数决策者对博弈的排序如下：![B\\succ A](Images/image00577.gif)和 ![A'\\succeq B'](Images/image00578.gif)。排序 ![B\\succ A](Images/image00577.gif)会导致以下不等式，其中 ![u\_{1}\\equiv u(2400),u\_{2}\\equiv u(2500),u\_{3}\\equiv u(0)](Images/image00579.gif)：

![\\begin{aligned}u\_1&>0.66\\times u\_1+0.33\\times u\_2+0.01\\times u\_3\\\\0.34\\times u\_1&>0.33\\times u\_2+0.01\\times u\_3\\end{aligned}](Images/image00580.gif)

排序 ![A'\\succeq B'](Images/image00578.gif)进而导致以下不等式：

![\\begin{aligned}0.33\\times u\_2+0.01\\times u\_3&>0.33\\times u\_1+0.01\\times u\_1\\\\0.34\\times u\_1&<0.33\\times u\_2+0.01\\times u\_3\\end{aligned}](Images/image00581.gif)

这些不等式显然相互矛盾，并导致了阿莱悖论。一种可能的解释是，一般情形下决策者对价值确定性要求高于典型模型（如 `EUT`）的预测。虽然在 `EUT` 下有许多可用的效用函数会让决策者选择游戏而不是确定性回报，但大多数人可能宁愿选择确定获得 100 万美元，而不是玩一个可以赢得 1 亿美元的可能性为 5% 的游戏。

另一种解释在于决策**框架**和决策者的心理。众所周知，接受“95% 成功概率”手术的人比接受“5% 死亡概率”手术的人要多。简单地改变措辞可能会导致与 `EUT` 等决策理论不一致的行为。
