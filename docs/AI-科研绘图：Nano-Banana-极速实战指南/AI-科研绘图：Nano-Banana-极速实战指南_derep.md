# Nano Banana 科研绘图快速实战

# 内容速览

生成式图像合成技术在过去两年里加速演进，为学术插图提供了新的技术底座。本书以科研可视化为核心，基于 AI 作图模型搭建一套可复用的工作方法，能够适配多学科的表达需求，直面研究者在论文配图环节的典型痛点，形成完整闭环。开篇从观念层面重新定位科研插图的价值，明确高质量图形的评判标准并论证引入 AI 的必要性；第二章以 Nano Banana Pro 为演示案例，介绍模型接入方式、核心功能以及配套工具的配合；第三至五章聚焦方法实践，涵盖从论文文本抽象结构关系、各学科场景下的提示构建、复杂长图的模块化生成与矢量管理；第六章梳理 AI 辅助绘图的伦理边界与学术规范。全书以结构拆解与精准描述为底层逻辑，帮助研究者提升图示的质量与效率，既可为已熟悉 AI 工具者固化稳定流程，也为初学者提供落地的起点。

本书面向所有需要制作科研论文配图的研究人群，尤其是不具备设计背景、希望提升表达效率与质量的硕博研究生和青年教师。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

# 前言

读到这里的你，可能正处在一种常见的瓶颈：论文框架已成型，实验数据已反复核验，推导链条经得起检验，唯独配图迟迟不能定稿；一张流程示意图反复推翻，一套配色被多次重来，时间在无声流逝。配图困境并非单纯的排版问题。视觉呈现往往决定论文的第一印象，清晰的结构与表达则直接影响审稿人与读者在有限时间内对研究贡献的判断。

近年，人工智能在图像合成领域发展迅速，模型对细节的把控更稳，对结构的控制力明显增强。越来越多的人尝试借助 AI 完成设计创作。作为长期实践科研绘图的人，我一直在思考：这些模型能否真正服务科研场景，而不仅停留在艺术创作？

起初的尝试并不顺利。提示不准确时，输出杂乱无序；逻辑关系表达不清时，模型难以理解意图。在不断试错中我发现，决定成图质量的关键在于把论文内容转译为清晰的结构，再用准确的语言描述出来。随着方法逐渐成型，Nano Banana Pro 等模型能够稳定产出符合学术审美的初稿。从偶然的盲测到可复现的输出，这一转变促使我想把路径系统化。

起初我将方法压缩成文章发布于 Datawhale 社区公众号，获得广泛反响。但留言中不少研究者围绕工具替代、后期可编辑性、跨学科适用性以及学术规范提出具体疑问。这些真实反馈让我意识到，单篇文章足以讲清一个工作流，却承载不了完整体系。在社区支持与出版团队推动下，这套方法才最终系统化成书。

为回应这些痛点并补齐工作流缺口，本书并非工具说明书，而是一套围绕科研绘图的方法论。第一章从认知角度审视科研插图的本质，将其视为科学信息的视觉转译。第二章以 Nano Banana Pro 为例讲解落地路径与提示构建逻辑，并补充 Qwen-image-2.0 等入口，便于快速建立能力。第三、四章聚焦方法论，探讨如何从论文中抽象结构关系，并在计算机、材料化学、生物医学等场景下实现可控表达。第五章进入进阶，强调精准控制、风格沉淀与体系化管理。第六章专门梳理学术伦理边界，确保在提升效率的同时守住规范底线。

本书核心价值不仅在于节省绘图时间，更在于提供一种新的思维方式。科研插图承载逻辑、结构与思想，一旦掌握结构抽象与表达的方法，工具迭代便不再焦虑。无论是 Nano Banana Pro、Qwen-image-2.0，还是未来更强的模型，这套底层思路依然适用。

本书面向所有需要制作科研论文插图的研究者，尤其是不懂设计却渴望提升表达效率的硕博研究生与青年教师。若你已会用 AI 作图，本书可帮助你建立更稳的工作流与个人风格；若你仍在观望，它也可作为实践的起点。

阅读与实践中请保持独立判断。技术在演进，学术规范也在完善。如何在工具创新与学术诚信间划定边界，是每位研究者必须面对的课题。需要说明的是，本书提供方法与经验，最终合规责任与学术判断仍需研究者自行承担。

最后感谢在写作出版过程中给予支持的师长与同仁。感谢导师陈太聪老师在研究与写作阶段的持续鼓励，使探索得以系统化。感谢 Datawhale 社区周理璇、范晶晶、马晓皖、卢水琼及社区成员在传播与交流中的反馈。感谢人民邮电出版社信息技术分社陈冀康社长与策划编辑卜一凡的专业指导。感谢家人长期理解与陪伴。正是这些支持使本书得以完成。

愿本书助你提升科研表达能力。当再次面对空白画布时，心中有结构，手中有方法。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

# 第一章
认知篇：重塑科研绘图的意义

科研路上，很多人都有相似的卡点。实验终于跑通，论文逻辑也清晰，最后却卡在配图上。用 PPT 拼凑的流程图显得业余，用 Visio 连线总是对不齐，想学专业三维软件又没时间。更头疼的是审稿意见，哪怕改一个模块，整张图可能要推倒重来。我们常把宝贵的研究时间浪费在对齐线条、调配色等机械劳动上。

本章从认知层面重新审视科研绘图。需要厘清在 AI 时代哪些图必须亲手完成、哪些环节可借助 AI 提效，并学会以审稿人视角评估一图的优劣。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 1.1
科研绘图的本质与边界划分

科研绘图与艺术创作不同，其本质是科学信息的视觉转译。

在论文中，图表的核心任务是降低读者的认知负荷，以最高效率传递科学逻辑。面对繁多的图表类型，研究者需要建立清晰的分类坐标，这决定了工具选择与伦理边界。

基于实践，这里做一个工作性划分，把常见科研插图归为三类。

第一类是定量数据图。这类图表直接呈现实验结果，包含折线、柱状、散点等，核心特征是每个像素点都对应真实数值。在此类型中，主流期刊普遍禁止用生成式 AI 直接生成图像内容。AI 生成基于概率预测，输出的数据点往往是像素规律的“幻觉”，而非真实数据。正确做法仍是用 Python、Origin 等专业软件作图，AI 仅可提供配色建议或编写绘图代码。图 1-1 即为典型的定量数据图。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94829_0.jpg)
图 1-1 定量数据图参考图 ^([1])

Nature 官方在其图像完整性政策中指出，投稿的数字图像必须如实反映原始数据，不得使用会改变数据本质或掩盖原始数据的手段，编辑或会用软件检测处理并要求提供原始未处理数据。

第二类是实证影像图。这类图是实验的直接证据，包括显微照片、电泳图、实物装置等，记录客观事实，强调原真性。此类图像通常不允许进行生成式填补修改，包括 AI 去噪或放大，仅允许全图线性亮度/对比度调整。图 1-2 所示的电镜下细胞结构是典型代表。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94829_1.jpg)
图 1-2 实证影像图参考图 ^([2])

第三类是定性示意图。这正是本书聚焦的核心，也是科研人员最耗时的部分。它不依赖具体数值，侧重表达逻辑、流程、机制与概念，是脑中假说与推演的具象化，如图 1-3 所示的机制示意。无论是细胞信号通路原理图、算法架构流程图，还是期刊封面，都属于此类。

需要明确的是，即便不涉及数据，主流期刊规范下 AI 直接生成的示意图通常也不宜作为终稿直接提交。但在设计阶段其价值极高。Nano Banana Pro 能理解复杂结构化文本，将抽象逻辑快速转为高质量视觉草案，为后续人工绘制与修改提供构图思路与风格参考，大幅降低时间成本。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94829_2.jpg)
图 1-3 定性示意图参考图 ^([3]) EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 1.2
怎样才算一张“好图”

评价一张图不能仅靠主观感受。在同行评审语境下，能被顶刊接受的插图通常要同时满足科学性、逻辑性与艺术性，三者共同决定专业度与说服力。同时，科研插图的审美也存在通用秩序，并因学科而异。本节从特性、通用准则到领域适配梳理达成路径。

### 1.2.1 “好图”应具备的特性

首先，科学性是不可逾越的底线。无论构图多精致，只要违背基本科学常识，图就失去价值。科学性是生命线，也是最基础的合格要求。核心标准是诚实：所有视觉表达必须真实反映数据、模型与实验事实，任何误导都会导致失败。

其次，逻辑性决定叙事是否顺畅。一张好的科研图应具备自明性。读者在不读正文的情况下，仅凭图与图注就能理解核心表达。这要求信息呈现顺序明确，视线流动自然，无需反复比对或猜测。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94830_0.jpg) 视觉层级（Visual Hierarchy）

通过位置、大小、对比度与色彩差异，在图中建立清晰的阅读优先级，引导读者先看重点，再关注次要内容。

当视觉层级清晰时，读者几乎无需刻意思考阅读起点，理解过程更顺畅低负担。这也是顶级期刊插图显得专业稳定的原因之一。理性判断也很关键。删减无关装饰、控制背景复杂度、统一配色体系，可确保注意力始终聚焦关键路径，弱化干扰。

最后，艺术性体现认知负荷管理能力。在科研语境下，它不等同于装饰或炫技，而是借助视觉心理学规律降低信息处理成本。一张让人觉得清晰舒服的图，往往源于顺应了人类感知本能。艺术性是一种功能化设计能力。合理的布局、克制的配色与清晰的结构，最终服务于理解效率，而非视觉炫耀。

### 1.2.2 科研美学的通用法则：克制与秩序

要实现上述特性，构建权威感的科研美学，无论学科都需要遵循共通的视觉语法，核心在极简与有序。

首先是减法思维。很多问题不是信息不足，而是信息过载。无关背景、装饰线条与视觉特效会持续消耗注意力，使关键数据被淹没。成熟审美体现为克制，移除干扰后数据自然成为焦点。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94830_0.jpg) 数据墨水比（Data–Ink Ratio）

图形中用于呈现数据的墨水应尽可能占高比例，所有不承载数据或必要结构的元素都应削减。优秀的可视化应最大化数据表达，最小化视觉噪声。

在该原则下，默认灰底、密集网格、立体阴影与冗余边框都是低价值元素。它们并不会帮助理解，反而打断视觉注意力。移除后图形会立刻更清晰有力。

其次是利用布局建立秩序。顶刊组图之所以整齐专业，很大程度上源于对视觉直觉的利用。相关结果紧密排列，读者会下意识视为一组；在不同图板为同一变量保持一致颜色或符号，即便无文字说明也能迅速建立对应。这种由空间位置与视觉相似性形成的内在秩序，使信息理解几乎无需额外思考。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94830_0.jpg) 格式塔原则（Gestalt Principles）

大脑天然倾向把位置接近、形态相似的元素归为整体，科研绘图正是借助这一认知规律，在不增加文字负担的情况下完成逻辑表达。

最后是科学的色彩管理。颜色是传递数据的载体而非装饰。传统彩虹色谱因亮度突变易造数据幻觉，已被视为误导。现代标准推荐 Viridis 或 Magma 等感知均匀色谱。同时考虑色觉障碍人群，避免红绿配对，改用洋红与绿色，既体现包容性，又提升对比与专业感。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94830_0.jpg) 感知均匀色谱（Perceptually Uniform Colormap）

此类色谱在亮度与颜色变化上更平滑，不会因颜色跳变夸大或掩盖数据差异，从而降低视觉误判风险。

### 1.2.3 领域风格的定向适配

掌握通用法则后，还需洞察不同学科的审美偏好，这决定插图是否具备“圈内”味道。当前顶刊审美主要呈现两种取向。

一种偏向物理、计算机与人工智能领域的极简主义。以 CVPR、NeurIPS 或 Nature Physics 为代表，这类图偏好扁平化与矢量感。以图 1-4 为例，多用低饱和莫兰迪色系，线条硬朗清晰，强调拓扑结构与逻辑流的直接表达，排斥不必要的三维渲染与光影修饰。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94830_4.jpg)
图 1-4 计算机领域插图风格 ^([3])

另一种偏向生物、医学与材料科学的拟真风格。以 Nature、Science、Cell 为代表，这类图更青睐三维质感与真实环境复现。它们强调微观细节丰富度，常利用环境光遮蔽与次表面散射等渲染技术模拟细胞、蛋白或纳米材料的真实质感，以强视觉冲击营造沉浸式微观世界。图 1-5 即体现了这一拟真风格。

理解并对齐这两种审美取向，能让插图迅速获得审稿人的专业认同，在同行评审中占据先机。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 1.3
为何选择 AI 作图

也许你还在犹豫：PPT 与 Visio 虽慢但够用，真的需要引入 AI 吗？即便不计最终成稿，仅从设计与迭代效率看，这一步改变就已值得。以前画一张复杂的神经网络架构图，半天时间很容易搭进去。现在用 AI 输入指令，几分钟就能得到多种方案，帮你从繁琐的对齐连线中解脱，腾出精力聚焦论文逻辑与实验本身。

除速度外，AI 还解决了一个科研人员普遍的短板：审美。研究者往往逻辑强，知道图里该有什么，却未必懂得如何让它“好看”。布局如何平衡？配色如何高级？这些设计短板常导致图科学性达标却视觉欠佳。AI 的介入相当于配备一位中上甚至顶级的专业设计师。你负责科学逻辑，配色、光影与排版交给它。它能把草图瞬间渲染出顶刊封面质感，让你不再因配图质量处于劣势。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94831_0.jpg)
图 1-5 生物医学领域插图风格 ^([4])

当然，要让科研工作者放心交付绘图任务，工具必须具备极高可信度与精确性。得益于两次关键技术突破，AI 作图现已具备“科研级”可行性。

首先是 LLM 语义理解与空间逻辑的质变。早期 AI 仅靠关键词概率猜测画面，易导致结构崩坏。当前新一代模型背后有强大大语言模型支撑，AI 从“画师”进化为“工程师”——具备复杂因果推理能力。当输入“A 包含 B，且 B 指向 C”时，高级模型不再随机堆砌元素，而是先构建准确逻辑拓扑，再进行视觉渲染。这意味着可用自然语言实现对图像结构的精确控制。

更关键的突破在文字渲染能力的成熟。这为 AI 科研绘图补上最后一块拼图。过去 AI 不擅长画面字符，生成大量乱码，导致必须后期 PS 修订。现在像 Z-Image、Nano Banana Pro 等模型已具备原生文字理解与生成能力，不仅图文融合完美，甚至可用于生成海报、带标注的漫画、项目架构图和科研图表。

最后，为什么 Nano Banana Pro 是科研场景的首选？

当前可用的生成式人工智能平台已经相当多元化。Midjourney 在艺术表现力上优势明显，但要实现精细化调控并不容易；Stable Diffusion 功能覆盖面广，上手成本却偏高。Nano Banana Pro 能在科研绘图赛道脱颖而出，关键在于它具备接近工程化的理性思维。它可以准确拆解晦涩的专业术语与复杂的空间指令，并始终坚持事实优先，有效抑制了纯艺术类模型常见的发散想象与幻觉偏差。正因如此，拓扑结构、生化通路、流程图这类逻辑密度极高的图像都被它轻松驾驭，而这正是科研示意图的核心战场。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 1.4

小结

本章为科研绘图实践搭建了认知的底层框架。我们重新梳理了科研插图的本质与边界，明确了 AI 在定性示意图场景中的核心价值。同时分析了构成优质图件的科学性、逻辑性与艺术性三个维度，形成了“克制与秩序”的科研美学准则，并指出了不同学科视觉风格定向适配的方向。最后论证了引入以 Nano Banana Pro 为代表的 AI 绘图模型对科研工作流的必要性与颠覆性意义。为便于内化这些底层观念，图 1-6 将本章的核心认知体系整合成一张全景式的知识脉络图。

认知决定上限，工具决定效率。在绘图理念与审美标准建立之后，第二章将正式进入“工具篇”，系统拆解本书的主力生成模型及其上下游工具链，为后续实战打下基础。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94832_0.jpg)

图 1-6 第一章知识脉络图 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

# 第二章

工具篇：Nano Banana Pro 快速上手指南

第一章已经构建起科研绘图的认知框架，明确了科研插图的主要类型以及合格乃至优秀的评判标准，并论证了在当前技术条件下将 AI 用于定性示意图这一高频高耗环节的现实价值。

从本章起，重心由理念转向工具。本章旨在帮助你快速掌握以 Nano Banana Pro 为代表的核心模型，并补齐生成前准备与生成后处理阶段所需的关键工具，为方法论学习与动手实操做铺垫。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 2.1

模型接入方式与前沿拓展

在 Google 官方体系中，Nano Banana Pro 对应的模型标识为 gemini-3-pro-image-preview。围绕该能力，当前存在多种形态的使用方式，底层模型保持一致。

从接入渠道看，可归纳为官方渠道与第三方渠道两类。官方渠道内部又包含 API、可视化实验平台与网页产品等不同入口。它们并非相互替代，而是同一模型的不同使用路径，在是否需要编程、参数控制精度与适用阶段上各有差异，理解其定位差异有助于选择合适的接入方式。考虑到中文语义理解与接入便利性，我们也建议将 Qwen-image-2.0 作为一种具备互补价值的本土化选项，详见 2.1.3 节。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

### 2.1.1 官方渠道

官方渠道是模型能力的最直接来源，也是第三方平台的底层基础。官方渠道内提供了多种面向不同控制需求与使用场景的方式。表 2-1 整理了官方渠道内 Nano Banana Pro 三种典型方式的优缺点及适用对象。

表 2-1 官方渠道内 Nano Banana Pro 三种典型使用方式的优缺点及适用对象

![](img/AI科研绘图：Nano Banana极速实战指南_content_94834_0.jpg)

#### 1\. 官方 API

官方 API 是最标准最底层的调用方式。常规流程是通过 Google 官方平台 Google AI Studio 申请权限、获取 API Key 并为项目配置计费账户，之后参考官方文档在代码中直接调用图像生成接口。

其优势在于模型版本更新最及时、规则最清晰，非常适合具备编程基础、希望将绘图能力嵌入可复用科研工作流的研究人员。你可以在 Python 脚本中集成生成功能，实现批量化或构建可复用的科研绘图流水线。事实上，市面上绝大多数第三方工具底层调用的就是这一接口。

#### 2\. Google AI Studio

Google AI Studio 是 Google 提供的官方可视化实验与调试平台。若不想写代码又希望获得比普通网页端更丰富的参数控制，AI Studio 较为合适。在 AI Studio 的 Playground 界面中，选择 Nano Banana Pro 模型后，可通过交互界面直接输入提示词并生成图像。图 2-1 展示了 Google AI Studio 的使用界面。

需要说明的是该方式并非免费，底层仍依赖 Google AI Studio 的 API Key 并要求项目已配置计费账户，计费逻辑与官方 API 一致。相比编码，它允许通过图形界面直接调整尺寸、分辨率等参数，适合在工程化前探索模型边界、调试复杂提示词结构。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94834_1.jpg)

图 2-1 Google AI Studio 的使用界面

#### 3\. Gemini 网页端

对于希望以最低成本快速体验 Nano Banana Pro 生成能力的用户，Gemini 网页端是最直接的入口。

目前在 Gemini 网页界面中，通过对话直接生成的图像默认由截至 2026 年 3 月的最新 Nano Banana 2 模型提供，官方名称为 Gemini 3 Flash Image。要使用 Pro 模型，需两步操作：先在“工具”中选择“制作图片”，通过对话窗口输入提示词生成初始图像；随后点击生成图像下方的“更多”图标，选择“使用 Pro 重新生成”，即可调用 Nano Banana Pro 获得细节更丰富的科学插图。图 2-2 展示了 Gemini 网页端中使用 Nano Banana Pro 的具体流程。该方式学习成本最低，但在可控性与参数精细度上相对受限。

值得一提的是，默认的 Nano Banana 2 在科研绘图上表现同样出色，尤其在生成速度与文字准确渲染方面具有优势，但在整体视觉质感与复杂细节的严谨度上，与 Pro 模型仍有差距，可根据需求灵活选择是否消耗额度重绘。

根据 Gemini 官方帮助文档关于图片生成与编辑功能的说明，Google AI 方案决定了每日生成额度和图像质量。Gemini 网页端不同方案的权益对比见表 2-2 所示。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94834_2.jpg)

（a）

![](img/AI科研绘图：Nano Banana极速实战指南_content_94834_3.jpg)

（b）

图 2-2 Gemini 网页端中使用 Nano Banana Pro 的方式

（a）选择“制作图片”并通过对话窗口输入提示词生成初始图片

（b）点击生成图片下方的“更多”图标，并选择“使用 Pro 重新生成”

表 2-2 Gemini 网页端不同 Google AI 方案的权益对比

![](img/AI科研绘图：Nano Banana极速实战指南_content_94834_4.jpg)

两点说明：一是上述额度每日重置，因图像生成需求较大，具体限制可能随 Google 策略调整；二是表 2-2 仅展示与图像生成相关的权益差异，其他方面仍存在显著区别，具体以官方最新定价为准。

### 2.1.2 第三方渠道

官方渠道权威性最强，但受网络稳定性、跨境支付门槛或配额策略影响，许多研究人员在实际操作中遇到阻碍。为解决“最后一公里”问题，市场上出现了多种第三方方案。这些渠道在易用性与成本上各有侧重，但风险程度也不同。

表 2-3 对比了两种主要的第三方渠道使用方式，可根据技术背景与数据安全需求选择。

表 2-3 两种主要的第三方渠道的使用方式对比

![](img/AI科研绘图：Nano Banana极速实战指南_content_94834_5.jpg)

#### 1\. 集成化第三方平台

集成化第三方平台的特点是开箱即用，无需配置 API Key，也无需担心网络连接，面对的是一个封装好的设计网站。

Lovart 是一个典型代表。如图 2-3 所示，用户在平台看到的是 一键生成流程，其底层可能调用 Nano Banana Pro，也可能根据场景切换至其他模型。Higgsfield 以及其他可通过搜索引擎找到的在线生成工具同属此类。

这类平台通常以账号体系限制每日生成次数，模型具体参数设置与调用路径对用户不可见。

本书建议：选择第三方平台时需注意，部分平台对提示词长度限制较严。而科研论文插图初稿生成时，Nano Banana Pro 通常需要较长且结构化的提示词，这在实际使用中可能成为明显制约。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94834_6.jpg)

图 2-3 Lovart 中使用 Nano Banana Pro 的操作界面

#### 2\. 第三方中转 API

第三方中转 API 是部分有代码能力的科研人员常用的折中方案。

在此模式下，用户在代码中配置的不是 Google 官方地址，而是第三方提供的接口地址。服务商负责将请求转发至官方 Nano Banana Pro 接口并按量计费。从代码层面看，调用方式与官方 API 高度一致，仅接口地址和模型名称有差异，具体细节需参考服务商文档。

这类中转服务的单价通常低于 Google 官方定价，多通过批量采购或混合路由实现，但稳定性取决于路由策略。

必须注意，市面上部分定价极低甚至免费的 API 服务，可能通过逆向工程等非正规手段实现，这不仅意味着服务随时可能中断，科研数据即提示词中的实验设计或创新点更会经过不可控的第三方服务器。

本书建议：处理未发表的敏感科研数据时，谨慎评估此类服务的安全性，优先使用官方渠道。

### 2.1.3 Qwen-image-2.0 使用渠道

2026 年 2 月，阿里云通义团队正式发布新一代 AI 绘图模型 Qwen-image-2.0。尽管本书方法论以 Nano Banana Pro 为主，但其底层逻辑同样适用于同类高水平模型。经测试，Qwen-image-2.0 在处理含特定中文科研术语的提示词时捕捉更敏锐，也为国内研究人员提供更稳定的访问体验。

目前可通过通义官方网站 https://chat.qwen.ai/ 直接使用。进入网页后，将模型切换为 Qwen3-Max，在对话框功能区选择“生成图像”，输入图像内容描述即可开始创作。图 2-4 展示了 Qwen-image-2.0 的使用方式。该方式无需复杂配置，适合快速验证灵感，或在 Nano Banana Pro 难以准确理解中文指令时作辅助生成。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94834_7.jpg)

（a）

![](img/AI科研绘图：Nano Banana极速实战指南_content_94834_8.jpg)

（b）

![](img/AI科研绘图：Nano Banana极速实战指南_content_94834_9.jpg)

（c）

图 2-4 Qwen-image-2.0 的使用方式

（a）选择模型 （b）选择“生成图像” （c）描述图像内容

本书实战环境说明：引入 Qwen-image-2.0 的初衷是为国内科研人员提供稳定、无障碍且懂中文的优质平替方案。为保证全书教学逻辑前后连贯，后文实战案例与提示词模板均以 Nano Banana Pro 为演示，但方法跨模型通用，后续方法论可无缝迁移到 Qwen-image-2.0 中，只需将原本的英文指令翻译为对应的中文描述即可，本书后续将不再对双模型进行重复对比演示。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 2.2

Nano Banana Pro 核心功能解构

了解如何打开工具后，下一步是理解它在科研绘图流程中的职能。

与以审美驱动为主的图像生成模型不同，Nano Banana Pro 在科研场景中的定位更接近一位能理解结构、约束与因果关系的工程协作者。它不追求自由发挥，而擅长在明确规则下完成视觉构建。

这也决定了使用它的核心方式并不复杂，无需成为提示词专家，只需掌握两种覆盖绝大多数科研绘图场景的对话模式：结构化生成的文生图，以及引入视觉约束的图生图。

### 2.2.1 文生图

在科研绘图中，最需警惕的是 AI 的随意发挥。因此要尽量减少模型的自由想象空间，将其严格约束在明确的目标边界内。当输入“画一个神经网络”时，许多传统模型会返回科技感强但逻辑松散的插画。而 Nano Banana Pro 的优势在于理解结构化指令，这正是第三章将重点介绍的“逻辑构建”思维的基础。接下来介绍文生图的两个技巧。

#### 1\. 像写伪代码一样写提示词

为实现精准控制，本书不建议使用散文式自然语言，而是推荐采用模块化、分层级的指令组织方式。用户可显式声明绘图的关键约束，如整体风格、空间布局、拓扑关系。

● **整体风格：** 明确图像更接近哪类期刊的视觉审美，例如“CVPR 偏好的扁平化风格”“Nature 常见的拟真风格”。

● **空间布局：** 定义画面的物理分区与层级关系，例如“左侧为输入层，中间为处理层，右侧为输出层”。

● **拓扑关系：** 明确组件间的连接方式与方向，例如“箭头 A 从区域 1 指向区域 2，并形成闭环结构”。

这种高度逻辑化的输入方式能在很大程度上抑制模型幻觉，确保生成结果在科学逻辑上自洽。

此外，尽管 Nano Banana Pro 中文理解能力良好，但在涉及复杂结构约束与多层逻辑描述时，英文提示词在精确度与稳定性上仍更具优势。

本书建议：在跟随本书使用 Nano Banana Pro 实战时，优先用英文撰写核心绘图指令。如需将图中文字或说明转为中文，可在后期编辑阶段统一处理。若使用前文提及的 Qwen-image-2.0 等中文原生模型，则不受此语言限制。

#### 2\. 参数化控色

文生图的关键技巧之一是对色彩进行参数化控制。科研插图对色彩风格有较为严格的隐性规范，例如 Nature、Science 等期刊常见低饱和度、弱对比度配色。仅用“蓝色”“红色”等模糊描述，往往得到过于鲜艳、缺乏专业质感的结果。

在 Nano Banana Pro 中，可直接在提示词中 **指定 HEX 色值**，例如 *Primary Color:Azure Blue#E1F5FE*。模型能够准确解析这些颜色参数，从而生成符合主流顶级期刊审美的图像，有效规避“塑料感”问题。

为更直观展示上述两种技巧效果，本节尝试复刻图 1-3，即多头注意力机制原理示意图。对比两种不同提示词方式的生成结果。图 2-5 展示使用较为模糊的自然语言描述生成的效果图；图 2-6 则采用结构化提示词与参数化控色策略。

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

把架构图画到期刊级别：先对齐结构再对齐风格

视觉层面，矩形背后叠加层层阴影来暗示纵深感，这一块正好对应多头模块；连线层面，来自第二区的箭头在此汇聚。

[Zone 4: Output Processing (Top)]
- Object A: A rectangle labeled "Concat" (Color: Pastel Yellow #FFF59D)
- Object B: A rectangle labeled "Linear" (Color: Light Grey #E0E0E0)
- Layout: Object B is placed above Object A

[Style Enforcement]
- Aesthetic: CVPR/NeurIPS Academic Style
- Vibe: 2D Flat Vector, Thick Black Outlines, Academic Schematic

从比对上看，图 2-5 只完成了最基础的结构近似，在视觉统一性和细节刻画上差距明显；图 2-6 采用的结构化指令，已经较大程度复现了目标期刊配图的视觉语言，还原水平大约在八成左右。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94835_0.jpg)

图 2-5 自然语言提示词生成图像

![](img/AI科研绘图：Nano Banana极速实战指南_content_94835_1.jpg)

图 2-6 结构化提示词生成图像

这里再补充一点，写不出这么细致的指令完全不用焦虑。实际流程里，指令的初稿可以先让大模型帮忙草拟，第三章会详细讲方法。

### 2.2.2 图生图

文字描述的表达力很强，但碰到多层嵌套的网络拓扑或复杂的分子形态时，对空间位置的把控力会明显不足。

这时引入参考图就成了体验的分水岭，它不只是可选项，而是科研制图从能用迈向好用的关键门槛。参考图主要承担两项功能：给出空间拓扑的骨架，以及锁定视觉特征的目标风格。

#### 1\. 草图即骨架

不需要专业绘画基础。无论是鼠标在绘图工具里随手勾的线条、PPT 里粗放的框架，还是纸上几笔速写，只要邻接关系和指向关系正确，模型就能把它当成整张图的骨架来用。

把这类草稿上传后，原本冗长抽象的文字描述被清晰的空间约束替代。系统保留草稿的结构关系，同时对线条、配色和质感做系统优化。整体来看，一张结构对但外观粗糙的草稿，往往比冗长的空间文字描述更可靠。

仍以复刻图 1-3 为例。上传图 2-7 手绘草图，并配合下方指令，即可得到图 2-8 的结果。

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

可以看出该版本的细节还原明显高于纯文字结构描述。这组指令几乎没有涉及逻辑关系的描述，而是聚焦配色与整体风格的约束。原本需要反复书写的连接关系，被草图以更低成本的方式准确表达出来。

#### 2\. 风格迁移与对齐

除结构参考外，以图引导生成也能用于风格对齐。阅读文献时，若遇到配色、线条、质感都高度契合目标期刊的插图，可将其作为风格参考提供给模型。

此时模型会放弃默认渲染习惯，转而对齐参考图的视觉特征，如线条粗细、配色与扁平化程度，从而快速产出与目标期刊视觉语言高度一致的科研插图。

下面案例选用与图 1-3 同篇的图 2-9 作为风格参考，并通过相应提示词生成图 2-10。该例所使用的提示词如下：

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

在 2.2 节介绍图生图方法时已经提到，结构草图作为参考图，在科研绘图中承担着确立空间拓扑与逻辑关系的约束作用。用于绘制草图的工具并不存在统一的最优解，真正重要的是能否够快速、明确地表达结构意图。

对于大多数科研人员，草图绘制工具并不需要很复杂。无论是操作系统自带的画图软件，PowerPoint 中最基础的形状与箭头工具，还是 Excalidraw、draw.io 这类轻量级流程或示意图工具，都足以承担这一阶段的任务。在很多情况下，甚至只需要一张 A4 纸和一支笔，就能完成对结构关系的初步表达。

我们使用这些工具的目标并非画出精致图像，而是尽快明确图像的空间布局与逻辑关系。只要结构清晰，线条是否规整并不重要。在图生图阶段，模型会将这些草图视为视觉骨架，并在此基础上完成风格与细节层面的优化。

#### 2\. 配色提取工具

配色提取工具主要用于解决另一个常见问题：如何快速获得已经在高水平科研插图中被验证过的配色方案。

在阅读高水平期刊论文时，我们常常会遇到配色与风格都非常成熟的插图。通过配色提取工具，则可以直接从这些插图中获取对应的 HEX 色值，用于后续的提示词编写或统一整篇论文的视觉风格。

这类工具并非必需，但如果你想复用顶级期刊中常见的低饱和度配色方案，或者需要在多张插图之间保持严格的颜色一致性，这类工具会显著提升你的效率。许多设计软件本身已经内置取色功能。如果你不希望额外安装工具，直接在浏览器中搜索并选取在线的取色工具，同样可以完成这一任务。

### 2.3.2 后处理工具

生成模型输出的图像，通常还无法直接用于正式的科研场景。这类结果往往会存在水印、分辨率不足，或在局部结构与细节上需要进一步修正。因此，后处理工具的作用，在于将 AI 生成的图像逐步整理为可继续编辑、可反复使用的论文插图素材或参考图像，而非简单地对生成结果进行修补。

#### 1\. 去水印工具

部分生成平台会在输出图像中附带水印。在合规使用的前提下，可以借助专门的去水印工具对图像进行处理。

例如，开源项目 gemini-watermark-remover 通过反向 Alpha 混合算法对透明度进行还原，能够在不引入额外视觉噪声的前提下恢复图像的原始结构。此外，一些实用的脚本工具基于 AI Studio 网页端的水印加载机制，通过在图片下载阶段阻止前端水印的加载，从源头避免水印叠加。这一步的目标，是尽可能获得一张干净、稳定的基础素材，为后续处理打好基础。

需要注意的是，由 Nano Banana Pro 生成的图像不仅包含直观可见的 Google 视觉水印，还嵌入了隐形的 SynthID 标记。SynthID 是一种在图像生成阶段将识别信息编码进像素数据深层的数字指纹技术。根据 Google 官方研究，SynthID 在设计时便充分考虑了鲁棒性，能够有效抵御裁剪、缩放、色彩调整与格式转换等常见编辑操作。

官方论文[5]指出，尽管通过大幅压缩、重采样或风格迁移等极端对抗手段，可以在一定程度上降低水印的可检测性，但这类操作往往伴随着明显的图像质量退化。这也是本书不建议将 Nano Banana Pro 生成的图像直接作为论文终稿插图的重要原因之一。

#### 2\. 高清放大工具

在完成去水印之后，常见的下一步是使用高清放大工具。典型的解决方案是部署 Real-ESRGAN 系列模型（支持本地便携版运行）。这一处理的意义在于为后续的矢量化与可编辑化提供更高质量的输入。更清晰的边界与细节，有助于提升线条识别、形状分割与文字保留的效果，从而在后续编辑过程中降低整体修改成本。

#### 3\. 矢量化与可编辑化工具

在论文排版或后续修改阶段，插图通常需要进行细节层面的再调整，例如文字标注的位置微调、线条粗细的统一、局部逻辑关系的修正，以及与整篇文稿中其他图形元素的整体协调。表 2-4 整理了 AI 生成图像后的编辑与矢量化方案。

表 2-4 AI 生成图像后的编辑与矢量化方案

![](img/AI科研绘图：Nano Banana极速实战指南_content_94836_0.jpg)

值得注意的是，上述方案并非互斥的选项。在实际的科研绘图工作流中，你可以根据需求灵活组合多种工具，以兼顾效率与精度。例如，可以先使用在线工具（如 Vectorizer）快速完成位图到矢量图的基础转换，再将结果导入专业软件（如 Illustrator）进行精细的节点调整，最后结合代码生成的图形结构进行整体组装。

无论选择单一工具还是混合工作流，其核心目标都是一致的：让 AI 生成的结果真正融入科研写作流程，而不是停留在“好看的图片”层面。无论是通过在线工具的快速转换，还是借助专业软件与代码的深度重构，最终都是为了实现图像的可编辑化与规范化。

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

这套工作流的设计核心在于关注点分离。阶段一只解决画什么的问题，重点保证结构与逻辑的准确性；阶段二聚焦如何画，强调视觉呈现的稳定性；阶段三用于处理细节层面的修正与完善。

3.2 节~3.4 节将依次对这三个阶段展开说明，并给出对应的操作提示词与实践细节。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 3.2

阶段一：逻辑构建

# 从论文到可控科研插图的生成路径

把学术论文转成可控的结构示意图，可以拆解为三环：先做逻辑拆解，再做图像落地，最后做交互打磨。三个环节的关注点各不相同，串联起来能把生成的不确定性压到最低。

### 结构提炼阶段

这一轮不直接出图，而是让大模型把论文转写成一份交付给绘画模型的 [VISUAL SCHEMA]。产物是一段文字蓝图，需要把空间分区、模块关系与视觉限制写清楚。

在这类任务里，模型更像蓝图规划者。借助强的语义理解与推理，诸如 Gemini、GPT、Claude，以及国内的 Kimi、GLM、DeepSeek 等旗舰系列，都能对论文做重组与提炼，明确插图要锁定的要点、模块划分方式与整体构图思路。

结合前文文生图的经验，本步要求模型输出可直接带入下一步的结构化提示，并在其中显式指定风格与版式。为了压制随机性，提示里常引用本领域的顶会或期刊来定调，同时给出若干常见布局模板供模型挑选。

如果对要绘制内容的逻辑已经很清晰，可把现有的文字说明、流程图、伪代码或笔记与原文一并喂给模型作为构建依据；若对本领域常用的插图版式把握不足，可先收集几篇代表性论文插图，让模型归纳总结，再把得到的版式类型与设计特征补进提示。实践中这能明显拉近输出与领域主流审美的一致性。

下面以计算机视觉与机器学习为例，给出可直接套用的逻辑构建提示词。

# 角色定位

你是一位 CVPR/NeurIPS 顶会的视觉架构师。核心能力是把抽象的论文逻辑转化为具体、可结构化、几何级的视觉指令。

# 任务目标

阅读我提供的论文内容，输出一份 [VISUAL SCHEMA]。该 Schema 将直接发给 AI 绘图模型，因此必须使用强硬的物理描述。

# 阶段一：布局策略选择

在生成 Schema 前，先解析论文逻辑，从以下布局原型中挑选最合适的一个或组合：

1. **Linear Pipeline**: 左→右流向，适用于数据处理、编码解码。
2. **Cyclic/Iterative**: 中心带循环箭头，适用于优化、强化学习、反馈回路。
3. **Hierarchical Stack**: 上→下或下→上堆叠，适用于多尺度特征、树结构。
4. **Parallel/Dual-Stream**: 上下平行的双流结构，适用于多模态融合、对比学习。
5. **Central Hub**: 一个核心模块连接四周组件，适用于智能体-环境、知识图谱。

# 阶段二：Schema 生成规范

1. **动态分区**：按选定布局定义 2-5 个物理区域，不限于 3 个。
2. **内部可视化**：必须为每个区域定义内部的“物体”，如图标、网格、树状图，禁止使用抽象概念。
3. **显式连接**：若为循环过程，必须明确描述 “从 Zone X 弯曲箭头回环至 Zone Y”。

# 输出格式

请严格按以下 Markdown 结构输出：

---BEGIN PROMPT---

[风格与元指令]

高保真科研示意图，技术矢量插画，纯白背景，边界分明，学术教材风格。高分辨率 4k，严格 2D 平面设计，带细微等距元素。

[布局配置]

* **选用布局**：[例如：含 3 节点的循环迭代过程]
* **构图逻辑**：[例如：中心三角反馈回路，四周包围输入输出面板]
* **配色方案**：专业莫兰迪色，Azure Blue、Slate Grey、Coral Orange、Mint Green。

[区域 1：位置 - 标签]

* **容器**：[形状描述，例如左上面板]
* **视觉结构**：[具体描述，例如一叠文档]
* **关键文字标签**："[文字 1]"

[区域 2：位置 - 标签]

* **容器**：[形状描述，例如中央圆形引擎]
* **视觉结构**：[具体描述，例如顺时针环路连接 3 个内部模块：A 齿轮、B 图谱、C 滤波器]
* **关键文字标签**："[文字 2]"、"[文字 3]"

[区域 3：位置 - 标签]

…如需可追加区域 4/5

[连接关系]

1. [描述连线，例如从区域 2 回环到区域 1 的虚线弯曲箭头，标注“反馈”]
2. [描述连线，例如从区域 2 到区域 3 的宽流箭头]

---END PROMPT---

# 输入材料

[论文相关内容]

这里选用“Attention Is All You Need”论文第三章作为输入素材，得到的结构化输出如图 3-1 所示。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94840_0.jpg)

图 3-1 Transformer 架构的 Schema 生成结果（截选）

本书建议：为避免最终成图与逻辑脱节，建议在进入阶段二前，仔细检查 Schema 中的结构与风格描述是否准确。这一步的校准能显著提高出图成功率。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 3.3

阶段二：绘图渲染

逻辑构建完成后，才进入真正的绘制阶段。此时 LLM 已提供包含明确布局与风格约束的 Schema，本阶段提示词的侧重点也从“逻辑定义”转向“结构执行”。

核心目标是遏制模型的幻觉与过度发挥，确保生成结果尽可能精准还原 Schema 中定义的空间位置与几何关系。绘图提示词应重点强调空间布局、视觉元素类型与整体风格约束，同时结合具体学科顶级期刊的审美标准，对画面风格再次确认。

另外需要注意，Nano Banana Pro 在某些情况下会把结构性提示词中的示意文本误判为需要渲染的画面文字。为减少后续文字层面的反复修正，应在提示词中对可渲染文字范围进行明确约束。

以下仍以计算机视觉与机器学习领域为例，给出完整的绘图渲染提示词示例。

**风格参照与执行指令：**

1. **美术风格（Visio/Illustrator 感）：**

生成一份适合顶级计算机科学论文的专业学术架构图，CVPR/NeurIPS 级别。

* **视觉**：扁平矢量图形，分明的几何形状，干净细线描边，柔和莫兰迪填充，Azure Blue、Slate Grey、Coral Orange。
* **布局**：严格遵循下方定义的空间排布。
* **氛围**：技术感、精确、纯白背景。非手绘、非写实、非 3D 渲染、无阴影/渐变。

2. **关键文字限制（务必阅读）：**

* **禁止渲染元标签**：不要在图像内写出“ZONE 1”、“LAYOUT CONFIGURATION”、“Input”、“Output”或“Container”等结构指令词。
* **仅渲染关键文字标签**：只有“Key Text Labels”下用双引号括起来的文字才应出现在图中。
* **字体**：所有标签使用干净有力的无衬线字体，如 Roboto 或 Helvetica。

3. **视觉 Schema 执行：**

把以下结构蓝图转化为最终图像：

[[VISUAL SCHEMA]的全部内容]

把这些提示词交给 Nano Banana Pro 后，就能得到一份可用插图初稿。以图 3-1 中的 Transformer 架构 Schema 作为输入，模型输出结果如图 3-2 所示。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94841_0.jpg)

图 3-2 基于 Schema 生成的 Transformer 架构插图初稿 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 3.4

阶段三：交互迭代

获得初稿后进入交互迭代阶段。虽然整体风格与结构基本达标，但受论文复杂性影响，Nano Banana Pro 未必一次就能呈现所有细节。例如图 3-2 中右侧出现“Residual Conction”拼写错误，且每个残差模块旁都重复堆叠了竖排文字，造成右侧拥挤、干扰信息传达。因此有针对性的修改与优化不可或缺。

从实践看，常见问题归为两类：整体布局正确但局部细节或风格有瑕疵；整体布局本身错误。本书推荐的核心原则是尽量减少无意义的重绘，充分利用 Nano Banana Pro 的自然语言编辑能力，对已有结果进行精确修改。

#### 1. 整体布局满意，但细节或风格有瑕疵

当插图的整体结构、模块关系与空间布局已满足预期，仅局部元素、配色或视觉风格存在问题时，建议采用自然语言编辑策略修正。

在 Google AI Studio 或 Gemini 网页端等多轮对话环境中，若图片仍在对话上下文，可直接在对话框输入修改指令，如图 3-3；若图片不在上下文，或在 API 等非交互调用场景，可通过图生图方式将当前结果作参考图再次输入模型编辑，如图 3-4。无论哪种方式，模型都能在保持主体结构稳定的前提下，对指定元素进行针对性修改。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94842_0.jpg)

图 3-3 在多轮对话式环境中修改图像细节

![](img/AI科研绘图：Nano Banana极速实战指南_content_94842_1.jpg)

（a）

![](img/AI科研绘图：Nano Banana极速实战指南_content_94842_2.jpg)

（b）

图 3-4 利用图生图方式修改图像细节

（a）上传待处理图像；（b）输入修改指令

表 3-2 汇总了科研绘图中常见的自然语言编辑指令示例，涵盖图标替换、颜色调整、风格统一与文字修正等高频需求。蓝色斜体部分为可变参数，需按实际情况代入。

表 3-2 常见的自然语言编辑指令示例

![](img/AI科研绘图：Nano Banana极速实战指南_content_94842_3.jpg)

需要额外说明的是，尽管 Nano Banana Pro 在文字渲染上已有明显提升，但在复杂结构或多标签场景下仍可能出现拼写错误或字体异常。若文字错误集中且影响观感，更稳妥的做法是直接移除图中所有文字标签，后期通过矢量编辑或排版软件统一添加说明。

本书建议：由于每次生成与修改都会叠加新的水印层，多层堆叠会让后期修复困难。建议参考 2.3.2 节介绍的去水印工具，在每次生成或修改完成后立即去除当次可见水印，避免累积。

#### 2. 整体布局错误

若生成结果在结构层面明显偏差，例如本应为循环关系的流程被画成线性，或关键模块因果关系被错误表达，此时不建议继续局部修补。

这类问题通常源于阶段一生成的 Schema 在结构描述上存在歧义或信息缺失。更有效的做法是回到阶段一，检查与修订 Schema，重点确认是否选用了合适的布局策略，即 3.2 节 Schema 输出的 [LAYOUT CONFIGURATION] 部分，以及各 Zone 对模块关系与空间组织的描述是否足够明确。

实际操作中可直接与 LLM 交互，要求其基于修正意见重新生成 Schema 作为新的结构蓝图，再重新执行阶段二的绘图渲染流程。这种方式往往能从根本上解决结构性错误。

#### 3. 关于多次生成与抽卡策略的有效性说明

AI 绘图中用同一提示词生成多张结果筛选是一种常见操作。需要说明的是，Nano Banana Pro 在图像编辑与结构保持方面已具备较高稳定性。当一张插图在整体结构和风格上已达到较高完成度时，并不建议频繁重新生成。

在本书推荐的提示词体系下，整体布局与视觉风格已被严格约束，多次生成间的差异多集中在线条走向、局部元素配色或细节处理，不会对整体结构产生显著影响。若已预先确定配色方案或风格规范，更高效的方式是基于当前结果，通过自然语言指令对局部细节进行定点修改。

图 3-5 展示了相同提示词条件下生成的两张插图，结果在整体布局和视觉语言上差异并不显著。因此在实际流程中，结构性或幅度较大的调整更适合通过优化阶段一的提示词完成；细节微调则可直接通过自然语言编辑完成。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94842_4.jpg)

（a）

![](img/AI科研绘图：Nano Banana极速实战指南_content_94842_5.jpg)

（b）

图 3-5 BERT 模型架构示意图。

（a）论文中给出的第一种架构绘制结果；

（b）在相同提示词条件下生成的另一种结果 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 3.5

小结

本章围绕科研绘图实际需求，系统介绍了一套从论文内容到可控结构示意图的完整方法流程。通过将逻辑构建、绘图渲染与交互迭代分阶段处理，可有效降低科研插图生成过程中的不确定性，并提升整体效率。为帮助直观复盘要点，图 3-6 将本章核心内容整理为全景式知识脉络图。

需要再次强调，本章产出的成果定位为 AI 生成的科研插图素材，仍处于可编辑、可优化的中间阶段。如何将这些素材进一步提升至可直接用于投稿的发表级插图，将在第五章结合后处理工具与实践经验展开详细讨论。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94843_0.jpg)

图 3-6 第三章知识脉络图 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

# 第四章

实战篇：不同领域的提示词策略

在上一章里，我们已经把从论文内容抽取可控草图、再交由模型生成出版级成图的完整链条搭好，并以计算机视觉与机器学习为例，演示了逻辑构建与图像渲染两步提示如何协同提效。

不同学科的视觉语法差异显著。计算机领域偏好抽象的拓扑与流程图，材料化学强调可感知的微观实体与能带行为，生物医学则更关注柔性有机环境和信号传递的空间关联。本章将视角拓展至材料与化学、生物与医学两大核心方向，结合各自的学科习惯拆解提示词设计思路。针对难以全部覆盖的细分领域，章节末尾还提供了一套基于参考图的提示词定制方法，助你建立专属体系。EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 4.1
材料与化学：微观机制可视化

材料与化学类图解对物理实体的依赖程度很高。Nature Materials、JACS、Angewandte Chemie 等顶刊的插图，实质是把微观运行规律转译为直观图示，常呈现多尺度融合与结构决定性质的风格。常见流派汇总于表 4-1。

表 4-1 材料与化学领域插图的典型风格流派

![](img/AI科研绘图：Nano Banana极速实战指南_content_94845_0.jpg)

基于上述特征，方案构建阶段应引导模型以材料学视角规划版面，渲染阶段则侧重区域划分的合理性、微观结构的视觉权重以及反应指向的箭头清晰度。

下面给出面向材料与化学定制的双阶段提示词模板。

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

图 4-1 展示了利用上述提示词处理文章“Water-in-salt electrolyte enables high-voltage aqueous lithium-ion chemistries”的实战效果。模型准确捕捉了文中关于溶剂化结构与 SEI 膜形成的核心逻辑，并生成了直观示意。

需要特别说明的是，当前 AI 绘图模型在分子拓扑结构、晶体构型及键长比例等方面的准确性仍难以完全保证。为确保插图的严谨性，在实际科研绘图流程中，建议将精确的分子式与晶体结构通过 ChemDraw、VESTA 等专业软件单独生成，并在 5.3 节所述的矢量化阶段进行替换与校正。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94845_1.jpg)

图 4-1 基于 Schema 生成的高浓度水系电解液微观与界面机制示意图 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 4.2
生物与医学：通路场景化

以 Cell、Nature、Science 为代表的顶刊，视觉语言极强调微环境与通路逻辑。与材料科学不同，生物医学插图需处理柔性非规则的有机形态，并清晰划分细胞内外空间。典型风格流派见表 4-2。

表 4-2 生物与医学领域插图的典型风格流派

![](img/AI科研绘图：Nano Banana极速实战指南_content_94846_0.jpg)

下面给出 BioRender 风格的双阶段提示词模板，适配生物与医学领域。

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

1. [描述反应过程, e.g., A glowing arrow from the Receptor (Zone 1) to Protein A (Zone 2) indicating signal activation]

2. [描述抑制关系, e.g., A red line with a flat head extending from Drug X to Protein B indicating inhibition]

3. [描述空间移动, e.g., A dotted swooping arrow showing Protein B moving into the Nucleus (Zone 3)]

---END PROMPT---

# Input Data

[在此处粘贴你的论文内容]

#### 2. 绘图渲染

**风格参考与执行说明：**

1. **美术风格（BioRender / 医学插画）：**
产出一张可达 Cell / Nature 级别的常规生物通路图。
* **画面：** 采用干净的矢量绘制并辅以平滑渐变。
* **形体：** 以圆润、有机形态为主，边缘柔和，避免硬朗几何块。
* **配色：** 选用专业生物淡彩，如膜质米色、DNA 蓝、胞质粉。
* **整体氛围：** 教学感强、通透易读，近似教科书水准。

2. **关键文字规范：**
* **可读性：** 标签置于浅底时需清晰可辨，文字偏深。
* **字体：** Arial 或 Roboto 粗体。

3. **视觉方案落地：**
将下方结构蓝图转化为最终图像：
[在此处直接粘贴 Step 1 生成的 ---BEGIN PROMPT--- ... ---END PROMPT--- 内容]

图 4-2 把该提示词用于论文“Ferroptosis: a regulated cell death nexus linking metabolism”后得到的结果，能够直观呈现铁死亡相关代谢通路与细胞器之间的生化诱导机制。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94846_1.jpg)

图 4-2 基于 Schema 生成的铁死亡诱导机制插图 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 4.3

参考图驱动的提示词定制方法

前文已覆盖计算机、材料、生物三大方向，但科研细分领域极为庞杂，单一模板难以面面俱到。此时容易联想到第二章的图生图技巧，然而科研制图中单纯风格迁移往往难以守住复杂的拓扑逻辑，手绘草图控制又对布局能力要求较高。化解矛盾的关键还是回到第三章的“逻辑构建+绘图渲染”工作流。

在此基础上，可充分利用当前多模态大模型的图像理解能力，让模型对目标领域的参考图进行逆向解析，提炼出隐含的布局逻辑与视觉风格，再结合该领域自身的表达范式，对既有基础提示词做针对性微调。这样便能快速获得兼具参考图视觉特征与领域逻辑的定制化提示词。

该方法的优势在于精度高：避免了仅靠宽泛文字描述带来的模糊，确保生成结果在布局与风格上与参考图高度一致；普适性也强，基本适用于各学科。下面以第三章计算机领域提示词为基础的微调示例帮助理解。

图 4-3 展示了该提示词的使用方式与输出示例。

你是一名资深提示词工程专家，熟悉学术论文插图的生成规律，对计算机领域以及【你所在的领域名称】领域的研究范式、图示习惯与视觉表达都有系统认知。

我会提供一张目标领域的成品插图示例，它代表我期望最终结果应遵循的整体风格。请你对该示例做逆向分析，重点关注整体布局、信息层级、模块空间关系、配色方案及其信息作用、图形元素的抽象程度与表达习惯。

在此基础上，请对下面给出的两条提示词分别做微调优化，使其在使用时能稳定生成与示例在视觉风格与表达逻辑上高度一致的插图。

注意，这两条原始提示词原本服务于计算机论文内容的抽取与示意图绘制。你的任务是将其调整为适用于【你所在的领域名称】的版本。请保留原提示词的整体结构、步骤逻辑与控制维度，仅结合【你所在的领域名称】常见的布局特征、学科重点与视觉习惯做针对性细化与替换。

最终输出应为两条可直接使用的完整提示词，风格与目标示例一致，内容表达自然适配【你所在的领域名称】。

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

本章聚焦不同领域的逻辑构建与绘图渲染提示词的构建方法。需要提醒的是，即便获得了适配自有领域的两类提示词，仍要回到 3.4 节的第三阶段：交互迭代。定制提示词的首版常在细节上出现偏差，需要借助该阶段方法识别并修正明显错误。为便于复盘，图 4-4 将本章要点整理为全景知识脉络图。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94848_0.jpg)

图 4-4 第四章知识脉络图

至此，你已能用 AI 生成布局合理、风格尚可的初稿。但这通常只是起点，还远未达到科研论文的严谨要求。第五章将进一步探讨科研绘图的精准控制与体系化构建，实现从“可看”到“可用”的跨越。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

# 第五章

进阶篇：科研绘图的精准控制与体系化构建

第 2~4 章已系统介绍了如何借助生成模型与工具，高效获得结构清晰、语义正确的科研插图初稿。从工具认知到完整工作流再到领域示例，你已具备独立生成的基础能力。

但在实际写作与投稿中，仅有一张“看起来不错”的图远远不够。随着论文修改、审稿往返，插图需要频繁调整、扩展，并在不同论文与汇报中复用。若结果无法持续编辑与稳定复用，初期效率优势很快会被后期维护成本抵消。

因此本章致力于搭建高标准的科研绘图体系。重点不再是单次生成，而是突破提示词边界，通过精确控制、模块化拆解与标准化处理，将 AI 的随机产出转化为严谨、合规、可长期复用的科研资产。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 5.1

重新拿回科研绘图的控制权

生成模型的最大优势是能在极短时间内给出结构完整、信息充分的视觉方案。但同时也隐藏一个易被忽视的风险：后续修改的可控性不足。

初稿阶段问题不明显，但当使用场景从“生成初稿”转向“反复修改”时，隐患会显现——模块比例难精调、局部结构无法单独调整、多图风格易不一致。这些问题在论文修改期会被放大，大幅抬高维护成本。

因此生成之后的第一步，不是继续堆砌画面细节，而是重新接管控制权。控制主要体现在两方面：一是对生成指令本身的再加工，二是通过更直接的方式约束模型的视觉理解。

### 5.1.1 指令层的结构控制

第三章曾建议在进入阶段二前仔细检查 LLM 生成的 Schema，并在交互中按需修改。因为 Schema 是最终交给 Nano Banana Pro 的核心提示。引入 LLM 的初衷是帮助研究者快速外化图像整体布局与模块关系，尤其在拓扑结构尚未清晰时。

但拿到可用初稿后，情况会变化。随着对内容与用途的反复推敲，你对“图最终应是什么样”会形成更明确判断，包括结构取舍、信息层级与整体视觉风格。这时完全依赖模型自动生成的 Schema 反而会限制表达。更合理的做法是基于已建立的认知，对 Schema 有意识地重写与精修，使其服务于设计意图。

重写不仅限于结构的增删调整，还应覆盖初始生成易忽略、但对正式科研插图至关重要的约束，其中色彩维度常被低估却极关键。

在进阶语境中，色彩不再只是审美，它承担信息区分与语义映射功能。因此重写 Schema 时，建议将色彩管理纳入提示设计。具体做法是从顶级期刊或高质量论文插图中提取已被验证的 HEX 色值，构建固定色盘，并在 Schema 中明确规定各模块、流程或概念对应的颜色编码，参数化控色详见 2.2.1 节。

这些 HEX 值可用取色工具从图中采集。若需批量提取文献插图的 HEX 色值分析，也可通过开源项目 colorgram.py 本地识别导出。

一旦参数化色彩约束写入 Schema，就成为模型生成时必须遵守的硬性条件。它不仅提升单图专业度，更能保证系列图或多张图表中同一概念保持一致的视觉指代，形成连贯可信的视觉语言。这是插图从“好看”走向“符合期刊审美规范”的分水岭。

### 5.1.2 视觉层的生成控制

即便多轮优化，语言描述仍有边界。对某些复杂空间拓扑或对视觉张力要求高的示意，再精细的文字也难以完全复现脑中设想。

第三、四章主要用文本引导 Schema，实践中你会发现模型仍难理解某些非典型结构，或多图间风格不统一。此时继续在文字框里微调形容词收效有限。

更有效的策略是引入更直接的视觉约束，结合 2.2 节的图生图与文生图方法，通过草图与风格参考图明确引导。

#### 1. 用草图强制纠偏拓扑结构

生成初稿时，常出现模型顽固地把“输入层”放错位置，或始终无法正确连接两个模块。这类问题多源于模型空间理解的固有倾向，而非提示不够精准。

此时最直接的解法是停止纠结文字，直接画出来。正如 2.2.2 节所述，只需在纸上或白板工具中快速勾勒模块相对位置与连接逻辑，把这张略显潦草的草图作为骨架输入模型。该类视觉输入在生成中的优先级高于文字，能显著提升拓扑服从度。

#### 2. 用风格参考图明确期刊级视觉取向

第三、四章给出多组基于领域归纳的提示范式，能在多数情况下生成符合习惯的示意图。但领域常见风格并不等同于具体期刊或会议的视觉审美。即使主题相同，不同期刊在配色、线条与构图密度上仍有稳定差异，这些差异很难仅靠抽象文字描述。

因此当目标期刊明确，或对期刊视觉要求较高时，仅靠通用领域提示往往不足。更有效的做法是直接引入风格参考图，明确期刊层面的视觉取向。

具体可从目标期刊已发表论文中挑选一张类型相近且视觉效果理想的插图，作为风格参考输入生成流程。模型据此能在笔触密度、配色策略与整体视觉节奏上获得明确约束，产出更贴合期刊审美的结果。

该方法也适用于小众或未被前述章节覆盖的方向，通过风格参考图反向提炼定制化提示词。

灵活运用上述视觉控制手段，可突破纯文字指令瓶颈，获得更符合预期且质量稳定的生成结果。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 5.2

复杂长图的模块化思维

第二章已展示 Nano Banana Pro 对结构的理解力，但 AI 在长时记忆与全局空间规划上仍有客观限制。当插图复杂度逼近或超过单一视图承载范围时，无论是尺寸拉伸还是信息密度叠加，都容易导致逻辑组织与空间布局失稳。此时靠一条不断膨胀的提示词一次性完成整图生成，往往难以稳定且可复用。

因此研究者需主动调整策略，将整体复杂任务拆解为若干边界清晰、结构明确的生成单元，再由人工整合校正。

### 5.2.1 案例分析

第 3、4 章的多次实战表明，当前模型能力下可用一段详尽提示词一次性输出看似复杂的长图。但细看会发现，宏观上虽能贴合目标风格与主流程，微观逻辑与关键语义细节却易积累隐蔽问题。

更重要的是，这类问题在后续修改中并不友好。由于元素已在整体画面中嵌套，局部校正常牵连其他区域，调整成本显著上升。

以图 5-1 为例，该图采用第三章工作流，以论文“Tree-KG:An expandable knowledge graph construction framework for knowledge-intensive domains”的 Methodology 章节为输入绘制，结果 Schema 详见附录。初看版式规整，左中右三区划分清晰，右侧的三维分层结构也具备一定完成度。

图 5-1 所使用的 Schema 内容如下：

1. Global Layout （全局布局）

Structure: Left-to-Right Progressive Pipeline. The flow transitions from rigid structural extraction to dynamic semantic expansion, culminating in a layered topology.

Background: Clean white canvas with a faint, technical isometric grid (light grey, 10% opacity) to support the 3D aspects of the graph layers.

2. Zone Definition （区域定义 - 核心）

Zone 1 (Location: Left):

Container Label: "INITIAL SKELETON CONSTRUCTION (Sec 3.2)"

Shape: A tall, vertical rectangular panel with a light blue tinted background.

Internal Structure:

Top: A stack of generic "Textbook/Corpora" icons.

![](img/AI科研绘图：Nano Banana极速实战指南_content_94851_0.jpg)
一次成图 Tree-KG 总览：满是标签却难读懂

上部出现正则解析齿轮在处理文本，下部是层级清晰的目录树从书名逐级展开到章、节、小节，并点出文本分段、目录提取、显式关联等要点。中间虚线框起的迭代扩展引擎 Sec 3.3 由三个模块按三角环流排列：顶部节点精炼呈现中心节点以光晕吸收邻居向量；左下实体优化展示节点簇合并为核心实体或非核心实体附着父节点；右下链接预测逻辑对应公式1，以虚线连结两节点 u、v，上方悬浮余弦波、三角形、路径三种度量图标。相关要点为语境卷积、边评分 AA+CA、实体聚合。右侧 Tree-KG 拓扑 Sec 3.1 置于类玻璃容器，多层结构 V1…Vk 中，粗实竖线表层级边 E1，细彩横线表同层关系边 E2，关键词为垂直层级、水平关系、分层知识。流程上宽灰箭头“骨架初始化”自第一区进入第二区，第二区内部三模块环形箭头体现迭代，最后双线箭头“结构整合合并”流向第三区。

初看此类图容易产生作图已完成的错觉。回到论文使用场景，一次性生成的高复杂度配图往往暴露两大缺陷：内容堆砌与语义表达脱节。

#### 1. 内容堆砌
图5-1 里堆满了标签，正则解析装置、语境卷积等都被直接写进画面。生成时模型倾向完整复现提示词，把本该用视觉抽象承载的部分转成文字堆叠。归纳力因此下降，观感更像结构化笔记而非符合科研审美的配图，与 1.3.1 节提及的美学准则相悖。

#### 2. 语义与视觉编码脱节
整体布局尚可，但在需严格表达结构关系的局部仍有偏差。以右侧多层知识图为例，原论文要求随着规模扩张，层内与层间均保持明确连通。生成结果对关系多停留在暗示，缺乏稳定可识别的视觉编码。具体表现为层内节点与连线被弱化，跨层映射不清，节点颜色语义不统一，“多层图”“水平关系”等标注对应也出现偏差。这并非局部失误，而是复杂结构在整体生成中难以持续精确映射到视觉符号体系所致的系统性偏离。

依 5.1 节做法可调 Schema 或加严视觉层控制来修补，但元素高度耦合的复杂图中，局部改动极易引发连锁反应，稳定性进一步恶化。因此事后修补难以根治，绘图思路本身需转变。

###5.2.2 复杂图形的拆解与生成
科研写作中真正需要模块化策略的复杂图，集中在两类：信息量大的长卷图，和由多个子图构成的复合大图。

####1. 信息量大的长卷图
此类用于展示完整算法流程、系统架构或多阶段分析路径，信息链条长、模块多，对前后关联表达精度要求高。复杂度已超单一视图稳定承载上限，图5-1 即为典型。

更稳妥的做法是先在逻辑上将整张长图拆成输入、处理、输出等相对独立的子模块。对每个子模块分别采用 5.1 节已验证的 Schema 与视觉约束生成，只在局部追求结构正确与语义清晰，避免复杂度在单次生成中集中爆发。

####2. 由多个子图组成的复合型大图
以图5-2 常见的图 A、B、C 组合为例。版面被拆分，但在审稿语境里仍视为整体。难点在于不同子图承担不同层级、不同抽象程度的表达任务。复合大图中可能同时出现应用场景、结构示意、工作原理，视觉风格天然不同，子图 A 可能是 2D 抽象示意，子图 B 是 3D 结构拆解。若整体生成，模型常在风格约束间妥协，每部分表现力被稀释。
![](img/AI科研绘图：Nano Banana极速实战指南_content_94851_1.jpg)
图5-2 融合不同视觉风格的复合型插图案例[6]

因此更适合在生成阶段把每个子图当作独立模块分别绘制，让每个子图在其最合适的视觉范式下完成表达，再用统一风格约束及人工排版做整体整合。

###5.2.3 基于母图的风格锚定
拆解后新的挑战是合成时局部结果的风格统一度。第一张满意的图完成后，后续重点从创造转向对齐。仅靠“扁平化设计”或“某会议风格”这类泛化标签很难保证统一。

更有效的做法是用你最满意的那张作为母图引入后续流程。图生图时要求模型严格参照母图的笔触密度、配色策略、线条粗细及整体视觉节奏。这种基于具体图像特征的风格锚定，能显著降低模块化生成时的风格漂移。母图在模块化绘制中不仅是风格示范，更是项目级的视觉基准，保证无论怎么拆分、顺序如何变化，最终拼合的整图仍保持稳定统一的视觉语言。

###5.3 图像结果的矢量化
5.2 节已讨论模块化应对复杂插图不稳定。无论是复杂长图还是复合大图，最终都需回到 PPT、Illustrator 等环境手动整合。整合可基于位图也可基于矢量，矢量化并非技术必然。但本书建议所有模型产出的插图都应矢量化并手动修正调整，这是科研规范、后续使用及编辑需求共同决定的。

###5.3.1 为什么必须进行矢量化
矢量化的首要动因不是美观或效率，而是科研规范。模型位图即便内容正确，其生成过程也难以满足论文对作者实质性参与绘制的要求。矢量化带来的重绘、重构、手动编辑，让插图从“模型输出”变为“作者主导完成”，在伦理、署名层面建立清晰边界。

这种重绘是实质再创作。线条重组、模块边界明确、文字重排，都意味着作者对图像进行了可以被检验并回溯的手动干预，这是智能辅助绘图被期刊接受的关键前提。

矢量化同时解决位图在科研写作中的现实问题。清晰度上，模型产出的文字常以位图嵌入，缩放后易模糊变形。矢量重排的文字及线条可在任意分辨率下保持清晰一致。修改便利性上，论文反复修改时插图常需局部调整名称、位置、线宽等，矢量对象可独立选取、精确移动，修改成本极低。

因此矢量化在流程中承担三重角色：合规层面的必要转换，清晰度、版式稳定性保障，支撑长期修改及复用的基础条件。

###5.3.2 矢量化路径
矢量化没有唯一标准，具体方式取决于工具熟练度、插图复杂类型及修改频率。实践中可行的路径大致分三类，表5-1 从核心优势、主要局限、推荐场景做横向对比。
![](img/AI科研绘图：Nano Banana极速实战指南_content_94852_0.jpg)
####1. 基于位图参考的手工重绘
若熟练使用 PPT、Figma、Visio 等，可将模型高清位图作为底图参考，手绘关键结构。初期耗时，但所得图形全由原生矢量构成，层级明确、逻辑清晰，后续局部修改、整体调整、跨版本复用成本都低。尤其适合流程图、系统架构图及逻辑关系明确的示意插图。

####2. 基于工具的自动描摹
若熟悉 Illustrator 等专业软件，可用图像描摹把位图快速变路径。色彩边界清晰、风格扁平的图效率较高。以 Illustrator 2025 为例，操作在“图像描摹”面板进行。图片导入后点“图像描摹”，预设选“高保真度图片”加载默认彩色追踪，手动调参建议颜色20%、路径50%、边角30%、杂色10px，预设切为“自定”。处理完依次点“扩展”与“取消编组”，即可对矢量对象单独编辑。图5-3 所示为操作面板，图5-4 为基于上述参数的描摹结果。
![](img/AI科研绘图：Nano Banana极速实战指南_content_94852_1.jpg)
（a）
![](img/AI科研绘图：Nano Banana极速实战指南_content_94852_2.jpg)
（b）
![](img/AI科研绘图：Nano Banana极速实战指南_content_94852_3.jpg)
（c）
![](img/AI科研绘图：Nano Banana极速实战指南_content_94852_4.jpg)
（d）
图5-3 Illustrator“图像描摹”面板（a）选择高保真度图片 （b）设置推荐参数（c）点击拓展（d）点击取消编组
![](img/AI科研绘图：Nano Banana极速实战指南_content_94852_5.jpg)
图5-4 基于 Illustrator 的图像描摹结果

自动描摹的结构仍偏粗糙，对文字识别效果有限，通常仍需手动清理重组。效果对参数和图像风格高度敏感，需反复微调。若无 Illustrator，也可用 Vectorizer 等在线工具做基础矢量化，但在精度和结构可调性上不及专业软件。建议描摹前先用高清放大工具如 Real-ESRGAN 系列预处理原始位图，分辨率越高、边缘越锐，描摹路径越平滑贴合。

####3. 基于 AI 识别的结构化重建
近年出现一些探索性工具，如 Edit-Banana、Paper2Any 等。它们不把生成图当纯图像，而是通过实例分割结合视觉大模型、OCR 把图片解析为文本、形状、连接关系构成的结构化对象，再导出可编辑格式。目前仍处探索早期，稳定性和可控性不足以完全替代手动重绘或半自动流程，但方向值得持续关注。

###5.4 构建你的科研绘图素材库
熟练掌握第2~4 章流程后会发现，耗时最久的不是单次作图，而是重复从零开始。把验证有效的中间成果系统沉淀为可复用资源，能显著降低边际成本。素材库可从三个维度构建：提示词模板、高质量科研插图示例、矢量元件的长期积累。

####1. 提示词模板
经过反复调试定型的 Prompt 不是单次使用的指令，而是逐渐贴合个人审美及研究方向的定制化模板。第3、4 章给出的是通用范式，完整满意的一套插图完成后，这套 Prompt 已内化了你对风格及结构表达的个人偏好，价值远高于通用示例。因此建议单独保存完整 Prompt，并抽离出视觉风格、版式组织、表达习惯的核心描述。面对新主题时只需替换关键概念及对象名称，即可快速复用已被验证的表达框架，避免每次重新摸索模型边界。

####2. 高质量科研插图示例

在日常的作图练习和文献阅读里，你会频繁碰见大量高水准的科研配图。风格明晰的参考图、辨识度高的配色组合、以及实战中表现稳定的版面安排，都值得留意。这一类素材具有持续的参考价值，有意识地积累会带来长远收益。

具体操作可以把值得借鉴的插图单独归档，作为风格或色彩对照使用；把其中成熟的配色提炼出来并记下 HEX 编号。那些用智能绘图工具生成后效果不错的稿件，也建议一并入库。随着储备不断增多，会逐步建立起贴合个人研究方向的专属参考库，为后续新项目的风格选择提供可靠依据。

#### 3. 矢量元件的长期积累

依照 5.3 节的矢量化流程处理，通常能得到一批结构清晰、表达规范的图形单元，比如神经网络示意、标准化模块或系统图标。经过人工校对和语义确认后，这些单元完全可以沉淀为可复用的科研绘图组件。

建议把这些矢量元素从原图剥离，按功能或语义分类保存。积累一段时间后，它们会形成属于自己的标准化元件库。之后无论是手工微调时快速调用，还是作为智能生成时的参考底图，只需搭建新的整体框架，再组合这些成熟组件，就能在很短时间内产出一张风格统一、逻辑严谨且符合期刊要求的科研插图。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 5.5
小结

本章聚焦如何把 AI 绘图从偶然生成变为可控创作。先用 Schema 重构和视觉限制取代盲目接受随机结果；再靠模块拆分和矢量转换化解复杂长图的逻辑失控与投稿合规风险；最后把检验过的成果沉淀为素材库，形成单次产出到持续复用的效率回路。

掌握这些手法后，智能工具不再只是画图程序，而是一套完整严谨高效的科研视觉生产体系。

为了方便复盘进阶路径，图 5-5 把本章要点梳理成全景脉络。

![](img/AI科研绘图：Nano Banana极速实战指南_content_94854_0.jpg)

图 5-5 第五章知识脉络图 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

# 第六章
规范篇：AI 辅助绘图的伦理边界与合规路径

第 2 到 5 章已经系统演示了如何借助 Nano Banana Pro 辅助科研绘图，并通过案例说明了其在结构设计、布局规划和效率提升方面的价值。但当智能工具真正进入科研表达环节，技术能力已不再是唯一考量。科研配图始终受伦理规范、学术责任和出版规则的共同制约。

本章不讨论如何更快使用工具，而是回到更根本的问题：在当前学术环境下，智能绘图的使用边界在哪里，哪些做法被明确禁止，又该如何在合规前提下合理引入辅助流程。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 6.1
AI 科研绘图的伦理红线

能否使用智能绘图，关键不在于工具本身，而在于图像在科研论证链条中的定位。正如第一章提出的分类框架，科研插图并非同质，不同类型在信息属性和证据价值上差异很大。规范层面，这种差异会直接变成可接受与否的分界线。

表 6-1 依据当前主流学术规范和期刊审稿实践，对不同类型科研插图的智能使用伦理红线与合规操作做了系统对照，给出了明确结论。

表 6-1 基于科研插图分类的 AI 伦理红线与合规操作对照表

![](img/AI科研绘图：Nano Banana极速实战指南_content_94856_0.jpg)

需要强调的是，表 6-1 划定的不是技术可行边界，而是学术责任能否承担的边界。当图像直接参与结论成立时，其生成与处理必须完全可追溯；当图像仅用于辅助理解和概念表达时，智能工具才有相对清晰可控的使用空间。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 6.2
从期刊政策看 AI 科研绘图的合规边界

在法律与出版实践中，学界已形成比较稳定的共识：完全由生成式模型自动产出且缺乏人类实质性介入的图像，很难被视为符合科研出版要求的原创表达。

这一判断源于著作权制度对作者主导性、创造性判断和责任可追溯性的基本要求。国际出版伦理委员会 Committee on Publication Ethics，COPE 指出，智能工具不具备人类作者的原创意图，也无法承担署名和版权责任，因此作者必须对论文中所有图像承担完整的学术与法律责任。

具体执行时，各期刊和出版集团在表述和适用范围上略有差异，但导向高度一致：都对直接使用生成式模型产出的图像持严格限制，并以“是否具备可核查的人类创作路径”为核心判断标准。表 6-2 汇总了主要国际期刊与出版集团在 AI 生成图像方面的公开政策，供参考。

表 6-2 主流国际期刊对 AI 生成图像的声明与使用要求概览

![](img/AI科研绘图：Nano Banana极速实战指南_content_94857_0.jpg)

在这种政策背景下，把生成式模型直接输出的位图作为科研插图投稿，会在版权归属和出版合规两方面同时面临风险。图像难以满足原创性要求，也缺乏清晰的责任主体，容易成为编辑与审稿的焦点问题。

因此，第五章提出的矢量化重绘流程在投稿语境下具有明确的合规价值。通过对结构、布局和视觉表达的系统重构，作者把图像转化为经人类判断、选择和修正的科研表达，创造性决策和科学责任得以清晰归属作者本人，从而满足期刊对原创性与责任主体的基本要求。矢量化重绘不仅是画质优化，更是连接智能辅助流程与科研出版规范的关键合规环节。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 6.3
投稿阶段的透明披露与自查建议

完成第五章的矢量化重绘、人工审核与修正后，图像在当前主流国际期刊的规范框架内通常已具备较高合规安全性。此流程中智能生成内容仅作为构思阶段的辅助草图，用于参考整体布局或配色，最终发表的图像由作者主导完成并承担全部学术责任。

在此前提下是否需要披露智能工具使用，不同期刊要求并不完全一致。部分期刊明确要求披露工具名称与用途，但在位置和细节上仍有差异。因此投稿前应以目标期刊最新的作者指南为准，避免因理解偏差带来合规风险。

为求稳妥，建议在致谢、方法部分或图注中简要明确说明智能工具使用情况。声明应包含工具名称、版本信息及具体用途，如用于构思、结构草图或配色参考，并明确所有科学判断、结构设计与最终图像内容均由人类作者完成并负责。

可参考的英文声明模板：

The authors acknowledge the use of Nano Banana Pro(powered by Google Imagen 3)for the initial conceptualization and color palette selection of Figure[X].The final figure was manually redrawn as a vector graphic and verified by the authors to ensure scientific accuracy and compliance with publication ethics.The authors remain fully accountable for the integrity of the work.

正式投稿前，建议从编辑与审稿人视角对全部插图进行系统自查。为每张图像建立原始材料包，完整保留无压缩的原始文件、绘制过程记录及关键中间版本。对曾使用智能工具辅助的插图，可提前准备好简要说明文本与操作记录，以便在被询问时迅速清晰回应。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==

## 6.4
小结

本章从伦理红线、期刊政策和投稿实践三方面系统梳理了智能科研绘图在学术体系中的合规边界。随着期刊政策和出版伦理逐渐明晰，合规与否不再取决于工具来源，而取决于生成路径是否可追溯、责任是否清晰归于作者。

在此前提下，本书倡导的“智能工具仅作为科研绘图前期辅助而非直接产出工具”路线，与当前主流期刊审核要求高度契合。将规范转化为操作习惯，把风险控制前置到创作过程，是当下更现实也更稳妥的实践策略。

为便于复盘本章要点，图 6-1 将核心内容整理为全景知识脉络图。

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

关于 Nano Banana Pro 辅助科研绘图的系统探讨至此接近尾声。

翻阅整本书，从入门操作和实用技巧开始，逐步拆解各学科场景的高频提示词，延伸到进阶玩法，最后收束到以矢量重绘为核心的合规实践路线。所有论述的出发点，都是在繁琐的绘图任务与严谨的学术表达之间搭起一套可落地的效率体系。

我们正处技术爆发期。生成式模型能力不断提升，算力与算法的融合正在重塑科研工作的组织方式。除本书呈现的工作流外，许多研究者也在探索基于智能的绘图自动化，或尝试用科研 Agent 重构流程。这一趋势表明，曾长期困扰科研人员的重复性机械劳动正在被技术缓解。

然而工具演进越快，使用者的定力越显珍贵。必须清醒认识到，无论工作流多智能、交互多便捷，在当前及可预见的未来，智能系统仍存在不可忽视的黑盒属性与幻觉风险。它能生成复杂精致的图像，却无法理解图像承载的科学假设；能提供丰富表达方案，却无法为研究结论真实性负责。

因此，在科研绘图环节，使用者和决策者永远只能是人类。这不仅关乎成果准确性，更关乎科学交流赖以成立的信任基础。

对研究者而言，真正重要并非对具体工具的技巧性掌握，而是形成持续有效的专业判断能力。用审慎客观的态度驾驭智能系统，既善用其长提升效率，又深知其短，用严谨验证填补算法漏洞。

展望未来，期刊政策与审查机制会随技术变化而调整，但围绕透明披露、责任明确与科学可控形成的基本原则，预计将长期稳定。

愿本书不仅成为你案头提升效率的工具指南，更成为一道助你在学术规范安全区内自由探索的防火墙。在追求科学真理的路上，让智能系统成为得力助手，同时始终将科学判断与创造的主动权牢牢掌握在自己手中。 EXr6287u6NNVz+GYLY+7SDk226FstKIHdC0bNoQkB2Nqi1xSR0yxPmwrTs5gRi/BtfSbid6dBhii9q7cz2pIyA==