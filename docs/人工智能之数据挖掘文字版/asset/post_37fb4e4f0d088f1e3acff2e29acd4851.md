
![](img/7f4c9837ed6feec4001ca93ea1661633_8_0.png)

图 1 数据挖掘是知识发现的过程之一

早期比较有影响力的发现算法有：IBM 的 Rakesh Agrawal 的关联算法（IBM Fellow、后来 MS Technical Fellow、第一届 ACM SIGKDD 的创新奖得主）、UIUC 大学韩家炜（Jiawei Han）教授等人的 FP Tree 算法（第四届 ACM SIGKDD 的创新奖得主）、澳大利亚的 John Ross Quinlan 教授的分类算法（第十一届 ACM SIGKDD 的创新奖得主）、密歇根州立大学 Erick Goodman 的遗传算法。同时已经有一些国际知名公司纷纷加入数据挖掘技术研究的行列，如：美国的 IBM 公司于 1996 年研制的智能挖掘机 Intelligent Miner，用来提供数据挖掘解决方案，此后出现了 SAS 公司的 Enterprise Miner、SGI 公司的 SetMiner、Sybase 公司的 Warehouse Studio，还有 CoverStory、EXPLORA、Knowledge Discovery Workbench、DBMiner、Quest 等等。

### 1.1.1 数据挖掘分类

表 2 数据挖掘分类表

| 分类依据 | 文字型 | 网络型 | Time 型 | Space 型 |
| :--- | :--- | :--- | :--- | :--- |
| 按挖掘的数据库类型 | 文字型 | 网络型 | Time 型 | Space 型 |
| 按挖掘的知识类型 | 高抽象层 | | 原始数据层 | 多个抽象层 |
| 按所用技术类型 | 模式识别 | 神经网络 | 可视化 | 统计学、面向数据库或仓库技术 |

- 按挖掘的数据库类型分类

由于数据库有约定俗成的分类方式，例如数据模式、数据类型、应用环境等分类种类，以上几种数据库都有属于自己特有的数据挖掘技术；数据库之间可以互相对应，根据数据库类型定义数据挖掘技术的方法可行。数据挖掘技术若按照数据类型进行分类，可以分为文字型、网络型、Time 型、Space 型等³。

3 王桂芹，黄道 数据挖掘技术综述 [J] 电脑应用技术 2007 (69): 10-11.

- 按挖掘的知识类型分类

数据挖掘技术可以按照数据挖掘技术的功能划分，可划分为分析数据的内在规律、分析数据间的内在联系、定义描述等。一个数据挖掘全过程会同时由以上二个、三个或者多个功能组成。数据挖掘还可以划分为广义知识、原始层知识、多层知识等类别。也就是专业术语所说的高抽象层、原始数据层、多个抽象层等类别。经典的数据挖掘技术通常能够找到多层知识。数据挖掘技术也能够按照其内在规律和奇特的异常性进行分类。通常来说，数据的内在规律可以通过分析相关性数据、找出数据之间的内在联系、定义描述、集合类的对象为多个类和估算等方法挖掘⁴。

- 按所用的技术类型分类

数据挖掘按照技术类型划分为：模式识别、神经网络和可视化、机器学习、统计学、面向数据库或仓库技术等；也可按照数据分析方法划分为：建模并模拟神经网络、进化算法、集合类似的对象为多个类、分类树、推演规律等。大型的数据挖掘系统通常包含二种或三种以上挖掘方法，或者吸取多种挖掘方法的优点来处理数据挖掘。

- 按应用分类

数据挖掘技术应用的领域不同，分类也不同。比如生物医学行业、交通行业、金融行业、通信行业、股市行业等都有自己合适的且已广泛应用的数据挖掘方法。因此不可能做到用同一个数据挖掘技术应用到各个行业领域。

### 1.1.2 数据挖掘过程

数据挖掘是指一个完整的过程，该过程从大型数据库中挖掘先前未知的、有效的、可实用的信息，并使用这些信息做出决策或丰富知识。下图描述了数据挖掘的主要步骤和过程。

数据挖掘过程中各步骤的大体内容如下：

第一步：确定挖掘目的。认清数据挖掘的目的是数据挖掘的重要一步。挖掘的最后结果是不可预测的，但要探索的问题应是有预见的。不能盲目的为了数据挖掘而数据挖掘。

第二步：数据准备。数据准备分为三个阶段。①数据的选择：搜索所有与目标对象有关的内部和外部数据信息，并从中选择出适用于数据挖掘应用的数据。②数据的预处理：研究数据的质量，为进一步的分析做准备，并确定将要进行的挖掘操作的类型。③数据的转换：将数据转换成一个分析模型。这个分析模型是针对挖掘算法建立的。建立一个真正适合挖掘算法的分析模型是数据挖掘成功的关键。

![](img/7f4c9837ed6feec4001ca93ea1661633_10_0.png)

图 2 数据挖掘过程基本步骤

第三步：进行数据挖掘。对得到的经过转换的数据进行挖掘。

第四步：结果分析。解释并评估结果，其使用的分析方法一般应视数据挖掘操作而定，通常会用到可视化技术。

第五步：知识的同化。将分析所得到的知识集成到所要应用的地方去⁵。

### 1.1.3 数据挖掘功能

目前数据挖掘的主要功能包括概念描述、关联分析、分类、聚类和偏差检测等。概念描述主要用于描述对象内涵并且概括此对象相关特征，概念描述分为特征性描述和区别性描述，特征性描述描述对象的相同特征，区别性描述描述对象的不同特征；关联分析主要用来发现数据库中相关的知识以及数据之间的规律，关联分为简单关联、时序关联、因果关联；分类和聚类就是根据需要训练相应的样本来对数据分类和合并；偏差分析用于对对象中异常数据的检测。

### 1.1.4 大数据与数据挖掘

大数据是近年随着互联网、物联网、通信网络以及人类社交网络快速发展的结果，成为一个交叉研究学科，和数据挖掘紧密相连。一方面大数据包含数据挖掘的各个阶段，即数据收集、预处理、特征选择、模式挖掘、表示等；另一方面大数据的基础架构又为数据挖掘提供上层数据处理的硬件设施；最后大数据的迅速发展也使得数据挖掘对象变得更为复杂，不仅包括人类社会与物理世界的复杂联系，还包括呈现出的高度动态化。这使得很多传统数据挖掘算法不再适用，传统数据挖掘算法必须满足对真实数据和实时数据的处理能力，才能从大量无序数据中获取真正价值。

大数据具有 4V 特性，对 4V 特性的解释有多种，包括美国国家标准技术研究院 NIST 的解释：即规模庞大（Volume）、种类繁多（Variety）、增长速度快（Velocity）和变化多样（Variability）。IBM 给出了类似的解释，但 Variability 变成了真实性（Veracity），后来将数据价值（Value）引入了进来，成为了大数据的 5V 特性。麦肯锡全球研究机构（McKinsey Global Institute）后来给出了大数据定义，综合了“现有技术无法处理”和“数据特征定义”定义，这也是目前比较公认的定义：即规模庞大（Volume）、种类繁多（Variety）、变化频繁（Velocity）和价值（Value）。

大数据的快速发展极大的促进了数据挖掘领域的发展。

## 1.2 数据挖掘知识图谱

本节分析了近年来数据挖掘领域的高水平学术论文，挖掘出了包括社交网络、大数据、情报分析、聚类分析、文本挖掘、用户行为、推荐系统、离群检测、专家系统等相关关键词近年来全球活跃的学术研究。此外，结合知识图谱技术，本报告将以上研究领域表示为三级图谱结构，具体分析和处理的方法如下：

- 1. 使用自然语言处理技术，提取每篇论文文献的关键词，据此，结合学科领域知识图谱，将文章分配到相应领域；
- 2. 依据学科领域对论文文献进行聚类，并统计论文数量作为领域的研究热度；
- 3. 领域专家按照领域层级对学科领域划分等级，设计了三级图谱结构，最后根据概念热度定义当前研究热点。

下图是数据挖掘二级知识图谱的可视化表示，三级详细数据可以参见本报告附录，或到 https://www.aminer.cn/data 中直接下载原始数据。

![](img/7f4c9837ed6feec4001ca93ea1661633_11_0.png)

图 3 Data Mining 知识图谱

## 1.3 近年 SIGKDD 概况

国际知识发现与数据挖掘大会（ACM SIGKDD Conference on Knowledge Discovery and Data Mining, 简称 SIGKDD）是数据挖掘领域的顶级国际会议，由 ACM 的数据挖掘及知识发现专委会负责协调筹办。会议内容涵盖数据挖掘的基础理论、算法和实际应用，SIGKDD 主会期间，除了学术研究论文，自 2010 年起还设有面向工业和政府应用的专题研讨会，以及工业应用博览的邀请报告会。

SIGKDD 发展的历史可以追溯到 1989 年，一系列关于知识发现及数据挖掘的研讨会从那时开始组织。自 1995 年以来，SIGKDD 以大会的形式连续举办了 24 届，论文的投稿量和参会人数呈现出逐年累增的趋势。由于 SIGKDD 的学科交叉性和广泛应用性，吸引了来自统计、社会网络分析、机器学习、大数据挖掘、数据库、万维网、生物信息学、多媒体、自然语言处理、人机交互及高性能计算等众多领域的学者。

SIGKDD 每年的大都会都会吸引大量的研究界和工业界的投稿。图 4 和图 5 分别给出了 KDD 近几年研究性论文（Research Track）和工业界论文（Industrial Track，最近改为 Applied Data Science Track）的投稿和录用情况。总的来说研究性论文投稿相对比较稳定，录用率也一直在 14%-20%之间；而工业界论文近年呈现明显快速增长趋势。尤其 2018 年达到近 500 篇投稿。投稿录用率也降到 20%以下。这与近年深度学习、人工智能、大数据等相关算法在工业界大量应用密不可分。

![](img/7f4c9837ed6feec4001ca93ea1661633_12_0.png)

图 4 2013-2018 KDD 研究性论文投稿与接收情况

### Applied Data Science Track

![](img/7f4c9837ed6feec4001ca93ea1661633_13_0.png)

图 5 2013-2018 KDD 工业界论文投稿与接收情况

下面就近几年 SIGKDD 大会分别做一个简单概述，相信读者能够对整个 SIGKDD 乃至数据挖掘领域有个更深入的了解。

### (1) SIGKDD 2013

2013 年 8 月 11-14 日，第 19 届知识发现与数据挖掘大会（SIGKDD 2013）在美国芝加哥召开，此次大会的主题是大数据挖掘。

SIGKDD 2013 大会的主席是前通用汽车高级研究经理 Ramasamy Uthurusamy 博士和伊利诺伊芝加哥大学的 Robert L. Grossman 教授，研究性程序委员会主席由来自德克萨斯奥斯丁大学的 Inderjit S. Dhillon 教授和 Google 的 Yehuda Koren 博士担任，另外有 50 名高级程序委员会委员和 300 名程序委员负责论文评审。吸引了来自全球 50 多个国家 1200 多人参加。

SIGKDD 2013 大会收到来自 50 多个国家的 726 篇论文投稿，每篇文章由至少 3 名审稿人评审，然后由相应领域的高级程序委员协调讨论并推荐，最后由程序主席决定是否接收。最终录用论文 125 篇（录用率约 17.2%），其中大会报告论文 66 篇（仅 9.1%）、张贴报告论文（Poster Presentation）59 篇。

SIGKDD 2013 大会邀请了微软的 Raghu Ramakrishnan、斯坦福的 Andrew Ng、威斯康辛的 Stephen J. Wright 以及 Google 的 Hal Varian 四位在产业界与学术界均产生重要影响的专家进行大会主题报告。

SIGKDD 2013 的具体获奖情况如下：

- 最佳研究性论文
- 最佳论文奖
- Simple and Deterministic Matrix Sketching
- 简易及确定性矩阵草图

作者：Edo Liberty

摘要：该论文研究的问题是针对给定的矩阵 A，如何能找到一个更小的压缩矩阵 B 对原矩阵 A 进行估计，这是基于社交网络大数据分析中的一个关键问题。

地址：https://www.aminer.cn/archive/simple-and-deterministic-matrix-sketching/53e9b500b7602d970402e5e6

### 最佳论文奖第二名

### Querying Discriminative and Representative Samples for Batch Mode Active Learning

批量模式主动学习的查询判别样本和代表性样本

作者：Zheng Wang, Jieping Ye

摘要：他们解决了在主动学习中如何能够挑选同时具有代表性又具有区分性的样本。

地址：https://www.aminer.cn/archive/querying-discriminative-and-representative-samples-for-batch-mode-active-learning/53e9bba7b7602d97047f32a6

### 最佳学生论文奖

### A space efficient streaming algorithm for triangle counting using the birthday paradox

一种利用生日悖论的空间高效三角形计数流算法

作者：Madhav Jha、C. Seshadhri、Ali Pinar

摘要：该论文设计了一种节省空间的算法，该算法近似于传递性（全局聚类系数）和总三角形计数，只有一次通过作为边缘流给出的图形。模型基于经典的概率结果，即生日悖论，作为单通道流式算法，通过存储极小部分的边缘来维持图的三角形的传递性/数量的实时估计。

地址：https://www.aminer.cn/archive/a-space-efficient-streaming-algorithm-for-triangle-counting-using-the-birthday-paradox/53e9999eb7602d97021e514e

### 最佳学生论文奖第二名

### Recursive Regularization for Large-scale Classification with Hierarchical and Graphical Dependencies

具有层次结构和图形依赖性的大规模分类的递归正则化

作者：Siddharth Gopal、Yiming Yang

摘要：该篇论文的目的是为大范围的层次分类，提出一种规则化框架，一次解决两个问题：1.利用 class-labels 间的层次依赖性提升性能；2.同时保持大规模的层次间的可扩展性。

地址：https://www.aminer.cn/archive/recursive-regularization-for-large-scale-classification-with-hierarchical-and-graphical-dependencies/53e9aac3b7602d97034430d3

### 最佳工业界与政府论文

### 最佳论文奖

### Amplifying the Voice of Youth in Africa via Text Analytics

通过文本分析放大非洲年轻人的心声。

作者：Prem Melville 及 UNICEF 的多名研究人员

摘要：他们的课题是通过文本分析，让世界听到非洲的年轻一代发出的声音，从某种程度上也体现了 KDD 注重将数据挖掘用于为世界公益做出贡献的精神。

地址：https://www.aminer.cn/archive/amplifying-the-voice-of-youth-in-africa-via-text-analytics/53e9b09fb7602d9703b07333

### 最佳论文奖第二名

### Query Clustering based on Bid Landscape for Sponsored Search Auction Optimization

基于竞价格局的查询聚类对赞助搜索竞价优化

作者：微软公司以 Ye Chen 为代表的一众人

摘要：集群查询更小但更有效的微市场，定义为基于集群的拍卖参数。

地址：https://www.aminer.cn/archive/query-clustering-based-on-bid-landscape-for-sponsored-search-auction-optimization/53e9af2cb7602d9703964e4f

### 最佳博士论文

### 最佳博士毕业论文

### Mining Heterogeneous Information Networks

挖掘异构信息网络

作者：Yizhou Sun 博士，其导师为韩家炜（Jiawei Han）教授

摘要：论文从许多将互连数据视为同构图或网络的现有网络模型中，研究了挖掘异构信息网络的原理和方法，半结构化异构信息网络模型利用了类型化节点和链接的丰富语义。这种半结构化异构网络建模为挖掘互联数据提供了一系列新原则和强大的方法。

地址：https://www.aminer.cn/archive/mining-heterogeneous-information-networks-a-structural-analysis-approach/53e9ae3cb7602d9703851185

### 最佳博士毕业论文第二名

### Machine Learning in Health Informatics: Making Better use of Domain Experts

健康信息学中的机器学习：更好地利用领域专家

作者：Byron Wallace 博士，其导师为 Carla Brodley 教授。

摘要：此次他们集中关注了临床信息学和具有大量信息的典型案例，发现因为临床信息学内在特性的原因，解决相关问题需要专业领域的知识，而该领域现有的机器学习技术普遍性能表现较弱。该论文目的旨在使用新奇的机器学习与数据挖掘方法，让现实世界中的学习系统更加有效率。

地址：https://www.aminer.cn/archive/machine-learning-in-health-informatics-making-better-use-of-domain-experts/56d890a4dabfae2eee4ca4d

### SIGKDD 创新和服务大奖

### 创新贡献奖: Jon Kleinberg (康奈尔大学)

2013 年创新奖（Innovation Award）由康奈尔大学的 Jon Kleinberg 教授获得，以表彰他在社交网络和信息网络分析中的传递行为以及用户个体行为建模方面的工作。Jon Kleinberg 是社会网络分析方向的知名学者，三十余岁时即当选为美国科学院、工程院、艺术与科学院院士，代表算法是 HITS。

SIGKDD 创新奖是知识发现与数据挖掘领域（KDD）的最高荣誉，授予对这一领域做出重大技术贡献的研究人员。根据评审要求，其研究成果必须在数据挖掘理论或商业数据挖掘系统的开发上能够产生深远的影响。这是 SIGKDD 每年最重头的奖项，也是数据挖掘领域最高技术奖项。

### 服务贡献奖: Gabor Melli (索尼互动娱乐)

2013 年杰出服务奖（Service Award）由 Gabor Melli（索尼互动娱乐）获得。Gabor Melli 博士因其对数据挖掘实践和应用的重大技术贡献，以及对全球 KDD 社区的杰出服务而受到表彰。他长期为 KDD 社区服务，从 1997 年开始，他在 datasetgenerator.com 上发布了合成数据集生成器。在 2005 年，他与伙伴共同创立了数据挖掘案例研究研讨会系列（dataminingcasestudies.com），并成为 SIGKDD 信息总监。从那时起，他参与组织了许多 KDD，ICDM 和 CIKM 国际会议，担任出版主席，研讨会主席，赞助主席，竞赛主席，展览主席和演示主席等职务，同时担任 KDD，CIKM，PAKDD 的评委和 ACM 计算调查。2013 年，Gabor Melli 博士启动了一个半自动创建广泛的 KDD 概念网络知识库（www.gabormelli.com/RKB/）的项目。

SIGKDD 杰出服务奖主要授予在知识发现及数据挖掘领域作出重大服务贡献的个人或团队，包括主办会议、主持学术团体等服务性工作，并在数据挖掘教学及财务性事务等方面的工作。该奖主要奖励对知识发现及数据挖掘领域作出重大服务贡献的个人或团队，考察的因素主要包括主持学术团体、主办会议等服务性工作，教育学生、研究者和实践者，资助研发活动，为传播技术信息提供专业志愿服务，并通过知识挖掘应用为社会做出贡献，改善全球性医疗、教育、灾难 / 危机管理及环境等议题。

### KDD CUP 竞赛

- 第一名: Algorithm 队
