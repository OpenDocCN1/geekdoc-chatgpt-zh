### 核心参考文献汇总

- [5] Tavares A, Azpurua H, Santos A, 等. Rock, Paper, StarCraft: Strategy selection in real-time strategy games[C]. The 12th AAAI Conference on Artificial Intelligence and Interactive Digital Entertainment, Burlingame, 2016: 93-99.

- [6] Tuyls K, Perolat J, Lanctot M, 等. Bounds and dynamics for empirical game theoretic analysis[J]. Autonomous Agents and Multi-Agent Systems, 2020, 34(1): 1-30.

- [7] Ganzfried S, Sandholm T. Safe opponent exploitation[J]. ACM Transactions on Economics and Computation, 2015, 3(2): 587-604.

- [8] Viqueira E A, Greenwald A R, Cousins C, 等. Learning simulation-based games from data[C]. Proceedings of the 18th International Conference on Autonomous Agents and Multi-agent Systems, Montreal, 2019: 1778-1780.

- [9] Balduzzi D, Garnelo M, Bachrach Y, 等. Open-ended learning in symmetric zero-sum games[C]. International Conference on Machine Learning, Vancouver, 2019: 434-443.

- [10] Czarnecki W M, Gidel G, Tracey B, 等. Real world games look like spinning tops[J]. Advances in Neural Information Processing Systems, 2020, 33: 17443-17454.

- [11] Sanjaya R, Wang J, Yang Y. Measuring the non-transitivity in Chess[EB/OL]. http://arxiv.org/abs/2110.11737.[2021-08-01].

- [12] Lanctot M, Zambaldi V, Gruslys A, 等. A unified game-theoretic approach to multiagent reinforcement learning[J]. Advances in Neural Information Processing Systems, 2017, 30: 4190-4203.

- [13] Tuyls K, Perolat J, Lanctot M, 等. A generalised method for empirical game theoretic analysis[C]. Proceedings of the 17th International Conference on Autonomous Agents and Multi-agent Systems, Richland, 2018: 77-85.

- [14] Stephen M, John L, Roy F, 等. Pipeline PSRO: A scalable approach for finding approximate Nash equilibria in large games[EB/OL]. https://arxiv.org/abs/2006.08555.[2021-10-10].

- [15] Zhou M, Wan Z Y, Wang H J, 等. MALib: A parallel framework for population-based multi-agent reinforcement learning[EB/OL]. https://arxiv.org/abs/2106.07551.[2021-10-10].

- [16] Zhou M, Chen J X, Wen Y, 等. Efficient policy space response oracles[EB/OL]. https://arxiv.org/abs/2202.00633.[2021-10-10].

- [17] Kahneman D. Thinking, Fast and Slow[M]. New York: Farrar, Straus and Giroux, 2011.

- [18] Ganapini M B, Campbell M, Fabiano F, 等. Thinking fast and slow in AI: The role of metacognition[EB/OL]. https://arxiv.org/abs/2110.01834.[2021-10-10].

- [19] Kotseruba I, Tsotsos J K. 40 years of cognitive architectures: Core cognitive abilities and practical applications[J]. Artificial Intelligence Review, 2020, 53: 17-94.

- [20] Anderson J R, Bower G H. Human Associative Memory[M]. Washington D.C.: VH Winston and Sons, 1973.

- [21] Laird J E. The Soar Cognitive Architecture[M]. Cambridge: MIT Press, 2019.

- [22] Schmidhuber J. Evolutionary principles in self-referential learning[D]. München: Technische Universität München, 1987.

- [23] Li A X, Huang W R, Lan X, 等. Boosting few-shot learning with adaptive margin loss[C]. Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition, Seattle, 2020: 12576-12584.

- [24] Sung F, Yang Y X, Zhang L, 等. Learning to compare: Relation network for few-shot learning[C]. Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition, Salt Lake City, 2018: 1199-1208.

- [25] Snell J, Swersky K, Zemel R S. Prototypical networks for few-shot learning[C]. Proceedings of the 31st International Conference on Neural Information Processing Systems, Los Angeles, 2017: 4080-4090.

- [26] Finn C, Abbeel P, Levine S. Model-agnostic meta-learning for fast adaptation of deep networks[C]. International Conference on Machine Learning, Sydney, 2017: 1126-1135.

- [27] Ravi S, Larochelle H. Optimization as a model for few-shot learning[C]. International Conference on Learning Representations, Toulon, 2017: 1-11.

- [28] Bertinetto L, Henriques J, Torr P H, 等. Meta-learning with differentiable closed-form solvers[C]. International Conference on Learning Representations, New Orleans, 2019: 1-12.

- [29] Edwards H, Storkey A. Towards a neural statistician[C]. International Conference on Learning Representations, Toulon, 2017: 1-12.

- [30] Grant E, Finn C, Levine S, 等. Recasting gradient-based meta-learning as hierarchical Bayes[C]. International Conference on Learning Representations, Vancouver, 2018: 1-12.

- [31] Kim T, Yoon J, Dia O, 等. Bayesian model-agnostic meta-learning[J]. Advances in Neural Information Processing Systems, 2018: 7343-7353.

## 第 10 章 智能博弈对抗：前沿应用透析

### 10.1 概述

智能博弈对抗技术的演进，为人工智能领域——尤其是认知智能的发展——注入了强劲动力。有别于传统机器学习单纯通过优化算法拟合模型、追求损失函数最小化的最优解路径，当前在博弈论指导下，深度强化学习与蒙特卡罗树搜索的结合，已成为求解博弈均衡的关键钥匙[1]。本章将围绕微分博弈、攻防博弈与平均场博弈这三大核心模型展开：第 10.2 节剖析微分博弈在视觉欺骗中的作用；第 10.3 节聚焦攻防博弈在复杂网络对抗中的应用；第 10.4 节探讨平均场博弈如何赋能无人机集群对抗。

### 10.2 微分博弈视角下的视觉欺博机理

#### 10.2.1 微分博弈核心机制

**1. 多人微分博弈的动力学分解**
微分博弈理论融合了最优控制与经典博弈论，专门用于解析连续对抗系统的演变规律。针对多人微分博弈，可借助哈密顿力学对其动力学方程进行剖析。依据 Helmholtz 分解原理，其二阶导数构成的雅可比矩阵可拆解为对称矩阵（映射势博弈成分）与反对称矩阵（映射哈密顿博弈成分）之和[2]，即 $ J(w) = S(w) + A(w) $，其中满足 $ S = S^\top $ 与 $ A + A^\top \equiv 0 $。Balduzzi 等[3]提出，辛梯度调节（Symplectic Gradient Adjustment, SGA）能有效寻觅博弈的稳定不动点，该技术在生成对抗网络（GAN）的模型训练中表现出色。

**2. 生成对抗网络（GAN）的博弈本质**
Goodfellow 等[4]于 2014 年基于二人零和博弈思想，开创了机器学习领域的生成对抗网络。该架构由生成器与判别器双模块构成，属于全新的非监督学习范式。训练过程实质是两个模块持续博弈：判别器致力于精准甄别真假样本（真样本输出趋近 1，假样本输出趋近 0）；生成器则竭力提升造假能力，力求让判别器失效。

**3. 可信任机器学习中的对抗攻防**
可信任机器学习涵盖可解释、鲁棒、安全、对抗等多元范式[5]。其中对抗机器学习聚焦于对抗样本的生成与检测。当前，对抗补丁因占据媒介面积极小，易使多类分类器失效，甚至被用于军事欺骗（如飞机隐身补丁、舰船舷号伪装），诱导对手误判。因此，针对对抗补丁的高效检测技术研发刻不容缓。

#### 10.2.2 视觉欺骗的理论与形态

**1. 欺骗基础理论演进**
人工智能历经计算智能、感知智能至认知智能三阶跨越[6]，对抗贯穿始终。马金生在《军事欺骗》中指出[7]，军事对抗中各方常施展蒙蔽、迷惑、诱导等欺骗手段，手法涵盖隐式、示形、诈骗。视觉欺骗作为典型的“视觉图灵测试”[8,9]，是感知层面认知对抗的前沿范式。现实中，自动驾驶误识别标志、大熊猫被判为长臂猿、Deepfake 操纵公众认知等案例频发。顶级 AI 会议已设专题探讨 AI 欺骗，随着智能体可能自主习得欺骗技能，防御体系构建迫在眉睫[10]。

国际上，DARPA 等机构持续布局数据欺骗对抗研究。美军自 2017 年成立跨职能小组[11]加速关键技术应用，10.2.2 2018 年海军启动“恶意样本欺骗视觉分类器”项目[12]探究机理。DARPA 先后推出“媒体取证”[13]、“语义取证”[14]及“保障 AI 鲁棒性”[15]项目，2021 年更从构建通用攻击框架、多源传感器防御系统、扩展评估体系三维度推进。美军 2020 年发布首份《数据战略》[16]确立数据可信目标，因数据失信会导致决策滞后或错判，造成巨大损失。2021 年美军研发的 DefakeHop[17] 及 Geo-DefakeHop[18] 技术，在深度伪造检测与地理空间目标鉴伪上实现领跑。

技术演进下，深度伪造已从单模态操纵进化为视频、音频、文本、微表情合成的多模态欺骗矩阵；军用迷彩伪装亦随科技升级。对抗环境下，海量真假掺杂、多模态交织的目标数据，给分析人员研判带来严峻挑战。

**2. 欺骗基本形态解析**
《孙子兵法》云“兵者，诡道也”，其“诡道十二法”（如能而示不能、用而示不用、近而示远）揭示了军事斗争中以诡诈干扰敌方认知、诱导误决、扭转战局的普遍规律。《现代汉语词典》定义欺骗为“以虚掩实致人上当”。Bell[19]视其为“错误认知”，Barton[20]则将其概括为“为获利而故意扭曲目标感知”，本质皆为“隐真”与“示假”的组合。

军事对抗既是欺骗的温床也是舞台。“明修栈道，暗度陈仓”、冷战“星球大战”计划等典故皆印证了诡道逻辑的普适性。天基、空基、地面目标信息分析中，针对人眼、传感器乃至 AI 程序的欺骗目标识别极具挑战，典型检测流程与效果见下图。

欺骗策略渗透军事、经济、网安等多域，可划分为信息对抗与策略对抗两层面。前者如电磁信号欺骗、雷达对抗、空地博弈；后者如网络主动蜜罐防御，效能远超被动侦测。Heckman 等[21]按欺骗对象（事实与谎言）将网络欺骗分为误导性数据与混淆性数据两大类，详见下表。

[PRE0]

##### 图 10.1 典型欺骗目标检测过程及效果

SAR: synthetic aperture radar，合成孔径雷达

##### 表 10.1 网络空间数据性方法

| 欺骗对象 | 误导性数据欺骗 | 混淆性数据欺骗 |

|---|---|---|