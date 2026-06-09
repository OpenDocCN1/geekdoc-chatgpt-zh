# AI科研绘图：Nano Banana极速实战指南

# 内容提要

人工智能（AI）绘图模型在图像生成领域的快速发展，为科研插图创作提供了技术支持。本书围绕科研绘图构建了基于 AI 绘图模型的系统方法，适配多学科场景的视觉化表达，针对科研工作者在论文插图绘制中面临的痛点，系统提供围绕科研绘图的方法体系。第一章从认知层面重新定义科研插图的本质与价值，明确“好图”标准及 AI 绘图的必要性；第二章以 Nano Banana Pro 为例，讲解模型接入、核心功能及辅助工具使用；第三章至第五章聚焦方法论，包括从论文内容抽象结构关系的策略、不同学科场景下的提示词构建、复杂长图的模块化生成及矢量化管理；第六章则梳理 AI 辅助绘图的伦理边界与学术规范。全书旨在通过结构抽象与精准表达的底层逻辑，帮助研究者提升科研插图质量与效率，既适用于熟悉 AI 工具者建立稳定工作流，也为初学者提供实践起点。

本书适合所有需要绘制科研论文插图的研究者，尤其是缺乏设计背景、希望提升表达质量与效率的硕博研究生与青年教师阅读。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

# 前言

翻开本书的你，或许正经历一个熟悉却难以言说的阶段：论文结构已经成型，实验数据反复验证，推导过程经得起推敲，唯独插图迟迟无法定稿；一张结构示意图来回调整，一套配色反复推翻，时间在无声流逝。插图的问题，往往并不只是排版层面的细节。高质量的插图往往决定论文的第一印象，清晰的表达与结构，则直接影响审稿人与读者在有限时间内对论文核心贡献的判断。

近年来，人工智能（Artificial Intelligence，AI）在图像生成领域快速发展。AI 绘图模型质量持续提升，细节表达愈发稳定，结构控制能力显著增强。越来越多的人开始尝试借助 AI 完成设计与创作。作为一名科研工作者，在为论文绘制插图的实践过程中，我逐渐开始思考一个问题：这些模型，能否真正服务于科研场景，而不仅停留在艺术创作层面？

最初的尝试并不顺利。提示词不准确时，生成结果杂乱无章；结构关系表达模糊时，模型难以理解逻辑。在反复试错中，我发现真正决定出图质量的核心，在于如何将论文内容转化为清晰的结构表达，再用精准的语言加以描述。随着这套方法逐渐成型，Nano Banana Pro 等 AI 绘图模型终于能稳定输出符合学术审美的插图初稿。这种从偶然盲测到稳定输出的转变，让我萌生了将这条路径系统整理出来的想法。

起初，我将这套方法浓缩为文章发布在 Datawhale 社区公众号上，获得了广泛传播与积极反馈。但在留言区，许多研究者针对工具平替、后期可编辑性、跨学科适用性及学术规范等问题提出了具体困惑。这些真实的声音让我深感，单篇文章的体量或许能讲透一个核心工作流，却远不足以承载一个完整的体系。在社区的支持与出版团队的推动下，将这套方法系统化成书的想法才最终落地。

正是为了回应这些痛点，补齐工作流中的缺环，本书并非简单的工具使用指南，而是致力于提供一套围绕科研绘图展开的系统方法。第一章从认知层面重新审视科研插图的本质，将其视为科学信息的视觉化转译。第二章以 Nano Banana Pro 为例，讲解具体操作路径与提示词构建逻辑，同时补充介绍 Qwen-image-2.0 等模型的使用入口，以期快速建立实践能力。第三章与第四章聚焦方法论，探讨如何从论文内容抽象出结构关系，并在计算机、材料与化学、生物与医学等不同学科场景下构建清晰、可控的视觉表达。第五章进入进阶阶段，强调精准控制、风格沉淀与体系化管理。第六章则专门对学术伦理边界进行梳理，确保在拥抱技术、提升效率的同时，守住学术规范的底线。

纵观全书，本书的核心价值，不仅在于节省科研绘图时间，更在于启发一种全新的思考方式。科研插图并不只是美化页面的附属元素，它承载着逻辑、结构与思想。一旦掌握了结构抽象与表达的核心方法，工具的更迭便不再令人焦虑。无论是 Nano Banana Pro、Qwen-image-2.0，还是未来更强大的模型，这套底层逻辑都将始终适用。

基于这样的理念，本书面向所有需要绘制科研论文插图的研究者，尤其是缺乏设计背景、却希望提升表达质量与效率的硕博研究生与青年教师。如果你已经熟悉 AI 绘图工具，这本书能帮助你建立更稳定的工作流与个人风格；如果你尚在观望，它也可以成为一次实践的起点。

在阅读与实践的过程中，希望你能保持独立思考与判断。技术不断演进，学术规范也在同步完善。如何在工具创新与学术诚信之间确立边界，是每一位研究者必须面对的课题。需要说明的是，本书旨在提供方法与经验，最终的合规责任与学术判断，仍需由研究者独立承担。

最后，谨向在本书写作与出版过程中给予支持与帮助的师长与同仁致以诚挚感谢。感谢导师陈太聪老师在研究与写作阶段给予持续的鼓励，使相关探索得以系统梳理并形成完整框架。感谢 Datawhale 社区周理璇（Amy）、范晶晶、马晓皖、卢水琼及社区成员在传播与实践交流中的支持与反馈，使书稿不断完善。感谢人民邮电出版社信息技术分社陈冀康社长与策划编辑卜一凡在出版过程中的专业指导与耐心审阅。同时，感谢家人长期以来的理解与陪伴。正是这些支持，使本书得以顺利完成。

愿这本书，成为你提升科研表达能力的得力助手。当你再次面对空白画布时，心中已有结构，手中已有方法。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

# 第一章
认知篇：重新定义科研绘图

在科研道路上，许多人都有过类似的困境。实验数据终于跑通，论文逻辑也已理顺，最后却卡在了插图绘制这一关。用 PPT 拼凑的流程图显得不够专业，用 Visio 连线时总是难以对齐，想要学习专业三维软件又缺乏时间。更令人头疼的是面对审稿人的修改意见，哪怕只是改动一个模块，整张图可能都需要推倒重来。我们往往将大量宝贵的科研时间，消耗在了对齐线条和调整配色这些机械劳动之中。

本章将从认知的角度重新审视科研绘图。我们需要理清在 AI 时代，哪些图必须亲力亲为，哪些环节可以借助 AI 提升效率，并学会以审稿人的视角去审视一张图的优劣。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 1.1
科研绘图的本质与边界

科研绘图有别于艺术创作，其本质在于 **科学信息的视觉化转译** 。

在科研论文中，图表的核心任务在于降低读者的认知负荷，以最高的效率传递科学逻辑。面对种类繁多的图表，科研人员需要建立一个清晰的分类坐标系，这决定了工具的选择和学术伦理的边界。

在实际科研写作中，由于科研领域不同，研究者通常会遇到多种类型的插图。为了明确不同图像的使用边界，这里采用一种工作性的分类方式，将常见科研插图概括为三类。

**第一类是定量数据图。** 这类图表直接反映实验结果，包含折线图、柱状图、散点图等。其核心特征在于每一个像素点都对应着真实的实验数值。在此类图表中，主流学术期刊普遍不允许使用生成式 AI 直接生成图像内容。AI 的生成机制基于概率预测，产出的数据点往往是基于像素规律的“幻觉”，而非真实数据。正确的做法是继续使用 Python、Origin 等专业软件作图，AI 仅可用于提供配色建议或编写绘图代码。图 1-1 所展示的柱状统计图即为一类典型的定量数据图。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94829_0.jpg)

图 1-1 定量数据图参考图 ^([1])

Nature 期刊官方在其图像完整性政策中指出，投稿的数字图像必须正确反映原始数据，不允许使用任何会改变数据本质或掩盖原始数据的手段，期刊编辑可能会使用软件检测图像处理，并在需要时要求作者提供原始未处理数据。

**第二类是实证影像图。** 这类图是实验的直接证据，包括显微镜照片、电泳图、实物装置图等。它们记录客观事实，讲究原真性。此类图像通常不允许进行包括 AI 去噪或放大等生成式填充修改，仅允许全图线性的亮度/对比度调整。图 1-2 所示的电子显微镜下细胞结构图像是实证影像图的典型代表。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94829_1.jpg)

图 1-2 实证影像图参考图 ^([2])

**第三类是定性示意图。** 这才是本书聚焦的核心领域，也是最耗费科研人员精力的部分。它不依赖具体数值，侧重于表达逻辑、流程、机制和概念，是你脑中科学假说和逻辑推演的具象化，正如图 1-3 所示的典型机制示意图。无论是展示细胞信号通路的原理图，还是描述算法模型架构的流程图，亦或是用于吸引眼球的期刊封面，都属于此列。

需要明确的是，在当前主流期刊的规范下，即便不涉及数据，AI 直接生成的示意图通常也不宜作为论文终稿直接提交。但这类图像在设计阶段具有极高价值。Nano Banana Pro 能够理解复杂的结构化文本，将抽象逻辑快速转化为高质量的视觉草案，为科研人员提供成熟的构图思路与风格参考，从而显著降低后续人工绘制与修改的时间成本。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94829_2.jpg)

图 1-3 定性示意图参考图 ^([3]) EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 1.2
什么样的图是“好图”？

当我们评价一张图时，不能只凭主观感觉说“好看”或“难看”。在同行评审的语境下，一张能被顶刊接收的插图，往往需要同时满足三个核心维度：科学性、逻辑性和艺术性。这三者共同决定了图片的专业度与说服力。与此同时，科研插图的审美也具有一些通用的秩序法则，并因学科领域的不同而存在显著差异。本节将从特性定义、通用准则到领域适配三个层面，梳理“好图”的达成逻辑。

### 1.2.1 “好图”具有的特性

首先， **科学性是不可逾越的底线** 。无论构图多么精致，只要违反基本科学常识，这张图就失去了存在价值。科学性是科研绘图的生命线，同时也是最基础的合格要求。它的核心标准只有一个：诚实。所有视觉表达都必须真实反映数据、模型和实验事实，任何形式的误导都会直接导致失败。

其次， **逻辑性决定叙事是否顺畅** 。一张好的科研图还需要具备自明性。读者在不阅读正文的情况下，应当能够仅凭图片和图注理解作者的核心表达。这要求图中的信息呈现具有明确的先后顺序，视线流动自然，没有需要反复比对或猜测含义的地方。

**![](img/AI科研绘图：Nano Banana极速实战指南_content_94830_0.jpg) 视觉层级（Visual Hierarchy）**

通过位置、大小、对比度和颜色的差异，在图中建立清晰的阅读优先级，引导读者先看到最重要的信息，再逐步关注次要内容。

当视觉层级清晰时，读者几乎无需刻意思考“该从哪里看起”，理解过程会变得顺畅而低负担。这也是顶级期刊插图往往显得“稳定”“专业”的重要原因之一。在这一层面，理性判断尤为重要。通过删减无关装饰、控制背景复杂度、统一配色体系，可以确保视觉注意力始终聚焦在关键路径上，同时弱化次要信息对理解的干扰。

最后， **艺术性体现对认知负荷的管理能力。** 艺术性是科研绘图中最容易被误解的维度。在科研语境下，它并不等同于装饰或炫技，而是利用视觉心理学规律，降低大脑处理信息的成本。一张让人感到“清晰”“舒服”的图，往往源于其顺应了人类感知视觉信息的本能。在这一视角下，艺术性并非装饰性的追求，而是一种高度功能化的设计能力。合理的布局、克制的配色和清晰的结构，最终服务的都是理解效率，而非视觉炫技。

### 1.2.2 科研美学的通用法则：克制与秩序

要实现上述特性，构建具有权威感的科研美学，无论属于哪个学科，都需要遵循一套共通的视觉语法，其核心在于极简和有序。

首先是 **减法思维** 。在科研绘图中，很多问题并非源于信息不足，而是信息过载。无关背景、装饰性线条和视觉效果会持续消耗读者的注意力，使真正重要的数据被淹没。成熟的科研审美往往体现为克制，当干扰项被移除后，数据本身会自然成为视觉焦点。

**![](img/AI科研绘图：Nano Banana极速实战指南_content_94830_0.jpg) 数据墨水比（Data–Ink Ratio）**

图形中用于呈现数据的信息墨水应尽可能占据更高比例，所有不直接承载数据或必要结构的信息元素都应被削减。优秀的可视化应当最大化数据表达，最小化视觉噪声。

在“数据墨水比”的原则下，默认灰色背景、密集网格线、立体阴影和冗余边框都属于典型的低价值元素。它们并不会帮助理解数据，反而会打断读者的视觉注意力。当这些元素被移除后，图形往往会立刻变得清晰而有力量。

其次是 **利用布局建立秩序。** 顶级期刊的组图之所以看起来整齐而专业，很大程度上源于对人类视觉直觉的充分利用。将相关的实验结果紧密排列，读者会下意识地把它们视为同一组；在不同图板中为同一变量保持一致的颜色或符号，即便不加文字说明，读者也能迅速建立起对应关系。这种通过空间位置和视觉相似性形成的内在秩序，使信息理解几乎不需要额外思考。

**![](img/AI科研绘图：Nano Banana极速实战指南_content_94830_0.jpg) 格式塔原则（Gestalt Principles）**

人的大脑天然倾向于把位置接近、形态相似的元素归为一个整体，科研绘图正是借助这一认知规律，在不增加文字负担的情况下完成逻辑表达。

最后是 **科学的色彩管理。** 颜色是传递数据的载体而非装饰品。传统彩虹色谱因亮度突变容易制造数据幻觉，已被可视化界公认为具有误导性，现代标准推荐使用 Viridis 或 Magma 等感知均匀的色谱。同时，考虑到色觉障碍人群的需求，避免使用红绿配色的组合，转而采用洋红色与绿色搭配，这不仅体现了学术包容性，更在视觉上呈现出更高的对比度与专业感。

**![](img/AI科研绘图：Nano Banana极速实战指南_content_94830_0.jpg) 感知均匀色谱（Perceptually Uniform Colormap）**

这类色谱在亮度和颜色变化上更加平滑，不会因为颜色跳变而夸大或掩盖数据差异，从而降低视觉误判的风险。

### 1.2.3 领域风格的定向适配

在掌握通用法则的基础上，我们还需洞察不同学科领域的审美偏好，这决定了你的插图是否具有“圈内人”的味道。目前的顶刊审美主要呈现出两种截然不同的取向。

一种是偏向物理、计算机与人工智能领域的极简主义风格。以 CVPR、NeurIPS 会议或 Nature Physics 为代表，这类插图偏好扁平化设计与矢量感。以图 1-4 所示的机制图为例，它们多使用低饱和度的莫兰迪色系，线条硬朗清晰，强调拓扑结构与逻辑流的直接表达，排斥不必要的三维渲染与光影修饰。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94830_4.jpg)

图 1-4 计算机领域插图风格 ^([3])

另一种则是偏向生物、医学与材料科学的拟真风格。以 Nature、Science 或 Cell 为代表，这类插图更青睐三维质感与真实环境的复现。它们强调微观细节的丰富度，常利用环境光遮蔽（AO）与次表面散射（SSS）等渲染技术来模拟细胞、蛋白质或纳米材料的真实质感，通过极强的视觉冲击力来营造沉浸式的微观世界。图 1-5 所示的生物医学领域插图即体现了这一拟真风格。

理解并对齐这两种审美取向，能让你的插图瞬间获得审稿人的专业认同，从而在同行评审中占据先机。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 1.3
为什么选择 AI 绘图？

你或许还在犹豫：传统的 PPT 和 Visio 虽然慢，但也够用，真的有必要引入 AI 吗？即便不考虑最终成稿，单纯从设计与迭代效率的角度看，这一步改变就已经非常值得。其实单纯为了效率，这点改变就完全值得。回想一下，以前为了画一张复杂的神经网络架构图，半天的时间很容易就搭进去了。而现在利用 AI，输入指令后几分钟就能得到多种方案。这能让你从繁琐的“对齐、连线”中解脱出来，腾出更多精力去关注论文逻辑与实验本身。

除了快，AI 绘图还解决了一个让很多科研人员头疼的硬伤：审美。

科研工作者往往具备顶级的逻辑思维，知道图里该有什么，但未必懂得如何让它“好看”。布局怎么摆才平衡？配色怎么搭才高级？这些设计层面的短板，往往导致我们画出来的图虽然科学性达标，但视觉效果总觉得差点意思。AI 的介入，相当于给你配备了一位中上水平甚至是顶级的专业设计师。你只负责提供科学逻辑，剩下的配色、光影和排版工作，统统交给它。它能帮助你将一张草图瞬间渲染出顶级期刊封面的质感，让你不再因为配图质量而处于劣势。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94831_0.jpg)

图 1-5 生物医学领域插图风格 ^([4])

当然，要让科研工作者真正放心地将绘图任务交付给 AI，前提是工具必须具备极高的可信度与精确性。得益于两次关键的技术突破，现在的 AI 绘图终于具备了这种“科研级”的可行性。

首先是 **LLM 语义理解与空间逻辑的质变。** 早期 AI 往往只能通过简单的关键词“概率性猜测”画面，容易导致结构崩坏或逻辑错乱。而新一代模型背后都有强大的大语言模型（LLM）作为支撑，这让 AI 从“画师”进化为了“工程师”——它开始具备复杂的因果推理能力。当你输入“A 包含 B，且 B 指向 C”时，高级 AI 不再是元素的随机堆砌，而是像一位严谨的理工科学生，先构建准确的逻辑拓扑，再进行视觉渲染。这意味着，我们终于可以用自然语言实现对图像结构的精确控制。

更关键的突破在于 **文字渲染能力的成熟。** 这为 AI 科研绘图补上了最后一块拼图。过去，AI 极不擅长处理画面中的字符，总是生成一堆不可读的乱码，导致我们生成的图表必须去 PS 里进行繁琐的二次加工。而现在，像 Z-Image、Nano Banana Pro 等先进模型已经具备了原生的文字理解与生成能力。它们不仅能实现图文的完美融合，甚至被用户直接用来生成电影海报、带有精准对白的四格漫画，乃至复杂的项目架构图和带标注的科研图表。

最后，为什么 Nano Banana Pro 是科研场景的首选？

市面上 AI 工具众多，Midjourney 侧重艺术美感但难以精确控制，Stable Diffusion 虽然强大但上手门槛较高。Nano Banana Pro 之所以脱颖而出，是因为它具备独特的“理工科逻辑思维”。它不仅能精准解析晦涩的科研术语与复杂的空间指令，更遵循事实优先原则，极好地克制了纯艺术类 AI 那种天马行空的随意发挥（幻觉）。这使得它在处理拓扑结构、生化通路及流程图等高逻辑密度的图像时游刃有余，而这正是科研示意图的主战场。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 1.4
小结

本章为我们的科研绘图实践建立起了坚实的认知基础。我们重新厘清了科研插图的本质与边界，明确了 AI 在定性示意图中的核心发力点。同时，我们探讨了决定一张“好图”的科学性、逻辑性与艺术性三大维度，确立了“克制与秩序”的科研美学法则，并指明了针对不同学科进行视觉风格定向适配的路径。最后，我们论证了引入以 Nano Banana Pro 为代表的 AI 绘图模型在科研工作流中的必要性与革命性优势。为了帮助你更好地消化并内化这些底层理念，图 1-6 将本章的核心认知体系整理为了一张全景式的知识脉络图。

认知决定高度，而工具决定效率。在建立了正确的绘图理念与审美标准后，第二章我们将正式进入“工具篇”，为你系统拆解本书的核心生成模型及其配套的上下游工具链，为后续的实战演练打造最坚实的基础。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94832_0.jpg)

图 1-6 第一章知识脉络图 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

# 第二章
工具篇：Nano Banana Pro 极速上手

在第一章中，我们已经建立了关于科研绘图的认知基础，厘清了科研插图的主要类型，并明确了什么样的图才称得上是一张合格甚至优秀的科研绘图。同时，我们也论证了在当前技术条件下，引入 AI，尤其是将其用于定性示意图这一高频、高消耗环节，具有现实且可行的价值。

从本章开始，重心将从理念转向工具。本章的目标在于帮助你快速理解并上手以 Nano Banana Pro 为代表的核心模型，并补齐在完整科研绘图流程中，生成前准备与生成后处理阶段可能会用到的关键工具，为后续学习方法论与动手实践做好铺垫。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 2.1
模型接入方式与前沿拓展

在 Google 官方体系中，Nano Banana Pro 对应的模型名称为 gemini-3-pro-image-preview。围绕该模型，目前存在多种不同形态的使用方式，它们共享同一底层模型能力。

从使用渠道的角度来看，Nano Banana Pro 的接入方式可以归纳为官方渠道和第三方渠道两种。其中，官方渠道内部又包含 API、可视化实验平台与网页端产品等不同入口。需要说明的是，这几种方式并非相互替代关系，而是同一模型的不同使用入口。它们在是否需要编程、参数控制粒度以及适用阶段上存在差异，但都可以作为科研绘图流程中的有效工具。理解这些方式之间的定位差异，会有助于你在实际使用中选择更合适的接入路径。此外，考虑到中文语义理解与接入便利性的独特优势，我们也推荐将 Qwen-image-2.0 作为一种具备互补价值的本土化选择（详见 2.1.3 节）。

### 2.1.1 官方渠道

官方渠道是模型能力最直接的来源，也是所有第三方平台和中转服务的底层基础。在官方渠道内，Nano Banana Pro 提供了多种使用方式，分别面向不同程度的控制需求与使用场景。表 2-1 整理了官方渠道内 Nano Banana Pro 三种典型方式的优缺点及适用对象。

表 2-1 官方渠道内 Nano Banana Pro 三种典型使用方式的优缺点及适用对象

![](img/AI科研绘图：Nano Banana极速实战指南_content_94834_0.jpg)

#### 1\. 官方 API

使用官方 API，是最标准、最底层的使用方式。完整流程通常是通过 Google 官方平台 Google AI Studio 申请访问权限，获取 API Key，并为项目配置计费账户。完成上述步骤后，即可参考官方文档在代码中直接调用图像生成接口。

它的核心优势在于模型版本更新最及时，规则最清晰。这种方式非常适合具备一定编程基础、希望将绘图能力纳入可复用科研工作流的科研人员。你可以将图像生成功能集成到自己的 Python 脚本中，实现批量化生成或构建可复用的科研绘图工作流。实际上，市面上绝大多数第三方工具，其底层调用的正是这一接口。

#### 2\. Google AI Studio

Google AI Studio 是 Google 提供的官方可视化实验与调试平台。如果你不想写代码，但又希望获得比普通网页端更丰富的参数控制权，Google AI Studio 是最佳选择。在 AI Studio 的 Playground 界面中，在选择了 Nano Banana Pro 模型后，可以通过交互式界面直接输入提示词并生成图片。图 2-1 展示了 Google AI Studio 的使用界面。

需要注意的是，这种方式并非免费使用。其底层依然依赖来自 Google AI Studio 的 API Key，并要求项目已配置计费账户，与官方 API 调用在计费层面保持一致。相比于写代码，它允许你通过图形界面直接调整尺寸、分辨率等参数，更适合用于在项目正式工程化之前探索模型能力边界、调试复杂提示词结构。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94834_1.jpg)

图 2-1 Google AI Studio 的使用界面

#### 3\. Gemini 网页端

对于希望以最低使用成本快速体验 Nano Banana Pro 生成能力的科研人员来说，Gemini 网页端提供了最直接的入口。

需要特别注意的是，在目前的 Gemini 网页界面中，直接通过对话生成的图像默认由最新（截至 2026 年 3 月）的 Nano Banana 2 模型（官方名称为 Gemini 3 Flash Image）提供支持。使用 Pro 模型，流程分为两步：首先，在“工具”中选择“制作图片”并通过对话窗口输入提示词生成初始图片；随后，点击生成图片下方的“更多”图标（三个点），并选择“使用 Pro 重新生成”，即可调用 Nano Banana Pro 获得细节更丰富的科学插图。图 2-2 展示的是 Gemini 网页端中使用 Nano Banana Pro 的具体流程。这一方式的学习成本最低，但在可控性与参数精细度上相对有限。

值得一提的是，默认的 Nano Banana 2 在科研绘图上同样表现不俗，尤其在生成速度、文字的准确渲染上具有独特优势。但在整体视觉质感、复杂细节的严谨度方面，与 Pro 模型仍存在一定差距。你可以根据实际需求灵活选择是否消耗额度使用 Pro 进行重绘。

根据 Gemini 官方帮助文档中关于图片生成与编辑功能的说明，目前的 Google AI 方案决定了你的每日生成额度和图片画质。Gemini 网页端不同 Google AI 方案的权益对比如表 2-2 所示。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94834_2.jpg)

（a）

![](img/AI科研绘图：Nano Banana极速实战指南_content_94834_3.jpg)

（b）

图 2-2 Gemini 网页端中使用 Nano Banana Pro 的方式
（a）选择“制作图片”并通过对话窗口输入提示词生成初始图片
（b）点击生成图片下方的“更多”图标，并选择“使用 Pro 重新生成”

表 2-2 Gemini 网页端不同 Google AI 方案的权益对比

![](img/AI科研绘图：Nano Banana极速实战指南_content_94834_4.jpg)

有两点需要说明。一个是上述额度每日重置。由于图像生成需求量较大，具体限制可能随 Google 策略调整而变化。另一个是表 2-2 仅展示与“图像生成”相关的权益差异。不同方案在其余方面亦存在显著区别，具体请以官方最新定价说明为准。

### 2.1.2 第三方渠道

虽然官方渠道提供了最权威的支持，但受限于网络环境稳定性、跨境支付门槛或官方配额策略，许多科研人员在实际操作中面临障碍。为了解决这些“最后一公里”的问题，市场上衍生出了多种第三方解决方案。这些渠道在易用性和成本上各有侧重，但也伴随着不同程度的风险。

表 2-3 对比了两种主要的第三方渠道的使用方式，你可以根据自身的技术背景与数据安全需求做出选择。

表 2-3 两种主要的第三方渠道的使用方式对比

![](img/AI科研绘图：Nano Banana极速实战指南_content_94834_5.jpg)

#### 1\. 集成化第三方平台

集成化第三方平台的特点是“开箱即用”。你不需要配置 API Key，也不用担心网络连接，面对的是一个封装好的设计网站。

Lovart 是一个典型例子。如图 2-3 所示，用户在平台中看到的是一键生成的设计流程，其底层可能调用 Nano Banana Pro，也可能根据具体场景切换至其他模型。Higgsfield 以及其他可通过搜索引擎找到的在线生成工具，同样属于这一范畴。

这类平台通常通过自身的账号体系限制每日生成次数。模型具体的参数设置与调用路径对用户而言是不可见的。

本书建议：在选择第三方平台时需特别注意，部分平台会对提示词长度施加较严格的限制。而在生成科研论文插图初稿时，Nano Banana Pro 往往需要较长、结构化程度较高的提示词，这在实际使用中可能构成明显约束。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94834_6.jpg)

图 2-3 Lovart 中使用 Nano Banana Pro 的操作界面

#### 2\. 第三方中转 API

第三方中转 API 是部分熟悉代码的科研人员常用的折中方案。

在这种模式下，用户在代码中配置的不再是 Google 官方地址，而是第三方提供的接口地址。服务商负责将你的请求转发至官方 Nano Banana Pro 接口，并按量计费。从代码层面看，第三方中转 API 的调用方式通常与官方 API 高度一致，仅在接口地址和模型名称上存在差异，具体细节应参考对应服务商的说明文档。

这类中转服务的 API 单价通常低于 Google 官方定价，主要通过批量采购或混合路由策略实现，但其稳定性往往取决于具体的路由策略。

但是必须要注意的是，市面上部分定价极低甚至免费的 API 服务，可能通过逆向工程等非正规手段实现。这不仅意味着服务随时可能中断（稳定性差），更重要的是，你的科研数据（即 Prompt 中的实验设计或创新点）经过了不可控的第三方服务器。

本书建议：在处理尚未发表的敏感科研数据时，谨慎评估此类服务的安全性，并优先选择官方渠道。

### 2.1.3 Qwen-image-2.0 使用渠道

2026 年 2 月，阿里云通义团队正式发布了新一代 AI 绘图模型 Qwen-image-2.0。尽管本书的方法论主要围绕 Nano Banana Pro 展开，但其底层逻辑对于同类高水平模型同样适用。经测试，Qwen-image-2.0 在处理含有特定中文科研术语的提示词时，往往具备更敏锐的捕捉能力，同时也为国内科研人员提供了更稳定的访问体验。

目前你可以通过通义官方网站（https://chat.qwen.ai/）直接使用该模型。在进入网页后，需将模型切换为 Qwen3-Max，在对话框功能区选择“生成图像”，随后输入想要生成的图像内容描述即可开始创作。图 2-4 展示了 Qwen-image-2.0 的使用方式。这种方式无需复杂的配置，适合用于快速验证灵感或在 Nano Banana Pro 无法准确理解中文指令时进行辅助生成。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94834_7.jpg)

（a）

![](img/AI科研绘图：Nano Banana极速实战指南_content_94834_8.jpg)

（b）

![](img/AI科研绘图：Nano Banana极速实战指南_content_94834_9.jpg)

（c）

图 2-4 Qwen-image-2.0 的使用方式
（a）选择模型 （b）选择“生成图像” （c）描述图像内容

本书实战环境说明：引入 Qwen-image-2.0 的初衷，是为国内科研人员提供一个稳定、无障碍且懂中文的优质平替方案。为了保证全书教学逻辑的前后连贯，本书后文的实战案例与提示词模板均以 Nano Banana Pro 作为演示，但本书的方法是跨模型通用的，后续的方法论均可无缝迁移到 Qwen-image-2.0 中，只需将原本的英文指令翻译为对应的中文描述即可，本书后续将不再对双模型进行重复对比演示。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 2.2
Nano Banana Pro 核心功能解构

在了解了如何“打开”这个工具后，下一步便是理解它在科研绘图流程中究竟承担怎样的职能角色。

与以审美驱动为核心的图像生成模型不同，Nano Banana Pro 在科研场景中的定位，更接近一位能够理解结构、约束和因果关系的工程协作者。它并不追求自由发挥，而是擅长在明确规则下完成视觉构建。

这也决定了使用它的核心方式并不复杂。你不需要成为提示词专家，只需要掌握两种足以覆盖绝大多数科研绘图场景的对话模式：结构化生成的文生图，以及引入视觉约束的图生图。

### 2.2.1 文生图

在科研绘图中，最需要警惕的是 AI 的随意发挥。因此，我们需要尽量减少模型的自由想象空间，将其严格约束在明确的目标边界内。当你输入“画一个神经网络”时，许多传统图像模型往往会返回一张充满科技感却逻辑松散的插画。而 Nano Banana Pro 的优势在于，它能够理解“结构化指令”。这一能力正是第三章将重点介绍的“逻辑构建”思维的基础。接下来将介绍文生图的两个技巧。

#### 1\. 像写伪代码一样写提示词

为了实现对图像的精准控制，本书不建议使用散文式的自然语言描述，而是推荐采用一种模块化、分层级的指令组织方式。用户可以显式地向模型声明绘图的关键约束条件，如整体风格、空间布局、拓扑关系。

● **整体风格：** 明确图像更接近哪一类期刊的视觉审美标准，例如“CVPR 偏好的扁平化风格”“Nature 常见的拟真风格”。

● **空间布局：** 定义画面的物理分区与层级关系，例如“左侧为输入层，中间为处理层，右侧为输出层”。

● **拓扑关系：** 明确组件之间的连接方式与方向，例如“箭头 A 从区域 1 指向区域 2，并形成闭环结构”。

这种高度逻辑化的输入方式，可以在很大程度上抑制模型的幻觉行为，确保生成结果在科学逻辑上是自洽的。

此外，需要补充的是，尽管 Nano Banana Pro 具备良好的中文理解能力，但在涉及复杂结构约束与多层逻辑描述时，英文提示词在精确度与稳定性上仍更具优势。

本书建议：在跟随本书使用 Nano Banana Pro 进行实战时，优先使用英文撰写核心绘图指令。如有将图中文字或说明转为中文的需求，可在后期编辑阶段统一处理（若使用前文提及的 Qwen-image-2.0 等中文原生模型，则不受此语言限制）。

#### 2\. 参数化控色

文生图中的另一个关键技巧，是对色彩的参数化控制。科研插图通常对色彩风格有较为严格的隐性规范，例如 Nature、Science 等期刊常见低饱和度、弱对比度的配色方案。仅使用“蓝色”“红色”等模糊描述，往往会得到过于鲜艳、缺乏专业质感的结果。

在 Nano Banana Pro 中，你可以直接在提示词中 **指定 HEX 色值** ，例如 *Primary Color:Azure Blue#E1F5FE* 。模型能够准确解析这些颜色参数，从而生成符合主流顶级期刊审美的图像，有效规避常见的“塑料感”问题。

为了更直观地展示上述两种技巧的效果，本节尝试复刻图 1-3，即多头注意力机制原理示意图。下面对比两种不同提示词方式所生成的结果。图 2-5 展示的是使用较为模糊的自然语言描述生成的效果图；图 2-6 则采用了结构化提示词与参数化控色策略。

图 2-5 提示词：

帮我画一张多头注意力机制的图。最下面是 V、K、Q 三个输入，往上连到三个 Linear 层。中间是一个大的紫色方块，叫 Scaled Dot-Product Attention，要体现出多头堆叠的感觉。上面是一个黄色的 Concat 层，最后是一个 Linear 层。风格要像论文里的那样，扁平一点，不要太花哨。

图 2-6 提示词：

[Layout Constraints]

* Type: Vertical Stack (Bottom-up Flow)

* Background: Pure White (#FFFFFF), No Shadow

[Zone 1: Inputs (Bottom)]

- Objects: Three labels "V", "K", "Q" arranged horizontally

- Connection: Arrows pointing upward to Zone 2

[Zone 2: Linear Projections]

- Shape: 3 small rounded rectangles arranged horizontally

- Label: "Linear" inside each box

- Color: Light Grey (#E0E0E0) with 2px black outline

[Zone 3: The Multi-Head Core (Middle)]

- Main Object: A wide rectangle labeled "Scaled Dot-Product Attention"

- Color: Pastel Purple (#D1C4E9)

- Visual Effect: "Stacked Shadows" behind the rectangle to represent depth (Multi-head)

- Connection: Receiving arrows from Zone 2

[Zone 4: Output Processing (Top)]

- Object A: A rectangle labeled "Concat" (Color: Pastel Yellow #FFF59D)

- Object B: A rectangle labeled "Linear" (Color: Light Grey #E0E0E0)

- Layout: Object B is placed above Object A

[Style Enforcement]

- Aesthetic: CVPR/NeurIPS Academic Style

- Vibe: 2D Flat Vector, Thick Black Outlines, Academic Schematic

通过对比可以发现，图 2-5 仅完成了基本结构的还原，在风格一致性与细节表达上仍存在明显不足；而图 2-6 所使用的提示词，已经在较高程度上复现了目标期刊示意图的视觉语言，整体还原度可达到约 80%。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94835_0.jpg)

图 2-5 自然语言提示词生成图像

![](img/AI科研绘图：Nano Banana极速实战指南_content_94835_1.jpg)

图 2-6 结构化提示词生成图像

最后需要说明的是，不必担心自己无法直接编写出如此细致的提示词。在实际工作流程中，提示词的初稿完全可以借助大语言模型自动生成，第三章将系统介绍相关方法。

### 2.2.2 图生图

尽管文字描述具有很强的表达能力，但在面对复杂空间结构时，例如多层嵌套的模型架构或复杂的分子结构，语言本身往往显得力不从心。

此时，引入参考图将成为决定体验上限的关键。它并不仅仅是一个辅助选项，更是科研绘图从可用迈向好用的重要分界点。具体而言， **参考图主要承担两种核心功能：提供空间拓扑的结构骨架，以及定义视觉特征的目** **标风** **格** 。

#### 1\. 草图即骨架

你并不需要具备专业的绘画能力。无论是用鼠标在画图工具中随手勾勒的线条，在 PPT 中粗略摆放的框架，还是在纸上快速画下的草图，只要其拓扑结构是正确的，即明确了谁与谁相邻、谁指向谁，Nano Banana Pro 就可以将其视为整个画面的“骨架”。

通过上传这样的参考图，原本抽象而冗长的文字描述便获得了清晰的空间约束。模型会保留草图中的结构关系，同时对线条、配色和质感进行系统化优化。总体而言，一张结构正确但外观粗糙的草图，往往胜过冗长且难以精确约束空间关系的文字描述。

同样以复刻图 1-3 为例。上传图 2-7，即手绘草图，并使用如下提示词，就能得到图 2-8 所示的结果。

该例所使用的提示词如下：

[Structural Constraint]

- Preserve all components and connections from Image

- Do not add or remove any elements

[Style & Color Enforcement]

- Visual Style: CVPR / NeurIPS academic schematic

- Rendering: 2D flat vector, clean and minimal

- Background: Pure White (#FFFFFF)

- Color Scheme:

* Inputs and Linear layers: Light Grey (#E0E0E0)

* Attention core: Pastel Purple (#D1C4E9)

* Concat layer: Pastel Yellow (#FFF59D)

![](img/AI科研绘图：Nano Banana极速实战指南_content_94835_2.jpg)

图 2-7 手绘草图

![](img/AI科研绘图：Nano Banana极速实战指南_content_94835_3.jpg)

图 2-8 使用草图生成图像

可以看到，该版本在细节上的还原度明显高于仅依赖结构化文字描述的结果。这组提示词几乎没有涉及逻辑关系的描述，而是专注于配色方案与整体风格的约束。原本需要反复书写的连接关系，被草图以更低成本的方式准确表达出来。

#### 2\. 风格迁移与对齐

除了结构参考，图生图模式还可以用于风格迁移。当你在阅读文献时，遇到配色、线条与整体质感都高度符合目标期刊风格的插图，可以将其作为风格上的参考图提供给模型。

在这种情况下，模型会主动放弃默认的渲染习惯，转而对齐参考图的视觉特征，例如线条粗细、配色方案与整体的扁平化程度，从而快速生成在视觉语言上与目标期刊高度一致的科研插图。

在下面的案例中，我们选择与图 1-3 来自同一篇文章的图 2-9 作为风格参考，并通过相应提示词生成图 2-10。该例所使用的提示词如下：

[Layout Constraints]

* Type: Vertical Stack (Bottom-up Flow)

* Background: Pure White, No Shadow

[Zone 1: Inputs (Bottom)]

- Objects: Three labels "V", "K", "Q" arranged horizontally

- Connection: Arrows pointing upward to Zone 2

[Zone 2: Linear Projections]

- Shape: 3 small rounded rectangles arranged horizontally

- Label: "Linear" inside each box

- Outline: 2px solid outline

[Zone 3: The Multi-Head Core (Middle)]

- Main Object: A wide rectangle labeled "Scaled Dot-Product Attention"

- Visual Effect: Stacked layers to represent multiple heads

- Connection: Receiving arrows from Zone 2

[Zone 4: Output Processing (Top)]

- Object A: A rectangle labeled "Concat"

- Object B: A rectangle labeled "Linear"

- Layout: Object B is placed above Object A

[Rendering Notes]

- Use the provided reference image for visual language only

- Layout and structure must strictly follow the constraints above

![](img/AI科研绘图：Nano Banana极速实战指南_content_94835_4.jpg)

图 2-9 提供的风格参考图[3]

不难发现，在未显式指定扁平化风格与配色方案的前提下，生成结果在整体视觉语言上仍然高度继承了参考图的风格特征。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94835_5.jpg)

图 2-10 风格迁移生成图像

相信你已经注意到了，在上述两个图生图示例中，我们同时运用了前文介绍的文生图技巧。多种技巧的组合使用是完全可行的，但需要注意的是，参考图像与文字指令之间应保持一致，避免在结构或风格层面出现相互冲突的约束。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 2.3
辅助工具推荐

2.2 节已经集中讨论了 Nano Banana Pro 的核心功能，但在真实的科研绘图流程中，图像生成模型只承担其中一个关键环节。要让生成结果真正进入论文、汇报或投稿体系，还需要一组围绕其前后衔接的辅助工具。

这些工具并不以功能复杂取胜，它们的价值主要体现在两个方面：一是降低从想法到图像之间的操作摩擦，二是补齐生成模型在流程边界处的能力空缺。本节将按照科研绘图的时间顺序，分别介绍生成前与生成后常用的几类辅助工具，帮你构建一套完整且顺手的工具链。

### 2.3.1 前处理工具

前处理工具的核心任务，在于模型调用之前，尽可能明确图像的结构、布局与视觉边界，从而减少生成阶段的不确定性。

#### 1\. 草图绘制工具

在 2.2 节介绍图生图方法时已经提到，结构草图作为参考图，在科研绘图中承担着确立空间拓扑与逻辑关系的约束作用。 **用于绘制草图的工具并不存在统一的最优解，真正重要的是能否够快速、明确地表达结构意图** 。

对于大多数科研人员，草图绘制工具并不需要很复杂。无论是操作系统自带的画图软件，PowerPoint 中最基础的形状与箭头工具，还是 Excalidraw、draw.io 这类轻量级流程或示意图工具，都足以承担这一阶段的任务。在很多情况下，甚至只需要一张 A4 纸和一支笔，就能完成对结构关系的初步表达。

我们使用这些工具的目标并非画出精致图像，而是尽快明确图像的空间布局与逻辑关系。只要结构清晰，线条是否规整并不重要。在图生图阶段，模型会将这些草图视为视觉骨架，并在此基础上完成风格与细节层面的优化。

#### 2\. 配色提取工具

配色提取工具主要用于解决另一个常见问题：如何 **快速获得已经在高水平科研插图中被验证过的配色方案** 。

在阅读高水平期刊论文时，我们常常会遇到配色与风格都非常成熟的插图。通过配色提取工具，则可以直接从这些插图中获取对应的 HEX 色值，用于后续的提示词编写或统一整篇论文的视觉风格。

这类工具并非必需，但如果你想复用顶级期刊中常见的低饱和度配色方案，或者需要在多张插图之间保持严格的颜色一致性，这类工具会显著提升你的效率。许多设计软件本身已经内置取色功能。如果你不希望额外安装工具，直接在浏览器中搜索并选取在线的取色工具，同样可以完成这一任务。

### 2.3.2 后处理工具

生成模型输出的图像，通常还无法直接用于正式的科研场景。这类结果往往会存在水印、分辨率不足，或在局部结构与细节上需要进一步修正。因此，后处理工具的作用，在于将 AI 生成的图像逐步整理为可继续编辑、可反复使用的论文插图素材或参考图像，而非简单地对生成结果进行修补。

#### 1\. 去水印工具

部分生成平台会在输出图像中附带水印。在合规使用的前提下，可以借助专门的去水印工具对图像进行处理。

例如，开源项目 gemini-watermark-remover 通过反向 Alpha 混合算法对透明度进行还原，能够在不引入额外视觉噪声的前提下恢复图像的原始结构。此外，一些实用的脚本工具基于 AI Studio 网页端的水印加载机制，通过在图片下载阶段阻止前端水印的加载，从源头避免水印叠加。这一步的目标，是 **尽可能获得一张干净、稳定的基础素材，为后续处理打好基础** 。

需要注意的是，由 Nano Banana Pro 生成的图像不仅包含直观可见的 Google 视觉水印，还嵌入了隐形的 SynthID 标记。SynthID 是一种在图像生成阶段将识别信息编码进像素数据深层的数字指纹技术。根据 Google 官方研究，SynthID 在设计时便充分考虑了鲁棒性，能够有效抵御裁剪、缩放、色彩调整与格式转换等常见编辑操作。

官方论文[5]指出，尽管通过大幅压缩、重采样或风格迁移等极端对抗手段，可以在一定程度上降低水印的可检测性，但这类操作往往伴随着明显的图像质量退化。这也是本书不建议将 Nano Banana Pro 生成的图像直接作为论文终稿插图的重要原因之一。

#### 2\. 高清放大工具

在完成去水印之后，常见的下一步是使用高清放大工具。典型的解决方案是部署 Real-ESRGAN 系列模型（支持本地便携版运行）。这一处理的意义在于 **为后续的矢量化与可编辑化提供更高质量的输入** 。更清晰的边界与细节，有助于提升线条识别、形状分割与文字保留的效果，从而在后续编辑过程中降低整体修改成本。

#### 3\. 矢量化与可编辑化工具

在论文排版或后续修改阶段，插图通常需要进行细节层面的再调整，例如文字标注的位置微调、线条粗细的统一、局部逻辑关系的修正，以及与整篇文稿中其他图形元素的整体协调。表 2-4 整理了 AI 生成图像后的编辑与矢量化方案。

表 2-4 AI 生成图像后的编辑与矢量化方案

![](img/AI科研绘图：Nano Banana极速实战指南_content_94836_0.jpg)

值得注意的是，上述方案并非互斥的选项。在实际的科研绘图工作流中，你可以根据需求灵活组合多种工具，以兼顾效率与精度。例如，可以先使用在线工具（如 Vectorizer）快速完成位图到矢量图的基础转换，再将结果导入专业软件（如 Illustrator）进行精细的节点调整，最后结合代码生成的图形结构进行整体组装。

无论选择单一工具还是混合工作流，其核心目标都是一致的： **让 AI 生成的结果真正融入科研写作流程，而不是停留在“好看的图片”层面** 。无论是通过在线工具的快速转换，还是借助专业软件与代码的深度重构，最终都是为了实现图像的可编辑化与规范化。

在完整的科研绘图工作流中，Nano Banana Pro 提供的是核心生成能力，而这些辅助工具则负责在前后两个方向上“托底”。它们并不决定最终效果的上限，却在很大程度上影响整个流程是否顺畅、可控。理解每一类工具在流程中的位置，远比掌握某一个具体软件更为重要。在第五章中，我们将结合这些工具，详细演示如何将 AI 生成的原始素材转化为符合出版标准的最终插图。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 2.4
小结

本章实现了从理念到工具的跨越，全面解构了以 Nano Banana Pro 为基准的 AI 绘图模型的使用形态与功能边界，并梳理了与其配套的上下游工具链。为了帮助你更直观地复盘本章要点，图 2-11 将本章核心内容整理为了一张全景式的知识脉络图。

工具本身并不是目的，而是实现科研表达的手段。第三章将正式进入实战环节，揭示如何利用 AI 实现从论文内容到可控结构示意图的完整转化方法论。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94837_0.jpg)

图 2-11 第二章知识脉络图 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

# 第三章
从论文内容到可控的结构示意图

第二章已经系统介绍了 Nano Banana Pro 的主要使用方式与配套工具，解决了科研绘图过程中“如何调用模型与工具”的问题。然而在真正进入科研绘图实践后，你会发现，工具层面的熟练程度并不足以保证插图质量。

在实际科研场景中，广大研究者更普遍的困惑在于：对方法本身的逻辑、模块之间的关系和原理演化过程非常清楚，却难以将这些内容转化为一个视觉清晰、结构合理且符合规范的页面布局。即便你已经掌握了文生图或图生图的基本技巧，也依然容易卡在“这张图该怎么组织”“从哪一个模块开始画”这类问题上。

因此，本章将不再零散地讨论单一技巧，而是围绕科研插图生成这一具体目标，把前一章中介绍的工具能力、模型特性与科研绘图的真实需求系统地串联起来，给出一套以论文文本为起点，最终产出可控科研插图素材的完整方法论。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 3.1
核心策略

在科研绘图实践中，文生图或图生图更多承担的是执行层面的角色，其效果高度依赖于一个前提条件：你是否已经对整张图的整体布局与拓扑连接关系做出了清晰决策。当这一前提缺失时，即便对论文内容理解充分，研究者依然很难判断各模块应当如何在二维画面中展开，以及空间位置与层级关系应当如何安排。

在这种情况下，许多初学者会做出一个看似省力、实则风险极高的策略选择：试图将从论文文本到最终插图的全部转换过程直接交由绘图模型完成。

然而，虽然 Nano Banana Pro 具备一定的文本理解能力，本书并不推荐直接将完整论文交由其进行绘图。科研论文本质上是面向同行阅读的线性文本，其中包含大量默认共识与省略信息，并不适合作为绘图模型的直接输入。如果跳过中间环节，直接让模型处理论文内容，往往会出现结构过度简化、模块被错误合并等问题。同时，Nano Banana Pro 直出的图片通常仍需要多轮交互迭代才能满足科研使用标准。

为了解决“人脑构思成本高”与“模型直出不稳定”之间的矛盾，并建立一套可控的科研绘图流程，本书建议将整个绘图过程拆分为三个功能边界清晰的阶段，如表 3-1 所示。

表 3-1 从论文内容到可控的结构示意图的核心方法论

![](img/AI科研绘图：Nano Banana极速实战指南_content_94839_0.jpg)

这套工作流的设计核心在于关注点分离。 **阶段一只解决画什么的问题** ，重点保证结构与逻辑的准确性； **阶段二聚焦如何画** ，强调视觉呈现的稳定性； **阶段三用于处理细节层面的修正与完善** 。

3.2 节~3.4 节将依次对这三个阶段展开说明，并给出对应的操作提示词与实践细节。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 3.2
阶段一：逻辑构建

在阶段一，研究者可借助大语言模型（Large Language Model，LLM），将论文内容转化为一份结构化的[VISUAL SCHEMA]（视觉图式，以下简称 Schema）。该过程并不涉及图像生成，而是要求模型输出一份面向绘图模型使用的结构描述文本，其中需要包含明确的空间布局、模块关系与视觉约束信息。

在这一阶段中，LLM 承担的是“结构设计者”的角色。我们 **利用其较强的语言理解与逻辑推理能力** ，例如 Gemini、GPT、Claude，以及国内的 Kimi、GLM、DeepSeek 等系列旗舰模型， **对论文内容进行重组与抽象，用以明确插图应当呈现的核心信息、模块划分方式以及整体画面设计思路** 。

结合第二章中介绍的文生图技巧，这一步的目标是让 LLM 输出一份可直接用于下一阶段的结构化提示内容，并在其中显式指定插图风格与布局形式。为此，提示词中通常会引入对应领域的顶级会议或期刊作为风格参照，同时提供若干常见的布局原型供模型选择，以降低生成结果的不确定性。

如果你对所要绘制内容的逻辑关系已经非常清楚，可以将现有的文字说明、流程图、伪代码或相关笔记，与论文正文一并提供给 LLM，作为结构构建的输入依据；如果对本领域中常见的插图布局形式缺乏把握，则可以先收集几篇具有代表性的论文插图，让 LLM 对其进行归纳与总结，再将得到的布局类型与设计特征补充进提示词中。这种做法在实践中能够显著提升模型输出结果与领域主流审美之间的一致性。

下面我们以计算机视觉与机器学习领域为例，给出一个完整的逻辑构建提示词示例（读者可直接使用）。

# Role

你是一位 CVPR/NeurIPS 顶会的**视觉架构师**。你的核心能力是将抽象的论文逻辑转化为**具体的、结构化的、几何级的视觉指令**。

# Objective

阅读我提供的论文内容，输出一份 **[VISUAL SCHEMA]**。这份 Schema 将被直接发送给 AI 绘图模型，因此必须使用**强硬的物理描述**。

# Phase 1: Layout Strategy Selector (关键步骤：布局决策)

在生成 Schema 之前，请先分析论文逻辑，从以下**布局原型**中选择最合适的一个（或组合）：

1\. **Linear Pipeline**: 左→右流向 (适合 Data Processing, Encoding-Decoding)。

2\. **Cyclic/Iterative**: 中心包含循环箭头 (适合 Optimization, RL, Feedback Loops)。

3\. **Hierarchical Stack**: 上→下或下→上堆叠 (适合 Multiscale features, Tree structures)。

4\. **Parallel/Dual-Stream**: 上下平行的双流结构 (适合 Multi-modal fusion, Contrastive Learning)。

5\. **Central Hub**: 一个核心模块连接四周组件 (适合 Agent-Environment, Knowledge Graphs)。

# Phase 2: Schema Generation Rules

1\. **Dynamic Zoning**: 根据选择的布局，定义 2-5 个物理区域 (Zones)。不要局限于 3 个。

2\. **Internal Visualization**: 必须定义每个区域内部的“物体” (Icons, Grids, Trees)，禁止使用抽象概念。

3\. **Explicit Connections**: 如果是循环过程，必须明确描述 "Curved arrow looping back from Zone X to Zone Y"。

# Output Format (The Golden Schema)

请严格遵守以下 Markdown 结构输出：

---BEGIN PROMPT---

[Style & Meta-Instructions]

High-fidelity scientific schematic, technical vector illustration, clean white background, distinct boundaries, academic textbook style. High resolution 4k, strictly 2D flat design with subtle isometric elements.

[LAYOUT CONFIGURATION]

* **Selected Layout**: [例如：Cyclic Iterative Process with 3 Nodes]

* **Composition Logic**: [例如：A central triangular feedback loop surrounded by input/output panels]

* **Color Palette**: Professional Pastel (Azure Blue, Slate Grey, Coral Orange, Mint Green).

[ZONE 1: LOCATION - LABEL]

* **Container**: [形状描述, e.g., Top-Left Panel]

* **Visual Structure**: [具体描述, e.g., A stack of documents]

* **Key Text Labels**: "[Text 1]"

[ZONE 2: LOCATION - LABEL]

* **Container**: [形状描述, e.g., Central Circular Engine]

* **Visual Structure**: [具体描述, e.g., A clockwise loop connecting 3 internal modules: A (Gear), B (Graph), C (Filter)]

* **Key Text Labels**: "[Text 2]", "[Text 3]"

[ZONE 3: LOCATION - LABEL]

... (Add Zone 4/5 if necessary based on layout)

[CONNECTIONS]

1\. [描述连接线, e.g., A curved dotted arrow looping from Zone 2 back to Zone 1 labeled "Feedback"]

2\. [描述连接线, e.g., A wide flow arrow from Zone 2 to Zone 3]

---END PROMPT---

# Input Data

[论文相关内容]

这里我们选取“Attention Is All You Need”论文的第三章作为输入素材，得到的结构化输出如图 3-1 所示。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94840_0.jpg)

图 3-1 Transformer 架构的 Schema 生成结果（截选）

本书建议：为避免最终成图与逻辑脱节，建议在进入阶段二前，仔细检查 Schema 中的结构与风格描述是否准确。这一步的校准能显著提高出图成功率。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 3.3
阶段二：绘图渲染

在完成逻辑构建后，流程才进入真正的绘图阶段。此时，LLM 已经提供了一份包含明确布局与风格约束的 Schema，因此本阶段提示词的关注重点也随之发生转移，由“逻辑定义”转向“结构执行”。

在这一阶段，核心目标是 **抑制模型的幻觉行为与过度发挥，确保生成结果尽可能精准地还原 Schema 中定义的空间位置与几何关系** 。绘图提示词应重点强调空间布局、视觉元素类型与整体风格约束。同时，结合具体学科领域的顶级期刊审美标准，对画面的风格进行再次确认。

此外，需要注意的是，Nano Banana Pro 在部分情况下会将结构性提示词中的示意文本误判为需要渲染的画面文字。为避免这一问题，应在提示词中对可渲染文字范围进行明确约束，从而减少后续在文字层面反复修正的成本。

这里我们依然以计算机视觉与机器学习领域为例，给出一个完整的绘图渲染提示词示例。

**Style Reference& Execution Instructions:**

1\. **Art Style (Visio/Illustrator Aesthetic):**

Generate a **professional academic architecture diagram** suitable for a top-tier computer science paper (CVPR/NeurIPS).

* **Visuals:** Flat vector graphics, distinct geometric shapes, clean thin outlines, and soft pastel fills (Azure Blue, Slate Grey, Coral Orange).

* **Layout:** Strictly follow the spatial arrangement defined below.

* **Vibe:** Technical, precise, clean white background. NOT hand-drawn, NOT photorealistic, NOT 3D render, NO shadows/shading.

2\. **CRITICAL TEXT CONSTRAINTS (Read Carefully):**

* **DO NOT render meta-labels:** Do not write words like "ZONE 1", "LAYOUT CONFIGURATION", "Input", "Output", or "Container" inside the image. These are structural instructions for YOU, not text for the image.

* **ONLY render "Key Text Labels":** Only text inside double quotes (e.g., "[Text]") listed under "Key Text Labels" should appear in the diagram.

* **Font:** Use a clean, bold Sans-Serif font (like Roboto or Helvetica) for all labels.

3\. **Visual Schema Execution:**

Translate the following structural blueprint into the final image:

[[VISUAL SCHEMA]的全部内容]

当我们将这些提示词交给 Nano Banana Pro 后，便可以得到一份看得过去的插图初稿了。以图 3-1 中得到的 Transformer 架构 Schema 作为输入，模型的输出结果如图 3-2 所示。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94841_0.jpg)

图 3-2 基于 Schema 生成的 Transformer 架构插图初稿 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 3.4
阶段三：交互迭代

在获得插图初稿后，流程进入交互迭代阶段。尽管整体风格与结构已经基本符合预期，但受限于论文本身的复杂性，Nano Banana Pro 未必能够在一次生成中准确呈现所有细节。例如在图 3-2 中，如果仔细观察细节，会发现右侧出现了“Residual Conction”的拼写错误，且每个残差模块旁都重复堆叠了竖排文字。这种视觉上的冗余不仅导致画面右侧过于拥挤，也干扰了核心信息的传递。因此，对初稿进行有针对性的修改与优化，依然是不可或缺的步骤。

从实践经验来看，常见问题主要可以归为两类：整体布局正确但局部细节或风格存在偏差，以及整体布局本身出现错误。为应对这两类问题，本书推荐的核心原则，是 **尽量减少无目的的重复生成，而是充分利用 Nano Banana Pro 在自然语言编辑方面的能力，对已有结果进行精确修改** 。

#### 1\. 整体布局满意，但细节或风格有瑕疵

当插图的整体结构、模块关系与空间布局已经满足预期，仅在局部元素、配色或视觉风格上存在瑕疵时，建议 **采用自然语言编辑策略进行修正** 。

在 Google AI Studio 或 Gemini 网页端等多轮对话式环境中，若该图片仍在对话的上下文中，你可以直接在对话框输入修改指令（如图 3-3）；若该图片不在对话的上下文中，或在 API 等非交互式调用场景下，则可通过图生图方式，将当前结果作为参考图再次输入模型进行编辑（如图 3-4）。无论采用哪种方式，模型都能够在保持主体结构稳定的前提下，对所指定的元素进行针对性修改。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94842_0.jpg)

图 3-3 在多轮对话式环境中修改图像细节

![](img/AI科研绘图：Nano Banana极速实战指南_content_94842_1.jpg)

（a）

![](img/AI科研绘图：Nano Banana极速实战指南_content_94842_2.jpg)

（b）

图 3-4 利用图生图方式修改图像细节
（a）上传待处理图像；（b）输入修改指令

表 3-2 汇总了科研绘图中常见的自然语言编辑指令示例，涵盖图标替换、颜色调整、风格统一与文字修正等高频需求。蓝色斜体部分为可变参数，需根据实际情况代入。

表 3-2 常见的自然语言编辑指令示例

![](img/AI科研绘图：Nano Banana极速实战指南_content_94842_3.jpg)

需要额外说明的是，尽管 Nano Banana Pro 相较于同类模型在文字渲染能力上已经有明显优势，但在复杂结构或多标签场景下仍可能出现拼写错误或字体异常。如果文字错误较为集中或影响整体观感，更稳妥的处理方式是直接移除图中所有文字标签，在后处理阶段通过矢量编辑或排版软件统一添加说明文字。

本书建议：由于模型的每次生成与修改都会叠加新的水印层，多层堆叠将导致后期难以完美修复。因此，建议参考 2.3.2 节介绍的去水印工具，在每一次生成或修改完成后，立即去除当次产生的可见水印，避免累积。

#### 2\. 整体布局错误

如果发现生成结果在结构层面出现明显偏差，例如原本应为循环关系的流程被绘制为线性结构，或关键模块之间的因果关系被错误表达，此时不建议继续通过局部修改进行修补。

这类问题通常意味着阶段一生成的 Schema 在结构描述上存在歧义或信息缺失。更有效的做法是 **回到阶段一，对 Schema 进行检查与修订，重点确认是否选择了合适的布局策略** ，即 3.2 节中 Schema 输出的 *[LAYOUT CONFIGURATION]* 部分，以及各个 Zone 中对模块关系与空间组织的描述是否足够明确。

在实际操作中，你可以直接与 LLM 进行交互，要求它基于你的修正意见重新生成 Schema 作为新的结构蓝图，再重新执行阶段二的绘图渲染流程。这种方式往往能够从根本上解决结构性错误问题。

#### 3\. 关于多次生成与抽卡策略的有效性说明

在 AI 绘图实践中，使用同一提示词生成多张结果以进行筛选，是一种常见操作。需要说明的是，Nano Banana Pro 在图像编辑与结构保持方面已经具备较高稳定性。 **当一张插图在整体结构和风格层面已经达到较高完成度时，并不建议频繁重新生成** 。

在本书所推荐的提示词体系下，整体布局与视觉风格已经被较为严格地约束，多次生成之间的差异通常集中在线条路径、局部元素配色或细节处理层面，而不会对整体结构产生显著影响。如果你已经预先确定了明确的配色方案或风格规范，更高效的方式是基于当前结果，通过自然语言指令对局部细节进行定点修改。

图 3-5 展示了在相同提示词条件下生成的两张插图结果，可以看到二者在整体布局和视觉语言上的差异并不显著。因此在实际工作流程中，结构性或幅度较大的调整更适合通过优化阶段一的提示词来完成；而细节层面的微调则可以直接通过自然语言编辑完成。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94842_4.jpg)

（a）

![](img/AI科研绘图：Nano Banana极速实战指南_content_94842_5.jpg)

（b）

图 3-5 BERT 模型架构示意图。
（a）论文中给出的第一种架构绘制结果；
（b）在相同提示词条件下生成的另一种结果 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 3.5
小结

本章围绕科研绘图的实际需求，系统介绍了一套从论文内容到可控结构示意图的完整方法流程。通过将逻辑构建、绘图渲染与交互迭代分阶段处理，可以有效降低科研插图生成过程中的不确定性，并提升整体效率。为了帮助你更直观地复盘本章要点，图 3-6 将本章核心内容整理为了一张全景式的知识脉络图。

需要再次强调的是，本章所产出的成果定位于 AI 生成的科研插图素材，仍然处于可编辑、可优化的中间阶段。如何将这些素材进一步提升至可直接用于投稿的发表级插图，将在第五章中结合后处理工具与实践经验展开详细讨论。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94843_0.jpg)

图 3-6 第三章知识脉络图 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

# 第四章
实战篇：不同领域的提示词策略

在第三章中，我们建立了从论文内容到可控结构示意图，再到最终渲染的完整工作流，并以计算机视觉与机器学习领域为例，展示了如何通过逻辑构建与绘图渲染两个阶段的提示词实现高效产出。

然而，不同学科的视觉语言差异巨大：计算机领域偏向抽象的拓扑结构，而材料化学则关注微观实体与能带机制，生物医学更强调复杂的有机环境与信号通路。在本章中，我们将视野扩展至材料与化学、生物与医学两大核心领域，结合其学科特点拆解提示词设计逻辑。此外，针对本书无法穷尽的其他细分领域，我们将在本章最后提供一套基于参考图的提示词定制方法，助你构建专属的提示词体系。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 4.1
材料与化学：微观机制的具象化

材料与化学领域的视觉表达具有高度的物理实体依赖性。在 Nature Materials、JACS（Journal of the American Chemical Society）、Angewandte Chemie 等顶级期刊中，插图是微观世界运行规律的直观展现，其风格通常呈现出“多尺度融合”与“结构决定性质”的特征。典型的风格流派如表 4-1 所示。

表 4-1 材料与化学领域插图的典型风格流派

![](img/AI科研绘图：Nano Banana极速实战指南_content_94845_0.jpg)

针对上述特征，我们需要在逻辑构建阶段引导 AI 从材料视角思考布局，并在渲染阶段重点强化空间区划的逻辑性、微观结构的视觉占位以及反应流向的矢量清晰度。

以下是专为材料与化学领域定制的提示词模板，包含逻辑构建与绘图渲染两个阶段。

#### 1\. 逻辑构建

# Role

你是一位 Nature Materials / Advanced Materials 风格的**科学可视化架构师**。你的核心能力是将材料与化学论文中的结构机制与反应路径，转化为**具体的、结构化的、物理可实现的视觉指令**。

# Objective

阅读我提供的材料或化学论文内容，输出一份 **[VISUAL SCHEMA]**。这份 Schema 将被直接发送给 AI 绘图模型，因此必须使用**严格的物理结构描述与空间指令**。

# Phase 1: Layout Strategy Selector （关键步骤：布局决策）

在生成 Schema 之前，请分析材料体系与机制逻辑，从以下**材料科学布局原型**中选择最合适的一个（或组合）：

1\. **Reaction Pathway Linear Flow**: 左→右反应路径（适合电化学反应、催化机理、相转变过程）。

2\. **Solvation or Coordination Cyclic Model**: 中心为配位或溶剂化结构，周围为离子分布。

3\. **Hierarchical Multiscale Structure**: 宏观器件 → 微观结构 → 原子级结构的垂直堆叠。

4\. **Parallel Material Comparison**: 左右或上下对比不同材料体系或不同浓度条件。

5\. **Core–Shell / Interface Hub Model**: 中心为纳米颗粒或晶体核心，外层为壳层或界面结构。

# Phase 2: Schema Generation Rules

1\. **Dynamic Zoning**

根据选择的布局定义 2–5 个物理区域（Zones）。

每个区域必须具有明确空间位置，例如 Left Panel、Central Core、Right Interface。

2\. **Internal Visualization**

每个区域必须包含具体材料结构对象，例如：

* 球棍分子模型

* 八面体或四面体晶体单元

* 分层石墨片结构

* 半透明溶剂化壳层

禁止使用抽象词汇如"Module"或"System"。

3\. **Explicit Connections**

必须明确离子迁移、电荷流向或反应方向。

使用明确的箭头指令，例如：

"A solid arrow indicating Li+ diffusion from Zone 1 to Zone 2"

"A curved arrow indicating redox cycle around Fe center"

# Output Format (The Golden Schema)

---BEGIN PROMPT---

[Style & Meta-Instructions]

High-fidelity materials science schematic, professional academic illustration for Nature Materials. Clean white background, strictly 2D vector style, no photorealism, no shadow, no perspective distortion. Subtle isometric alignment allowed only for crystal lattices.

[LAYOUT CONFIGURATION]

* **Selected Layout**: [例如：Parallel Material Comparison with 4 Zones]

* **Composition Logic**: [例如：Two material systems placed left and right with central reaction pathway]

* **Color Palette**: Low-saturation scientific palette (Mint Green for Li+, Amber Yellow for Cl-, Slate Grey for carbon framework, Soft Purple for transition metal centers).

[ZONE 1: LOCATION - LABEL]

* **Container**: [形状描述, e.g., Left Rectangular Panel]

* **Visual Structure**: [例如：A crystalline lattice composed of repeating MO6 octahedra in a grid array]

* **Key Text Labels**: "[Material Name]"

[ZONE 2: LOCATION - LABEL]

* **Container**: [形状描述, e.g., Central Circular Region]

* **Visual Structure**: [例如：A core–shell nanoparticle with inner crystalline core and semi-transparent hydrated shell]

* **Key Text Labels**: "[Process Name]", "[Ion Species]"

[ZONE 3: LOCATION - LABEL]

...

[CONNECTIONS]

1\. A solid directional arrow from Zone 1 to Zone 2 labeled "[Ion Diffusion]"

2\. A curved arrow around central metal atom labeled "[Redox Cycle]"

---END PROMPT---

# Input Data

[在此处粘贴你的论文内容]

#### 2\. 绘图渲染

**Style Reference & Execution Instructions:**

1\. **Art Style (Nature Materials / Advanced Energy Materials Aesthetic):**

Generate a **professional materials science mechanism schematic** suitable for a top-tier materials or chemistry journal.

* **Visuals:** Strict flat vector illustration, clean geometric shapes, molecular ball-and-stick models, crystal lattice arrays, thin outlines, soft pastel scientific color coding.

* **Layout:** Strictly follow the spatial arrangement defined in the provided VISUAL SCHEMA.

* **Vibe:** Precise, structural, mechanism-oriented, white background. No photorealism, no shadows, no depth simulation, no perspective distortion.

2\. **CRITICAL TEXT CONSTRAINTS:**

* Do NOT render structural meta-instructions such as "ZONE", "LAYOUT", or "Container".

* Only render text that appears inside double quotes under "Key Text Labels".

* Use clean bold Sans-Serif font suitable for scientific figures.

3\. **Scientific Visual Conventions Enforcement:**

* Ions must be rendered as solid colored spheres with clear element distinction.

* Crystal lattices must appear as periodic repeating geometric units.

* Solvation shells must be semi-transparent circular envelopes surrounding ions.

* Interfaces must be represented as flat planar boundaries.

* Reaction arrows must be clear, directional, and physically interpretable.

4\. **Visual Schema Execution:**

Translate the following structural blueprint into a final publication-ready materials science schematic:

[在此处直接粘贴 Step 1 生成的 ---BEGIN PROMPT--- ... ---END PROMPT--- 内容（包含方括号内的英文）]

图 4-1 展示了利用上述提示词处理文章“Water-in-salt electrolyte enables high-voltage aqueous lithium-ion chemistries”的实战效果。AI 成功识别了文中关于溶剂化结构与 SEI 膜形成的关键逻辑，并生成了直观示意图。

需要特别说明的是，当前 AI 绘图模型在分子拓扑结构、晶体构型及键长比例等方面的准确性仍无法完全保证。为确保插图的严谨性，在实际科研绘图流程中，本书建议将精确的分子式与晶体结构通过 ChemDraw、VESTA 等专业软件单独生成，并在 5.3 节所述的矢量化阶段进行替换与校正。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94845_1.jpg)

图 4-1 基于 Schema 生成的高浓度水系电解液微观与界面机制示意图 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 4.2
生物与医学：复杂通路的场景化

生物与医学领域的顶级期刊以 Cell，Nature，Science 为代表，其视觉语言极其强调微观环境与通路逻辑。与材料科学不同，生物与医学领域的插图通常需要处理柔性的、非规则的有机形态，并清晰地划分细胞内外的空间界限。典型的风格流派如表 4-2 所示。

表 4-2 生物与医学领域插图的典型风格流派

![](img/AI科研绘图：Nano Banana极速实战指南_content_94846_0.jpg)

以下是专为生物与医学领域应用广泛的 BioRender 风格定制的提示词模板，包含逻辑构建与绘图渲染两个阶段。

#### 1\. 逻辑构建

# Role

你是一位 Nature/Cell/Science 顶刊的**资深医学插画师（Medical Illustrator）**。你的核心能力是将复杂的生物医学机制、临床试验设计或分子通路转化为**直观的、符合生物学特征的、出版级视觉指令**。

# Objective

阅读我提供的论文/摘要内容，输出一份 **[VISUAL SCHEMA]**。这份 Schema 将被直接发送给 AI 绘图模型，因此必须使用**精确的生物实体描述**（而非抽象几何形状）。

# Phase 1: Layout Strategy Selector (关键步骤：布局决策)

在生成 Schema 之前，请先分析论文逻辑，从以下**生物医学布局原型**中选择最合适的一个（或组合）：

1\. **Signaling Pathway (Linear/Cascade)**: 上→下或左→右流向 (适合信号转导、代谢通路、药物作用机制)。

2\. **Cyclic/Regulatory Loop**: 中心包含循环结构 (适合细胞周期、负反馈调节、病毒复制周期)。

3\. **Anatomical/Spatial Zoom**: 包含宏观到微观的视觉引导 (适合从器官→组织→细胞→分子的跨尺度展示)。

4\. **Comparative/Parallel Groups**: 平行的对照结构 (适合 Case-control study, Wild-type vs Mutant, 治疗组 vs 对照组)。

5\. **Interaction Network**: 核心分子连接周围多靶点 (适合 PPI 网络、多器官相互作用)。

# Phase 2: Schema Generation Rules

1\. **Biological Context**: 必须定义背景环境 (Context)，例如：细胞质基质 (Cytosol)、细胞核内 (Nucleus)、突触间隙 (Synaptic cleft) 或培养皿 (Petri dish)。

2\. **Entity Materialization**: 禁止使用抽象方块代表生物体。必须描述具体形态，例如：

- *抽象概念* -> *视觉实体*

- Gene -> Double Helix segment

- Protein -> 3D folded structure / Surface representation

- Cell -> Lipid bilayer sphere with receptors

3\. **Bio-Semantics in Connections**: 箭头的含义必须明确：

- Arrow tip ($\rightarrow$) = 促进/激活 (Activation)

- Flat tip ($\dashv$) = 抑制/阻断 (Inhibition)

- Dotted arrow = 易位/运输 (Translocation/Secretion)

# Output Format (The Golden Schema)

请严格遵守以下 Markdown 结构输出：

---BEGIN PROMPT---

[Style & Meta-Instructions]

High-fidelity scientific illustration, BioRender style, 3D semi-realistic rendering, smooth lighting, organic textures. Clean white background. High resolution 4k. Distinct cellular compartments.

[LAYOUT CONFIGURATION]

- **Selected Layout**: [例如：Signaling Pathway with Nuclear Translocation]

- **Composition Logic**: [例如：Split composition: Top half represents the Cell Membrane, Bottom half represents the Nucleus]

- **Color Palette**: Biomimetic & Distinct (e.g., Lipid Blue, Protein Red, Cytosol Beige, DNA Purple). Focus on contrast for key molecules.

[ZONE 1: LOCATION - CONTEXT]

- **Container**: [环境描述, e.g., Extracellular Space & Lipid Bilayer]

- **Visual Structure**: [具体实体, e.g., A cross-section of a phospholipid bilayer with embedded Y-shaped transmembrane receptors]

- **Key Text Labels**: "[Ligand Name]", "[Receptor Name]"

[ZONE 2: LOCATION - CONTEXT]

- **Container**: [环境描述, e.g., Cytoplasm (Intracellular)]

- **Visual Structure**: [具体实体, e.g., A complex of globular proteins showing phosphorylation sites (small glowing yellow dots)]

- **Key Text Labels**: "[Protein A]", "[Protein B-PO4]"

[ZONE 3: LOCATION - CONTEXT]

... (Add Zone 4/5 if necessary, e.g., Nucleus)

[CONNECTIONS & INTERACTIONS]

1\. [描述反应过程, e.g., A glowing arrow from the Receptor (Zone 1) to Protein A (Zone 2) indicating signal activation]

2\. [描述抑制关系, e.g., A red line with a flat head extending from Drug X to Protein B indicating inhibition]

3\. [描述空间移动, e.g., A dotted swooping arrow showing Protein B moving into the Nucleus (Zone 3)]

---END PROMPT---

# Input Data

[在此处粘贴你的论文内容]

#### 2\. 绘图渲染

**Style Reference & Execution Instructions:**

1\. **Art Style (BioRender/Medical Illustration):**

Generate a **standard biological pathway diagram** suitable for Cell/Nature.

* **Visuals:** Clean vector-like illustrations with **smooth gradients**.

* **Shapes:** Rounded, organic forms (soft edges), not sharp geometric blocks.

* **Color Palette:** Professional biological pastels (Membrane Beige, DNA Blue, Cytoplasm Pink).

* **Vibe:** Educational, clear, textbook-standard.

2\. **CRITICAL TEXT CONSTRAINTS:**

* **Legibility:** Labels must be clear and dark on light backgrounds.

* **Font:** Arial or Roboto (Bold).

3\. **Visual Schema Execution:**

Translate the following structural blueprint into the final image:

[在此处直接粘贴 Step 1 生成的 ---BEGIN PROMPT--- ... ---END PROMPT--- 内容]

图 4-2 展示了应用上述提示词解析文章“Ferroptosis: a regulated cell death nexus linking metabolism”的结果，清晰地呈现了与铁死亡（Ferroptosis）相关的代谢通路与细胞器间的生化诱导机制。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94846_1.jpg)

图 4-2 基于 Schema 生成的铁死亡诱导机制插图 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 4.3
基于参考图的提示词定制法

尽管前文覆盖了计算机、材料、生物三大领域，但科学研究的细分领域浩如烟海，单一的模板无法穷尽所有场景。面对这一挑战，你或许会联想到第二章介绍的图生图的技巧。然而在科研绘图中，单纯的风格迁移往往难以精确维持复杂的拓扑逻辑，而利用手绘草图进行控制又对科研工作者的布局能力提出了较高要求。为了解决这一矛盾，我们必须回归到第三章建立的“逻辑构建+绘图渲染”工作流。

在此基础之上，我们可以充分利用当前多模态大模型卓越的图像理解能力，让 AI 深度解析目标领域的参考图像，提取其潜在的布局逻辑与视觉风格。具体的解决路径是 **让 AI 基于我们提供的参考图进行布局与风格层面的逆向分析，并结合目标领域的知识表达范式，对既有的基础提示词进行针对性微调** 。通过这种方式，我们能够快速得到一套兼具参考图视觉特征与目标领域逻辑的定制化提示词。

该方法的优势在于极高的精确度：它有效解决了仅使用宽泛文字描述带来的模糊感，确保生成结果在布局与风格上与参考图高度一致。同时，该方法具备极强的普适性，几乎适用于任何学科领域。为了帮助你更好地掌握这一技巧，下面提供了一个以第三章计算机领域提示词为基础进行微调的示例供参考使用。

图 4-3 展示了这份提示词的使用方式和输出示例。

你是一名资深提示词工程专家，熟悉学术论文插图的生成逻辑，对计算机领域以及【你所在的领域名称】领域的研究范式、图示风格与视觉表达均有系统理解。

我将提供一个目标领域插图的成品示例。该示例代表我希望最终生成结果所遵循的整体风格。请你对该示例进行逆向分析，重点关注以下方面：整体布局结构、信息层级组织方式、模块之间的空间关系、配色方案及其在信息表达中的作用、图形元素的抽象程度与表达习惯。

在此基础上，请对下方给定的两个提示词分别进行微调优化，使其在实际使用时，能够稳定生成与示例在视觉风格与表达逻辑上高度一致的插图提示词。

需要注意的是，这两条原始提示词均是为计算机领域论文内容抽取与示意图绘制所设计的。你的任务是将其调整为适用于【你所在的领域名称】的版本。请保持原有提示词的整体结构、步骤逻辑和控制维度，仅结合【你所在的领域名称】常见的图示布局特征、学科语义重点以及视觉表达习惯进行针对性的细化和替换。

最终输出应为两条对应的完整、可直接使用的提示词，其生成结果在风格上与目标示例保持一致，同时在内容表达上自然适配【你所在的领域名称】。

---

提示词 A：

[3.2 节逻辑构建阶段的提示词示例]

---

提示词 B：

[3.3 节绘图渲染阶段的提示词示例]

![](img/AI科研绘图：Nano Banana极速实战指南_content_94847_0.jpg)

（a）

![](img/AI科研绘图：Nano Banana极速实战指南_content_94847_1.jpg)

（b）

图 4-3 基于参考图的提示词定制流程
（a）输入目标领域的参考图；
（b）AI 分析后生成的提示词（截选） EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 4.4
小结

本章主要聚焦于不同领域的逻辑构建和绘图渲染提示词的构建方法。特别需要提醒的是，当你得到自己领域的逻辑构建和绘图渲染提示词后，依然要回到 3.4 节介绍的阶段三：交互迭代。即使是定制化的提示词，AI 生成的初稿也往往存在细节上的偏差，你需要利用这一阶段的方法，对初稿中暴露的明显错误进行识别与修正。为了帮助你更直观地复盘本章要点，图 4-4 将本章核心内容整理为了一张全景式的知识脉络图。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94848_0.jpg)

图 4-4 第四章知识脉络图

至此，相信你已经掌握了如何使用 AI 得到一个布局合理且风格美观的初稿。但这通常只是一个起点，还无法直接满足科研论文的严谨要求。在第五章中，我们将进一步走进科研绘图的精准控制与体系化构建阶段，实现从“可看级”到“可用级”的跨越。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

# 第五章
进阶篇：科研绘图的精准控制与体系化构建

在第 2~4 章中，我们已经系统学习了如何借助生成模型与相关工具，高效获得结构清晰、语义正确的科研插图初稿。从工具认知，到完整工作流的搭建，再到具体领域示例，相信你已经具备了独立完成科研插图生成的基本能力。

但在真实的科研写作与投稿过程中，仅仅“生成一张看起来不错的图”，还远远不够。随着论文反复修改、审稿意见不断往返，插图需要被频繁调整、扩展，甚至在不同论文和汇报中被再次使用。如果生成结果无法被持续编辑和稳定复用，其初期带来的效率优势很快就会被后期的维护成本抵消。

因此，本章致力于构建一套高标准的科研绘图体系。本章将不再局限于追求单次生成，而是深入探讨如何突破提示词的边界，通过精确控制、模块化拆解与标准化处理，将 AI 的随机产出转化为严谨、合规且可长期复用的科研资产。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 5.1
重新建立科研绘图的主动权

生成模型在科研绘图中的最大优势，在于能够在极短时间内给出一个结构完整、信息充分的视觉方案。但与此同时，这类结果也往往伴随着一个容易被忽视的隐患，即对后续修改缺乏足够的控制力。

在初稿阶段，这一问题并不明显。但随着使用场景从“生成初稿”进入“反复修改”，许多隐藏的问题会逐渐显现——模块比例难以精调，局部结构无法单独调整，多张插图之间的风格也容易出现偏差。这些问题在论文修改阶段会被不断放大，往往会显著拉高后续维护成本。

因此，在生成完成之后，你需要做的第一件事，并非继续追求画面细节，而是重新接管绘图过程的控制权。这一控制权主要体现在两个层面：一是对生成指令本身的再加工，二是通过更直接的方式约束模型的视觉理解。

### 5.1.1 指令层的结构控制

第三章曾建议，在进入阶段二前，应当仔细检查 LLM 生成的 Schema，并在后续交互中根据需要进行修改。之所以要这样做，是因为生成的 Schema 是你最终提供给 Nano Banana Pro 的核心提示词。引入 LLM 的初衷在于帮助研究者从论文文本中快速外化出图像的整体布局与模块关系，尤其是在对整张图的拓扑结构尚未形成清晰认知的阶段。

但当你已经拿到一版可用的初稿之后，情况往往会发生变化。随着对论文内容和插图用途的反复推敲，你对“这张图最终应该长成什么样”会逐渐形成更明确的判断，包括结构取舍、信息层级，乃至整体视觉风格。在这一阶段，继续完全依赖模型自动生成的 Schema，反而可能限制你的表达。更合理的做法，是基于已经建立起来的完整认知，对 Schema 进行有意识的重写和精修，让它真正服务于你的设计意图。

这种重写并不仅限于结构层面的增删与调整，还应当覆盖那些在初始生成中容易被忽略，但在正式科研插图中至关重要的约束条件。其中一个常被低估，却极其关键的维度，就是色彩。

在进阶的科研绘图语境中，色彩不再只是审美层面的自由发挥，它承担着信息区分与语义映射的功能。因此，在重写 Schema 时，建议 **将色彩管理一并纳入提示词设计** 。具体来说，可以从顶级期刊或高质量论文插图中提取稳定、被反复验证过的 HEX 色值，构建一组固定的色盘，并在 Schema 中明确规定各类模块、流程或概念所对应的具体颜色编码，参数化控色详见 2.2.1 节。

这些 HEX 色值可以利用取色工具依次从图中选取。如果你有批量提取文献插图 HEX 色值进行分析的需求，也可以通过开源项目 colorgram.py 本地识别、导出。

这种参数化的色彩约束一旦写入 Schema，便会成为模型生成时必须遵循的硬性条件。它不仅能够显著提升单张插图的专业度，更重要的是，在涉及多张图表或系列图时，可以确保同一概念在不同图中始终保持一致的视觉指代，从而建立起连贯、可信的视觉语言体系。这一步，往往是科研插图从“看起来不错”走向“符合期刊审美规范”的分水岭。

### 5.1.2 视觉层的生成控制

即便经过多轮优化，语言描述本身依然存在边界。对于某些复杂的空间拓扑关系，或对视觉张力要求较高的结构示意，再精细的文字提示也难以完全复刻你脑海中的设想。

在第三章和第四章中，我们主要通过文本来引导生成 Schema。但在实际操作中，你可能会发现模型依然难以理解某些非典型结构，或者在多张插图之间出现风格不统一的问题。在这种情况下，继续在文本框中反复微调形容词，往往收效有限。

此时，更有效的策略是引入更直接的视觉约束手段。结合 2.2 节中介绍的图生图和文生图方法，通过草图和风格参考图，对生成过程进行更明确的引导。

#### 1\. 用草图强制纠偏拓扑结构

在生成初稿的过程中，你可能会遇到模型顽固地将“输入层”放置在错误位置，或者始终无法正确连接两个特定模块的情况。这类问题并非源于提示词不够精准，而更多来自模型在空间理解上的固有倾向。

此时最直接的解法是 **停止与文字指令的纠缠，直接画给它看** 。正如 2.2.2 节中所提到的，你只需在纸上或白板工具（详见 2.3.1 节介绍的草图绘制工具）中快速勾勒出模块的相对位置与连接逻辑，将这张甚至略显潦草的草图作为骨架喂给模型。这类视觉输入在生成中的优先级高于文字描述，能够显著提升模型对拓扑关系的服从度。

#### 2\. 用风格参考图明确期刊级视觉取向

在第三章和第四章中，我们给出了多组基于具体研究领域总结的提示词范式。这些提示词来源于对典型论文插图的归纳，能够在多数情况下生成符合该领域常见使用习惯的示意图。

但需要明确的是，领域层面的常见风格，并不必然等同于某一具体期刊或会议的视觉审美。即便研究主题相同，不同期刊在配色倾向、线条处理和构图密度等方面，往往仍然存在稳定差异，而这些差异很难仅通过抽象文字被准确描述。

因此，当你已经明确目标投稿期刊，或对期刊整体风格有较高要求时，仅依赖通用领域提示词往往不足以获得理想结果。在这种情况下，更有效的做法是直接引入风格参考图，明确期刊层面的视觉取向。

具体而言，可以 **从目标期刊已发表论文中选取一张与当前工作类型接近且视觉效果令人满意的插图，将其作为风格参考输入生成流程** 。通过这种方式，模型能够在笔触密度、配色策略和整体视觉节奏上获得更明确的约束，从而生成更贴近期刊审美的结果。

此外，这一方法并不局限于已有提示词覆盖的领域。如果研究方向较为小众，或不在前述章节所列举的典型范围内，同样可以通过风格参考图反向提炼出适用于该领域的定制化提示词。

灵活运用上述视觉控制手段，将帮助你突破纯文字指令的瓶颈，获得更符合预期且质量稳定的生成结果。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 5.2
复杂长图的模块化思维

第二章中我们已经展示了 Nano Banana Pro 对结构的理解能力，但 AI 在长时记忆和全局空间规划方面仍然存在客观限制。当插图的复杂度逐渐逼近甚至超过单一视图所能承载的范围时，无论是尺寸上的拉伸，还是信息密度的持续叠加，都容易触发模型在逻辑组织与空间布局上的失稳。在这类场景下，试图依靠一条不断膨胀的提示词来一次性完成整张插图的生成，往往难以获得稳定且可复用的结果。

因此，研究者需要主动调整绘图策略，将原本整体性的复杂任务拆解为若干结构明确、边界清晰的生成单元，再由人工完成最终整合与校正。

### 5.2.1 案例分析

通过第 3、4 章的多次实战可以发现，在当前模型能力条件下，确实可以通过一段较为详尽的提示词，让 AI 一次性输出结构看似复杂的长图。但进一步检查后往往会发现，这类结果在宏观层面虽然能够较好地贴合目标风格与主流程设计，但在微观逻辑与关键语义细节上却容易积累隐蔽的问题。

更重要的是，这类问题在后续修改阶段往往并不“友好”。由于结构和元素已经在整体画面中相互嵌套，一旦需要针对某个局部进行校正，常常会牵连到其他区域，使得调整成本显著上升。

以图 5-1 为例，该图采用第三章工作流，以论文“Tree-KG:An expandable knowledge graph construction framework for knowledge-intensive domains”的 Methodology 章节作为输入绘制的结果。该图使用的 Schema 详见附录。初看之下，整张图版式规整，左、中、右三区划分明确，右侧的三维多层级结构也具备一定的视觉完成度。

图 5-1 所使用的 Schema 内容如下：

1\. Global Layout （全局布局）

Structure: Left-to-Right Progressive Pipeline. The flow transitions from rigid structural extraction to dynamic semantic expansion, culminating in a layered topology.

Background: Clean white canvas with a faint, technical isometric grid (light grey, 10% opacity) to support the 3D aspects of the graph layers.

2\. Zone Definition （区域定义 - 核心）

Zone 1 (Location: Left):

Container Label: "INITIAL SKELETON CONSTRUCTION (Sec 3.2)"

Shape: A tall, vertical rectangular panel with a light blue tinted background.

Internal Structure:

Top: A stack of generic "Textbook/Corpora" icons.

Middle: A "Regex Parsing Gear" icon processing the text.

Bottom: A rigid Table-of-Contents (TOC) Tree. This is a strict hierarchical dendrogram showing Book $\to$ Chapter $\to$ Section $\to$ Subsection.

Key Text: "Text Segmentation", "TOC Extraction", "Explicit Relations"

Zone 2 (Location: Center - The Core):

Container Label: "ITERATIVE EXPANSION ENGINE (Sec 3.3)"

Shape: A large central container with a dashed border, representing the "Black Box" processing. It contains three nested sub-modules arranged in a triangular cycle:

Internal Structure:

Sub-module A (Top): "Node Refinement". Visualizes the Contextual Convolution (conv). A central node is surrounded by a halo, absorbing vector lines from neighbor nodes.

Sub-module B (Bottom Left): "Entity Optimization". Visualizes Aggregation (aggr) and Deduplication (dedup). Shows small clusters of nodes merging into single "Core" entities, or "Non-core" entities attaching to a parent.

Sub-module C (Bottom Right): "Link Prediction Logic". Visualizes Eq(1). A dotted line connects two nodes ($u, v$). Floating above are three small icons representing the metrics: a Cosine wave, a Triangle (for Common Neighbors/AA), and a Path icon (for Common Ancestors).

Key Text: "Contextual Convolution", "Edge Scoring (AA+CA)", "Entity Aggregation"

Zone 3 (Location: Right):

Container Label: "TREE-KG TOPOLOGY (Sec 3.1)"

Shape: A clear glass-like 3D container.

Internal Structure:

A Multi-Layered Graph view (Layers $V_1 \dots V_k$).

Vertical Pillars: Thick, solid lines representing Hierarchical Edges ($E_1$) connecting layers.

Horizontal Webs: Thinner, colored lines representing Relational Edges ($E_2$) within the same layer.

Key Text: "Vertical Hierarchy ($E_1$)", "Horizontal Relations ($E_2$)", "Layered Knowledge"

3\. Connection Logic （连接逻辑）

Main Flow: A wide, grey block arrow labeled "Skeleton Initialization" flows from Zone 1 to Zone 2.

Internal Cycle: Inside Zone 2, circular arrows connect the three sub-modules (Refinement $\to$ Optimization $\to$ Prediction) to represent the "Iterative" nature of the expansion.

Output Flow: A double-lined arrow labeled "Structure Integration (merge)" flows from Zone 2 to Zone 3.

![](img/AI科研绘图：Nano Banana极速实战指南_content_94851_0.jpg)

图 5-1 基于长提示词一次性生成的 Tree-KG 框架示意图

很多研究者在得到这样一张图时容易产生“绘图已基本完成”的判断。然而，当我们将其放回科研论文的使用语境中，这类一步生成的高复杂度插图往往会暴露出更深层次的问题，主要体现在信息过载和结构语义与视觉编码不一致两个方面。

#### 1\. 信息过载

图 5-1 中堆叠了大量文字标签，例如 Regex Parsing Gear（正则表达式解析装置）、Contextual Convolution（上下文卷积）等。AI 在生成过程中倾向于尽可能完整地复现提示词中的描述内容，从而将本应通过视觉抽象表达的信息，直接转化为文本元素堆积在画面中。这种做法削弱了插图的视觉归纳能力，使其更像一张结构化笔记，而非真正意义上的科研论文插图。这也与 1.3.1 节中提到的科研美学的通用法则相违背。

#### 2\. 结构语义与视觉编码的不一致

尽管整体布局在形式上较为合理，但在需要严格表达结构语义关系的局部区域，仍然存在明显问题。以图 5-1 右侧的多层级知识图谱结构为例，根据论文原文描述，该部分应展示的是一个随着知识规模扩展，在层内与层间均存在明确连接关系的图谱结构。然而在生成结果中，这些关系更多停留在形式暗示层面，未能通过稳定、可辨识的视觉编码加以明确表达。

具体而言，生成图中层内节点数量与连接关系被弱化甚至模糊化，跨层级实体映射未得到清晰呈现；节点颜色的语义指向缺乏一致定义；同时，“Multi-layered Graph”与“Horizontal Relations”等标注在语义对应关系上也出现了明显偏差。这些问题并非源于单一局部错误，而是复杂结构在整体生成过程中，其结构语义难以被持续、精确地映射到视觉符号体系中所导致的系统性偏移。

为了修正上述信息过载与多层级结构表达失真的问题，按照 5.1 节的方法，可以通过调整 Schema 或引入更严格的视觉层控制来修补。但在这样一张元素高度耦合的复杂插图中，无论是局部修改还是结构性调整，都极易引发连锁反应，使整体稳定性进一步下降。

正因如此，仅依赖事后修正并不足以从根本上解决问题。要获得可控且可维护的复杂插图结果，绘图思路本身需要发生转变。

### 5.2.2 复杂图形的拆解与生成

在科研写作中，真正需要引入模块化策略的复杂图形，主要集中在两类典型形态：复杂长图，以及由多个子图构成的复合型大图。

#### 1\. 复杂长图

这类插图通常用于展示完整算法流程、系统架构或多阶段分析路径，其共同特征是信息链条较长、模块数量较多，并且对上下游关系的表达精度要求较高。在这类场景中，复杂度本身已经超过单一视图稳定承载的上限，图 5-1 所示案例即为复杂长图的典型体现。

更稳妥的做法是 **在逻辑层面先将整张长图拆分为输入、处理、输出等若干相对独立的子模块** 。针对每一个子模块，分别采用 5.1 节中已经验证有效的 Schema 与视觉约束进行生成，只在局部范围内追求结构正确与语义清晰，从而避免复杂度在单次生成中集中爆发。

#### 2\. 由多个子图组成的复合型大图

以图 5-2 所示论文中常见的图 A、B、C 组合形式为例。这类插图在版面上虽然被拆分为多个子图，但在审稿语境中依然被视为一个整体。其难点并不在于结构是否足够复杂，而在于不同子图往往承担着不同层级、不同抽象程度的表达任务。在一张复合型大图中，涵盖应用场景、结构示意、工作原理等子图，在视觉风格上可能天然属于不同类型，例如子图 A 是 2D 抽象示意图、子图 B 是 3D 结构拆解图。在这种情况下，若尝试整体生成，模型往往会在不同风格约束之间做出折中处理，导致每一部分的表现力都被削弱。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94851_1.jpg)

图 5-2 融合不同视觉风格的复合型插图案例[6]

因此，这类复合图更适合 **在生成阶段将每一个子图视为独立模块分别绘制** ，使每个子图都能在自身最合适的视觉范式下完成表达， **再通过统一的风格约束与人工排版完成整体整合** 。

### 5.2.3 基于母图的风格锚定

当复杂图形被拆解为多个模块分别生成后，新的挑战随之出现，那就是如何保证这些局部结果在最终合成时仍然保持高度一致的视觉风格。

在完成第一张令人满意的插图后，后续工作的重点便从“创造”转向了“对齐”。为了确保最终的插图在视觉上具有统一风格，仅依赖“扁平化设计”或某某会议风格这类泛化标签往往是不够的。

更有效的做法，是将你最满意的那张生成结果作为“母图”引入后续生成流程。通过图生图的方式，在生成新的模块或子图时， **要求模型严格参考该母图的笔触密度、配色策略、线条粗细与整体视觉节奏** 。这种基于具体图像特征的风格锚定方式，能够显著降低模块化生成过程中出现风格漂移的风险。

在模块化绘制复杂插图时，母图所起的作用并不只是风格示范，更像是一种项目级的视觉基准。它确保了无论模块如何拆分、生成顺序如何变化，最终拼合出来的整张图，依然能够形成稳定而统一的视觉语言。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 5.3
图像结果的矢量化

在 5.2 节中，我们已经讨论了通过模块化策略来应对复杂科研插图生成的不稳定性问题。无论是复杂长图，还是由多个子图构成的复合型大图，其最终形态往往都需要回到 PPT、Illustrator 等绘图环境中进行人工整合。

从操作层面来看，这一整合过程本身既可以基于位图完成，也可以通过矢量方式进行。因此，是否进行矢量化，并不是一个技术上的必然选择。但是需要强调的是，无论是否使用了模块化策略的 AI 生成的插图，本书都建议进行矢量化，并人工进行修正、调整。这是一个与科研规范、后续使用场景和编辑需求密切相关的决策。

本节将围绕这一决策展开讨论，重点分析在科研写作语境中，为什么矢量化会成为几乎不可回避的一步，以及在此基础上应如何选择合适的实现路径。

### 5.3.1 为什么必须进行矢量化

矢量化的首要动机，并不来自美观或效率，而来自科研规范本身——AI 生成的位图结果，即便在内容上完全正确，但其生成过程依然难以满足论文对“作者实质性参与绘制过程”的要求。通过矢量化所引入的重绘、重构与人工编辑环节，插图从“模型输出结果”转变为“作者主导完成的科研图像”，从而在伦理与署名层面建立清晰边界。

这一重绘过程并不是形式上的规避，而是实质性的再创作。无论是结构线条的重新组织，模块边界的明确划分，还是文字标注的重新排版，矢量化都意味着作者对图像内容进行了可审查、可追溯的人工干预。这正是 AI 辅助绘图能够被期刊接受的关键前提。

在此基础上，矢量化还同时解决了位图插图在科研写作中的一系列现实问题。

首先是清晰度问题。AI 生成的文字元素，即便语义正确，也常以位图形式嵌入画面，在缩放或版式调整后容易出现模糊、变形等情况。通过矢量化重新插入的文字与线条，可以以百分百矢量形式呈现，确保在任意分辨率下的清晰度与一致性。

其次是修改便利性。在论文反复修改过程中，插图往往需要进行大量局部调整，例如模块名称替换、文字位置微调、线条粗细统一等操作。矢量对象可以被独立选取、精确移动和单独编辑，使这些修改成为低成本操作，而不再需要回到生成阶段重新开始。

因此，矢量化在科研绘图流程中同时承担了三重角色，一是合规层面的必要转换，二是清晰度与版式稳定性的保障，三是支撑长期修改与复用的基础条件。

### 5.3.2 矢量化路径

在 2.3.2 节中，我们已经了解了当前常见的矢量化工具类型。但在真实的科研绘图场景中，矢量化并不存在唯一标准路径，其具体实现方式往往取决于研究者对绘图工具的熟悉程度、插图的复杂类型，以及后续修改的频率与深度。

综合实践经验，现阶段较为可行的矢量化路径大致可以分为以下三类。为了帮助你根据具体需求快速定位合适的方案，表 5-1 从核心优势、主要局限及推荐适用场景三个维度，对这三种路径进行了横向对比。

表 5-1 常见矢量化路径特性对比

![](img/AI科研绘图：Nano Banana极速实战指南_content_94852_0.jpg)

#### 1\. 基于位图参考的人工重绘

如果你擅长使用 PPT、Figma、Visio 等工具完成论文插图，最稳妥的方式是将 AI 生成的高清位图作为底图参考，在其上手动重绘关键结构。

这种方法在初期需要一定时间成本，但其优势在于最终得到的图形完全由原生矢量对象构成，层级关系明确，逻辑结构清晰，后续无论是局部修改、整体调整还是跨版本复用，成本都极低。该方式尤其适用于流程图、系统架构图以及逻辑关系明确的示意性插图。

#### 2\. 基于工具的自动描摹

如果你熟悉 Illustrator 等专业绘图软件，可以利用其图像描摹功能，将位图快速转换为矢量路径。这一方法在色彩边界清晰、风格扁平的插图中效率较高，能够显著缩短初始矢量化时间。

以 Illustrator 2025 的操作为例，该功能集中在如图 5-3 所示的“图像描摹”面板。将图片拖入 Illustrator 主界面后，点击右侧面板中的“图像描摹”按钮。首先在预设一栏选择“高保真度图片”以加载系统默认的彩色追踪方案，此时手动修改参数（推荐参数：颜色 20%、路径 50%、边角 30%、杂色 10px），预设会自动切换为“自定”。处理完成后，依次点击“扩展”与“取消编组”，即可对各个矢量对象进行灵活拖动或单独编辑。图 5-4 所示的是图 3-2 基于上述参数的图像描摹结果。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94852_1.jpg)

（a）

![](img/AI科研绘图：Nano Banana极速实战指南_content_94852_2.jpg)

（b）

![](img/AI科研绘图：Nano Banana极速实战指南_content_94852_3.jpg)

（c）

![](img/AI科研绘图：Nano Banana极速实战指南_content_94852_4.jpg)

（d）

图 5-3 Illustrator“图像描摹”面板（a）选择高保真度图片
（b）设置推荐参数（c）点击拓展（d）点击取消编组

![](img/AI科研绘图：Nano Banana极速实战指南_content_94852_5.jpg)

图 5-4 基于 Illustrator 的图像描摹结果

可以看到的是，自动描摹生成的路径往往在结构层面仍然较为粗糙，尤其是对文字部分的识别效果有限，通常仍需经过人工清理与重组，才能满足科研插图对逻辑清晰度和可维护性的要求。

在实际测试中，描摹效果对参数设置和图像风格高度敏感，需要通过反复微调颜色、路径、边角、杂色，才能获得相对可用的结果，也需要一定的反复试验。

若缺乏 Illustrator 环境，也可借助 Vectorizer 等在线工具完成基础矢量化处理，但在精度控制和结构可调性方面通常不及专业软件。

本书建议：在执行自动描摹前，建议先通过高清放大工具（如第二章提及的 Real-ESRGAN 系列模型）将原始位图进行预处理。理论上，输入的位图分辨率越高、边缘锐度越强，自动描摹生成的路径就越平滑且贴合原貌。

#### 3\. 基于 AI 识别的结构化重建

除上述成熟方案外，近年来也出现了一些尝试从结构层面重建科研插图的探索性工具，例如 Edit-Banana、Paper2Any 等。这类工作不再将 AI 生成的插图视为单纯的图像，而是尝试将其通过图像分割模型对输入图像进行实例分割，并引入视觉大模型（Visual Large Model，VLM）、OCR 模型将图片解析为由文本、形状、连接关系构成的结构化对象，再导出为可编辑格式。

需要指出的是，这些工具目前仍处于探索或早期应用阶段，在稳定性和可控性方面尚无法完全替代人工重绘或半自动流程，但它们所代表的方向，值得在未来科研绘图工作流中持续关注。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 5.4
构建你的科研绘图素材库

当你逐渐熟练地掌握第 2~4 章所介绍的绘图流程后，往往会意识到一个现实问题：真正耗时的并非单次作图，而是不断从零开始的重复劳动。将已经验证有效的中间成果系统性地沉淀为可复用的资源，能够显著降低后续科研绘图的边际成本，从而实现效率上的跃迁。因此，本节将从提示词模板、高质量科研插图示例以及矢量元件的长期积累三个维度出发，系统讲解如何构建并管理个人科研绘图素材库。

#### 1\. 提示词模板

经过多轮反复调试并最终定型的 Prompt，并不只是一次性的指令，而是一套逐渐贴合个人审美与研究方向的定制化模板。第 3、4 章所给出的更多是通用范式，而当你真正完成一整套满意的插图后，这套 Prompt 已经内化了你对科研绘图风格与结构表达的个人偏好，其价值显著高于通用示例。

因此，建议单独保存完整 Prompt，并将其中关于视觉风格、版式组织与表达习惯的核心描述抽离出来，例如对整体风格的定义方式、对模块层级与排列关系的描述逻辑。后续面对新的研究主题时，只需替换关键概念与对象名称，便可快速复用这套已经被验证有效的表达框架，避免每次都从零开始摸索模型的响应边界。

#### 2\. 高质量科研插图示例

在绘图实践与日常文献阅读中，你往往会接触到大量高质量的科研插图示例，其中包括结构清晰的风格参考图、具有辨识度的配色方案，以及在实践中表现良好的排版布局。这些内容同样具有长期价值，应当有意识地加以积累。

具体而言，可以将优秀的科研绘图示例单独保存为风格参考或颜色参考，同时提取其中成熟的配色方案并记录对应的 HEX 色值。此外，对于你借助 AI 绘制并且效果满意的插图，也应一并归档保存。随着积累的增加，你将逐步形成一个高度贴合自身研究方向的参考图库，在后续开展新工作时，为风格决策提供稳定而可靠的依据。

#### 3\. 矢量元件的长期积累

在 5.3 节的矢量化流程中，你通常会得到一批结构明确、表达规范的图形元素，例如神经网络结构示意、标准化的模块或系统图标。这些元素在经过人工校正与语义确认后，便可以转化为可长期复用的科研绘图组件。

因此，建议将这些矢量元件从原始插图中拆分出来，按照功能或语义类型进行分类保存。随着时间推移，它们会逐渐构成一个专属于你的标准化元件库。在后续工作中，无论是在手动调整时快速调用，还是作为 AI 生成的参考垫图，只需搭建新的整体框架，再组合这些成熟组件，便能够在极短时间内完成一张风格统一、逻辑严谨且符合期刊规范的科研插图。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 5.5
小结

本章重点探讨了如何将 AI 绘图从随机生成转化为精确可控的创作过程。首先，通过引入 Schema 重构与视觉约束手段，不再被动接受模型的随机生成，而是让结果精准落地；接着，利用模块化拆解与矢量化处理，解决了复杂长图的逻辑崩塌与期刊投稿的合规性难题；最后，将这些验证过的成果固化为素材库，完成了从单次创作到长期复用的效率闭环。

学会上述技巧后，你手中的 AI 就不再是一个单纯的画图工具，而是一套完整、严谨且高效的科研视觉生产体系。

为了帮助你更直观地复盘这一进阶路径，图 5-5 将本章的核心知识点梳理为了一张全景脉络图。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94854_0.jpg)

图 5-5 第五章知识脉络图 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

# 第六章
规范篇：AI 辅助绘图的伦理边界与合规路径

在 2-5 章中，我们已经系统性地展示了如何使用 Nano Banana Pro 辅助科研绘图，并通过多种案例说明了其在结构设计、布局规划与效率提升方面的价值。但当 AI 工具真正进入科研表达流程时，技术能力本身已不再是决定使用的唯一依据。科研绘图始终受到伦理规范、学术责任与出版规则的共同约束。

因此，本章不再讨论如何更高效地使用 AI 绘图工具，而是回到一个更根本的问题——在当前学术环境中，AI 科研绘图的使用边界在哪里，哪些行为是被明确禁止的，又如何在合规前提下合理引入 AI 辅助流程。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 6.1
AI 科研绘图的伦理红线

科研绘图是否可以使用 AI，并不取决于工具能力本身，而取决于图像在科研论证链条中的功能定位。正如第一章所提出的分类框架，科研插图并非统一处理对象，不同类型图像在信息属性与证据价值上存在根本差异。在规范层面，这种差异会直接转化为可接受性判断的分水岭。

表 6-1 对不同类型科研插图在 AI 使用上的伦理红线与合规操作进行了系统梳理，以当前主流学术规范与期刊审查实践为依据给出直接结论。

表 6-1 基于科研插图分类的 AI 伦理红线与合规操作对照表

![](img/AI科研绘图：Nano Banana极速实战指南_content_94856_0.jpg)

需要强调的是，表 6-1 所划定的并非“技术可行性边界”，而是“学术责任可承担性边界”。当图像直接参与科研结论的成立过程时，其生成与处理方式必须保持完全可追溯；当图像仅用于辅助理解与概念表达时，AI 才具有相对明确且可控的使用空间。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 6.2
从期刊政策看 AI 科研绘图的合规边界

围绕 AI 生成图像的使用，在当前法律与出版实践中，学界已逐步形成稳定共识：完全由生成式 AI 自动生成且缺乏人类实质性介入的图像，难以被认定为符合科研出版所要求的原创性表达。

这一判断，并非针对 AI 技术能力本身的价值评估，而是源于著作权制度对“作者主导性”“创造性判断”与“责任可追溯性”的基本要求。国际出版伦理委员会（Committee on Publication Ethics，COPE）指出，AI 工具不具备人类作者的原创性意图，也无法承担署名与版权责任，因此作者必须对论文中出现的全部图像内容承担完整的学术与法律责任。

在具体执行层面，不同期刊与出版集团在措辞与适用范围上存在差异，但其规范导向高度一致：均对直接使用生成式 AI 产出的图像持严格限制态度，并将“是否存在可核查的人类创作路径”作为核心判断标准。表 6-2 汇总了主要国际期刊与出版集团在 AI 生成图像方面的公开政策立场，用以呈现这一共识在出版规则中的具体体现。

表 6-2 主流国际期刊对 AI 生成图像的声明与使用要求概览

![](img/AI科研绘图：Nano Banana极速实战指南_content_94857_0.jpg)

在上述政策背景下，直接将生成式 AI 输出的位图作为科研插图投稿，往往在版权归属与出版合规两个层面同时面临风险。图像既难以满足原创性要求，也缺乏清晰的责任主体界定，从而成为编辑与审稿环节重点关注的潜在问题源。

因此，第五章所提出的矢量化重绘流程，在出版与投稿的语境中具有明确的合规价值。通过对结构、布局与视觉表达的系统性重构，作者将图像转化为经由人类判断、选择与修正的科研表达成果。图像中所体现的创造性决策与科学责任由此可以清晰归属于作者本人，从而满足期刊对原创性与责任主体的基本要求。在这一意义上，矢量化重绘并非单纯的画质优化步骤，而是连接 AI 辅助流程与科研出版规范之间的关键合规环节。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 6.3
投稿阶段的透明披露与自查建议

在完成第五章所述的矢量化重绘流程、人工审核与修正后，图像在当前主流国际期刊的规范框架内，通常已具备较高的合规安全性。在这一流程中，AI 生成内容仅作为构思阶段的辅助草图，用于参考整体布局或配色方案，最终发表的图像由作者主导完成并承担全部学术责任。

在这种前提下，是否需要对 AI 的使用进行透明披露，不同期刊的要求并不完全一致。部分期刊明确要求披露工具名称与用途，但在声明位置、表述细节上仍存在差异。因此，在投稿前，我们仍应以目标期刊的最新作者指南为准，避免因理解偏差引发不必要的合规风险。

出于稳妥的考虑，本书建议在致谢、方法部分或图注中，对 AI 工具的使用情况进行简要而明确的说明。声明内容应包括所使用的工具名称、版本信息及具体用途，例如用于构思阶段、结构草图或配色参考，同时明确指出所有科学判断、结构设计与最终图像内容均由人类作者完成并负责。

以下是一种可供参考的英文声明模板：

The authors acknowledge the use of Nano Banana Pro(powered by Google Imagen 3)for the initial conceptualization and color palette selection of Figure[X].The final figure was manually redrawn as a vector graphic and verified by the authors to ensure scientific accuracy and compliance with publication ethics.The authors remain fully accountable for the integrity of the work.

在正式投稿前，我们还应从编辑与审稿人的视角，对全部插图进行系统性自查。建议为每一张图像建立对应的原始材料包，完整保留未经压缩的原始文件、绘制过程记录以及关键的中间版本。对于曾使用 AI 辅助的插图，可以提前准备好简要的说明文本与必要的操作记录，以便在编辑或审稿人询问时能够迅速、清晰地回应。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 6.4
小结

本章围绕 AI 科研绘图在当前学术体系中的合规边界，从伦理红线、期刊政策与投稿实践三个层面进行了系统梳理。随着期刊政策与出版伦理逐步明晰，图像是否合规，已不再取决于工具来源，而取决于生成路径是否可追溯、责任是否清晰归属作者。

在这一前提下，本书所倡导的将 AI 仅作为科研绘图前期辅助、而非直接产出工具的技术路线，严谨契合了目前主流期刊的审核要求。这种将规范转化为操作习惯、主动使风险控制前置于创作过程的思路，构成了当前阶段更现实、也更稳妥的实践策略。

为了帮助你更直观地复盘本章要点，图 6-1 将本章核心内容整理为了一张全景式的知识脉络图。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94859_0.jpg)

图 6-1 第六章知识脉络图 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

# 参考文献

[1] DeepSeek-R1\. DeepSeek-R1 benchmark comparison figure[EB/OL]. (2025-1-20)[2026-01-24].

[2] Savage J C, Picard K, González-Ibáñez F, et al. A brief history of microglial ultrastructure: distinctive features, phenotypes, and functions discovered over the past 60 years by electron microscopy[J]. Frontiers in immunology, 2018, 9: 803.

[3] Vaswani A, Shazeer N, Parmar N, et al. Attention is all you need[J]. Advances in neural information processing systems, 2017, 30.

[4] Bustillo J P O, Engels E E M, de Rover V, et al. Three-dimensional bioprinted in vitro glioma tumor constructs for synchrotron microbeam radiotherapy dosimetry and biological study using gelatin methacryloyl hydrogel[J]. Scientific Reports, 2025, 15(1): 13868.

[5] Gowal S, Bunel R, Stimberg F, et al. SynthID-Image: Image watermarking at internet scale[J]. arXiv preprint arXiv:2510.09263, 2025.

[6] Tian S, Liu S, Zhu R. Flexible thermal array sensor for imperceptible monitoring of multidimensional vascular dynamics[J]. Science Advances, 2026, 12(3): eaea5803\. EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

# 后记

至此，关于 Nano Banana Pro 辅助科研绘图的系统探讨已近尾声。

通览全书，本书从基础操作与使用技巧出发，逐步拆解了不同学科场景下的常用提示词，延伸至进阶用法，并最终落脚于以矢量化重绘为核心的合规实践路径。这一系列讨论的初衷，是在繁复的绘图工作与严谨的科研表达之间，搭建一套可被实际采用的高效方法体系。

我们正身处一个技术爆发的时代。生成式模型的能力持续增强，算力与算法的结合正在深刻改变科研工作的组织方式。除了本书所呈现的系统化工作流之外，越来越多的科研实践者也在探索基于 AI 的绘图自动化方案，或尝试通过各类科研 Agent 重构既有流程。这一趋势清晰地表明，那些曾经长期困扰科研人员的重复性、机械性劳动，正在被技术逐步缓解。

然而，工具的演进越是迅猛，使用者的定力便越显珍贵。

我们必须清醒地认识到，无论工作流多么智能、交互多么便捷，在当前阶段乃至可预见的未来，AI 仍具有不可忽视的“黑盒”属性与潜在的“幻觉”风险。它可以生成高度复杂且视觉上精致的图像，却无法理解图像所承载的科学假设；它能够提供丰富的表达方案，却无法为研究结论的真实性承担责任。

因此，在科研绘图这一环节中，使用者和决策者，永远只能是人类本身。这不仅关乎科研成果的准确性，更关乎科学交流体系赖以成立的信任基础。

对研究者而言，真正重要的并非对具体工具的技巧性掌握，而是形成持续有效的专业判断能力。作为一名合格的科研工作者，我们需要怀着审慎、客观的态度去驾驭 AI。既要善用其能，以提升效率；又要深知其短，以严谨的验证填补算法的漏洞。

展望未来，期刊政策与审查机制或许会随技术环节的变化而不断调整，但围绕透明披露、责任明确与科学可控所形成的基本原则，预计将长期保持稳定。

愿本书不仅能成为你案头提升效率的工具指南，更能成为一道助你在学术规范安全区内自由探索的防火墙。在追求科学真理的道路上，让 AI 成为你的得力助手，同时始终将科学判断与创造的主动权牢牢掌握在自己手中。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==