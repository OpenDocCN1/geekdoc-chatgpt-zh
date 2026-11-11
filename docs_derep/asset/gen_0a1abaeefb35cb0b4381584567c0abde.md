续表

[PRE0]

续表

[PRE1]

核心概念节点之间的关联共归纳出六种类型，等价、子类、组合、聚合、关联与风控。其中风控关系是金融审计场景特有的，其他五类关系则具备跨领域迁移的能力。这六类联系映射出节点间的紧密度差异，在知识图谱中体现为边权的差异化赋值，依次为 10、8、6、5、3、1。

### 7.5.2 意图划分方法
基本意图的划分采用了两种路径。第一种依赖领域专家的经验进行人工标注，可视作基准结果，对应表 7.2。第二种是基于二维结构信息熵最小化的自动化划分，结果见表 7.3。

表 7.2　金融审计知识图谱上的人工意图划分结果

[PRE2]

表 7.3　金融审计知识图谱上的自动意图划分结果

[PRE3]

对比可见，两套划分均无重叠，且整体高度吻合。意图 1 与意图 3 的结果完全一致，自动方法把人工划分的意图 5 与意图 6 合并为同一类，同时把意图 7 拆分为两类。除此之外，仅在节点 10 与节点 15 上出现划分偏差。进一步用兰德指数进行量化评估，即

[PRE4]

式中，`n`为节点总数；`T`为节点对中划分正确的数量，包含人工把一对节点分到同一意图且自动方法也如此，或人工把一对节点分到不同意图且自动方法也如此的情况。该指标取值区间为\[0,1\]，数值越高说明划分越可靠。计算得到 `RI=0.935`，表明所采用的自动划分策略准确有效。

### 7.5.3 交互意图解析与计算
交互意图的理解由意图检测、意图迁移、意图推断与人工筛选四步构成。以“查询北京分行存在外部违规客户的所有合同”为例，交互意图的理解与计算流程如图 7.13 所示。

[PRE5]

图 7.13　交互意图理解与计算过程

## 参考文献

\[1\] HUANG X, ACERO A, HON H W. Spoken language processing: a guide to theory, algorithm, and system development\[M\]. 2001.

\[2\] 颜永红. 语言声学与内容理解研究进展\[J\]. 应用声学, 2012(1):35-41.

\[3\] HOLMBERG M, GELBART D, HEMMERT W. Automatic speech recognition with an adaptation model motivated by auditory processing\[J\]. IEEE Transactions on Audio Speech and Language Processing, 2006, 14(1):43-49.

\[4\] S. C. CHIOU, C. P. CHEN. Noise-robust feature extraction based on forward masking\[C\]//Interspeech, 2009.

\[5\] KIM C, STERN R M. Feature extraction for robust speech recognition based on maximizing the sharpness of the power distribution and on power flooring\[C\]//IEEE International Confe-rence on Acoustics Speech & Signal Processing. 2010:4574-4577.

\[6\] HILGER F, NEY H. Quantile based histogram equalization for noise robust large vocabulary speech recognition\[J\]. IEEE Transactions on Audio, Speech and Language Processing, 2006, 14(3):845-854.

\[7\] POVEY D, KINGSBURY B, MANGU L, et al. FMPE: discriminatively trained features for speech recognition\[C\]//IEEE International Conference on Acoustics, Speech, and Signal Processing. IEEE, 2005.

\[8\] POVEY D, BURGET L, AGARWAL M, et al. The subspace gaussian mixture model—a structured model for speech recognition\[J\]. Computer Speech & Language, 2011, 25(2):404-439.

\[9\] DAHL G E, YU D, DENG L, et al. Context-dependent pre-trained deep neural networks for large-vocabulary speech recognition\[J\]. IEEE Transactions on Audio Speech & Language Processing, 2011, 20(1):30-42.

\[10\] EMAMI A, PAPINENI, SORENSEN J. Large-scale distributed language modeling\[C\]//IEEE International Conference on Acoustics, Speech and Signal Processing, 2007.

\[11\] MOHRI M. Speech recognition with weighted finite-state transducers\[M\]. Handbook on Speech Processing And Speech Communication, Part E:Speech Recognition, 2008:559-583.

\[12\] ALLAUZEN C, RILEY M, SCHALKWYK J. A generalized composition algorithm for weighted finite-state transducers\[C\]//Interspeech, 2009.

\[13\] 俞栋, 邓力, 俞凯等. 解析深度学习语音识别实践\[M\]. 北京：电子工业出版社, 2016.

\[14\] 张建华. 基于深度学习的语音识别应用研究\[D\]. 北京邮电大学, 2015.

\[15\] XIONG W, DROPPO J, HUANG X, et al. Achieving human parity in conversational speech recognition\[J\]. IEEE/ACM Transactions on Audio, Speech, and Language Processing, 2016.

\[16\] PURWINS H, LI B, VIRTANEN T, et al. Deep learning for audio signal processing\[J\]. IEEE Journal of Selected Topics in Signal Processing, 2019:206-219.

\[17\] ORGDEN C K, RICHARDS I A. The meaning of meaning: a study of the influence of thought and of the science of symbolism\[M\]. Orlando, Florida: Harcourt Brace Jovanovich Publishers, 1923.

\[18\] PEIRCE C S. Collected papers of Charles Sanders Peirce\[G\]. Harvard University Press, 1974.

\[19\] FREGE G, HERMES H, KAMBARTEL F, et al. Posthumous writings\[G\]. John Wiley Press, 1991.

\[20\] PERSSON G. Meanings, models, and metaphors: a study in lexical semantics in English\[M\]. Coronet Books Inc, 1990.

\[21\] PICCA D, GLIOZZO A M, GANGEMI A. LMM: an owl-dl metamodel to represent heterogeneous lexical knowledge\[C\]//International Conference on Language Resources and Evaluation, Marrakech, Morocco. 2008:2413-2419.

\[22\] TIAN F, DAI G Z, CHEN Y D, et al. Specification and structure design of 3D interaction tasks\[J\]. Journal of Software, 2002, 13:2099-2105.

\[23\] 虞龙发. 言语行为理论中的语言规则\[J\]. 上海师范大学学报(哲学社会科学版), 2003, 32(1):47-50.

\[24\] 徐赳赳. 现代汉语篇章回指研究\[M\]. 北京：中国社会科学出版社, 2003.

\[25\] HUNSBERGER L, ORTIZ C L. Dynamic intention structures I: a theory of intention representation\[J\]. Autonomous Agents and Multi-Agent Systems, 2008, 16(3):298-326.

\[26\] 刘胜航, 朱嘉奇, 邓昌智, 等. 基于人机协同的潜在意图检测模型\[J\]. 软件学报, 2016, 27(Suppl.(2)):82-90.

\[27\] 刘胜航, 陈辉, 朱嘉奇, 等. 基于语义三角形的自然人机交互模型\[J\]. 中国科学：信息科学(中文版), 2018, 48(4):466-474.

# 第 8 章 仿真交互新篇
## 8.1 总览
计算机发展至今已历经主机、PC 到普适计算三个阶段。无论时代如何更迭，人机界面始终是核心议题。图形用户界面造就了 PC 的辉煌，而虚实融合与普适计算正指向人机交互的终极方向。

虚实融合的发展是从独立的虚拟现实走向结合的增强现实。虚拟现实依托高性能计算、图形生成及各类传感器，构建出具备视听触嗅等感知的逼真三维环境，让用户能以自然方式与虚拟对象互动，是多项前沿技术融合的产物。增强现实则借助光电显示、交互与可视化技术，将不存在的虚拟元素精准植入真实场景，通过显示设备实现虚实一体，营造出高度真实的感知环境。

MR 由现实与虚拟两部分构成。虚拟侧关注用户与虚拟世界的连接，包含虚拟世界的构建呈现以及人与虚拟世界的互动。因为虚拟呈现直接作用于感官，所以营造真实感的关键在于搭建与人类感官匹配的自然通道，通过渲染、音效与触觉力觉等反馈构建闭环。混合现实省却了对现实世界的实时仿真，转而建立虚实之间的联结并模拟相互影响。但要实现虚实融合，技术挑战巨大，不仅要感知用户行为，还要感知环境、事件语义等，涉及计算机视觉、图形学、模式识别、光学、电子与材料等多学科。正是这种与现实的紧密结合，使其具备广泛的实用价值。

人机交互的核心是用户与系统间的信息交换，包含人到系统和系统到人的双向传递。系统可指各种智能设备，用户可借助操纵杆、数据服、眼动追踪器、数据手套、压力笔等向系统传递手势、语音、动作、眼动甚至脑电信息，系统则通过显示器、音箱等输出信息。理想状态下，人机交流不再依赖键盘鼠标触摸屏，实现物理世界与虚拟网络的无缝融合。

用户界面是人机信息交换的媒介，其设计目标是把用户行为状态转化为机器可处理的形式，再把机器状态转化为用户可理解的形式。MR 既能感知肌肉运动、姿势、语言、身体跟踪等输入，也能通过视觉听觉触觉嗅觉等多通道输出逼真感官体验，并根据视点与输入实时反馈。从交互视角看，MR 是一种新型人机交互界面。

MR 虽有近 30 年历史，因输入设备不便、成本较高且缺乏新范式，故长期未进入主流。如今伴随人、事件、思想与范式的变迁以及心理学用户模型的完善，MR 已具备成熟的交互心理学模型与界面范式，正从科幻走向现实，引发人机交互的革新。技术成熟后，其价值远超游戏娱乐，已在军事、航天、工业设计、手术模拟、科学可视化、远程控制与教育等领域发挥作用。未来或出现可收听对话并实时给出建议的设备，持续学习用户行为模式，如同共生体般陪伴生活。

MR 的核心是人机交互，其未来将走向网络化、智能化与泛在化。深化 MR 与人机交互的基础理论，支撑云计算、移动互联网、人工智能与大数据等新一代计算技术与 MR 融合，具有重要意义。

## 8.2 MR 交互模型
人机交互界面从命令行演进到图形界面，并正向脑机接口与自然用户界面过渡，其演进示意如图 8.1 所示。

[PRE6]

图 8.1　人机交互界面的示意图

交互过程中人类可通过多种方式与系统交换信息。传统 GUI 需借助鼠标键盘等设备，用户需先学习后使用。BCI 直接读取中枢与外周神经脉冲，通过头戴式设备获取意图完成交互，常见设备有 EmotivEpoc、BrainLink、MindWave 等，相对于 GUI 学习成本更低，更自然。NUI 基于听觉视觉触觉、人体行为与高层认知功能，用户无需键盘鼠标即可通过眼动、语言、表情、手势与肢体在移动、桌面、空间环境中互动，代表设备有 Leap Motion、Kinect、Wii U、MYO 等，学习门槛低、交互更自然。MR 人机交互界面融合上述多种形态，充分调动神经、肌肉与行为系统，实现更自然的交互。

与 GUI 的 WIMP 交互相比，MR 的核心交互是三维交互，主要界面形式是三维用户界面。传统二维组件已无法满足需求，MR 界面设计需考虑新的输入组件、输出显示与交互隐喻，研究重点包括界面范式、交互任务分析、关键交互设备及实现技术。

### 8.2.1 界面范式

## 8.2 MR 交互基石

### 8.2.1 Post-WIMP 与 RBI