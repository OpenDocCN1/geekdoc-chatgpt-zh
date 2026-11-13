[59] Barriga N A, Stanescu M, Buro M. Puppet search: Enhancing scripted behavior by look-ahead search with applications to real-time strategy games[C]. Proceedings of the 11th AAAI Conference on Artificial Intelligence and Interactive Digital Entertainment, Santa Cruz, 2015: 9-15.

[60] Moraes R O, Lelis L H S. Asymmetric action abstractions for multi-unit control in adversarial real-time games[C]. Proceedings of the AAAI Conference on Artificial Intelligence, New Orleans, 2018: 876-883.

[61] Yang Z Z, Ontañón S. Guiding Monte Carlo tree search by scripts in real-time strategy games[C]. Proceedings of the 15th AAAI Conference on Artificial Intelligence and Interactive Digital Entertainment, Atlanta, 2019: 100-106.

[62] Tavares A R, Chaimowicz L. Tabular reinforcement learning in real-time strategy games via options[C]. IEEE Conference on Computational Intelligence and Games, Maastricht, 2018: 1-8.

[63] Lu L N, Zhang W P, Gu X Q, et al. HMCTS-OP: Hierarchical MCTS based online planning in the asymmetric adversarial environment[J]. Symmetry, 2020, 12 (5) : 719.

[64] Schrittwieser J, Antonoglou I, Hubert T, et al. Mastering Atari, Go, Chess and Shogi by planning with a learned model[J]. Nature, 2020, 588 (7839) : 604-609.

[65] Li X, Miikkulainen R. Opponent modeling and exploitation in poker using evolved recurrent neural networks[C]. Proceedings of the Genetic and Evolutionary Computation Conference, Kyoto, 2018: 189-196.

[66] Ganzfried S. Computing strong game-theoretic strategies and exploiting suboptimal opponents in large games[D]. Pittsburgh: Carnegie Mellon University, 2015.

[67] Ganzfried S, Sun Q Y. Bayesian opponent exploitation in imperfect-information games[C]. IEEE Conference on Computational Intelligence and Games, Maastricht, 2018: 1-8.

[68] Tian Z. Opponent modelling in multi-agent systems[D]. London: University College London, 2021.

[69] Rusch T, Steixner-Kumar S, Doshi P, et al. Theory of mind and decision science: Towards a typology of tasks and computational models[J]. Neuropsychologia, 2020, 146: 107488.

[70] Doshi P, Qu X, Goodie A S, et al. Modeling human recursive reasoning using empirically informed interactive partially observable Markov decision processes[J]. IEEE Transactions on Systems, Man, and Cybernetics-Part A: Systems and Humans, 2012, 42 (6) : 1529-1542.

[71] Doshi P, Gmytrasiewicz P, Durfee E. Recursively modeling other agents for decision making: A research perspective[J]. Artificial Intelligence, 2020, 279: 103202.

[72] Foerster J N, Chen R Y, Al-Shedivat M, et al. Learning with opponent-learning awareness[C]. Proceedings of the 17th International Conference on Autonomous Agents and Multi-agent Systems, Stockholm, 2018: 122-130.

[73] Letcher A, Foerster J, Balduzzi D, et al. Stable opponent shaping in differentiable games[EB/OL]. http://arxiv.org/abs/1811.08469.[2021-05-06].

[74] Schreven C V. Deepbot-poker[EB/OL]. https://github.com/tamlhp/deepbot-poker.[2021-05-06].

[75] Ekmekci O, Sirin V. Learning strategies for opponent modeling in poker[C]. Workshops at the 27th AAAI Conference on Artificial Intelligence, Washington, 2013: 1-7.

[76] Ganzfried S, Sandholm T. Game theory-based opponent modeling in large imperfect-information games[C]. The 10th International Conference on Autonomous Agents and Multi-agent Systems, Taipei, 2011: 533-540.

[77] Johanson M, Bowling M H. Data biased robust counter strategies[C]. Artificial Intelligence and Statistics, Clearwater Beach, 2009: 264-271.

[78] Ganzfried S, Sandholm T. Safe opponent exploitation[J]. ACM Transactions on Economics and Computation, 2015, 3 (2): 1-28.

[79] Ponsen M J V, de Jong S, Lanctot M. Computing approximate Nash equilibria and robust best-responses using sampling[J]. The Journal of Artificial Intelligence Research, 2011, 42: 575-605.

[80] Šustr M, Kovařík V, Lisý V. Monte Carlo continual resolving for online strategy computation in imperfect information games[C]. Proceedings of the 18th International Conference on Autonomous Agents and Multi-agent Systems, Montreal, 2019: 224-232.

[81] Brown N, Sandholm T. Safe and nested endgame solving for imperfect-information games[C]. Workshops at the 31st AAAI Conference on Artificial Intelligence, San Francisco, 2017: 1-14.

[82] Brown N, Bakhtin A, Lerer A, et al. Combining deep reinforcement learning and search for imperfect-information games[C]. Proceedings of the 34th Conference on Neural Information Processing Systems, Vancouver, 2022: 17057-17069.

[83] Kovařík V, Seitz D, Lisý V, et al. Value functions for depth-limited solving in zero-sum imperfect-information games[EB/OL]. http://arxiv.org/abs/1906.06412.[2021-05-06].

[84] Milec D, Lisý V. Continual depth-limited responses for computing counter-strategies in extensive-form games[EB/OL]. http://arxiv.org/abs/2112.12594.[2021-05-06].

[85] Moravčík M, Schmid M, Burch N, et al. DeepStack: Expert-level artificial intelligence in heads-up no-limit poker[J]. Science, 2017, 356 (6337) : 508-513.

## 第 5 章 协同即时策略博弈中的智能体决策架构设计

### 5.1 引言

在复杂环境中，智能体若能在多人即时策略（Real-Time Strategy, RTS）博弈中掌控大局、洞察战术并实现团队协同，堪称人工智能研究领域的一座高峰。与轮流行动的序贯博弈截然不同，协作式即时策略博弈全程实时推进，极度依赖多智能体间的紧密配合，要求智能体通过持续的探索学习与经验积累不断进化。该研究领域与大量现实系统紧密耦合，为破解未来世界协同对抗难题提供了关键路径。典型应用场景涵盖即时战略游戏、机器人足球、多机器人灾难救援、自动驾驶协同及无人集群对抗等。近些年，得益于深度学习与强化学习的深度融合，协作式即时策略博弈决策技术实现跨越式突破：面向《星际争霸》《王者荣耀》《刀塔2》等验证平台研发的各类 AI 系统，在赛事中屡获佳绩，甚至在人机对抗中击败人类顶尖高手。本章以《星际争霸》为核心切入点：5.2 节概述游戏基础，将其抽象为协作式即时策略对抗模型，剖析状态空间复杂度并归纳核心挑战及应对关键技术；5.3 节详述《星际争霸》AI 的构建全貌，涵盖发展历程、运行环境与工程实现，并基于 SMAC 平台给出算法实验范例。

### 5.2 面向《星际争霸》的即时策略博弈决策建模

#### 5.2.1 《星际争霸》游戏机制解析

《星际争霸》是一款经典的即时战略游戏（如图 5.1 所示）。虽同属零和博弈范畴，但与围棋相比，它呈现出**不完全信息**、**输入输出状态空间极大**、**海量先验知识**、**前向预测困难**等显著特征。《星际争霸 2》① 的核心构成要素可拆解为经济体系、生产体系、作战部队、科技树、侦察体系与地图地形。玩家可操控人族、虫族、神族三大差异化种族，每个种族均包含多样化的生命单位、战斗装备与功能建筑。

① 《星际争霸》常用版本之一，详情将在 5.3.2 节展开。

[PRE0]

《星际争霸》的对抗过程可划分为**战略层**、**战术层**与**微操层**三个决策维度。

- **战略层（宏观管理）**：聚焦资源采集、单位生产与实力积累等核心要素。涵盖基地建设、科技树推进（建造顺序优化）、资源调度等经济相关任务。高层战略决策还包括军队编成规划与进攻时机把控。宏观管理优势直接转化为更强的军事力量与更稳固的防御体系。

- **战术层（中观协调）**：核心在于不同规模部队群的协同作战。需在考量地形地貌的前提下，统筹大部队定位与小分队行动，处理分队间的协同配合。区别于战略层的长周期规划，战术层决策时效性更强。侦察任务贯通战略与战术两层，旨在获取对手基地布局、兵种构成及单位动向。受限于战争迷雾机制，地图上未被己方单位或建筑临近的区域均处于不可见状态，这导致信息不完全，使得主动侦察成为规避偷袭、支撑战略决策与战术机动的关键环节。

- **微操层（微观控制）**：是《星际争霸》技术上限极高、需专项训练的硬核技能。常规对局中，玩家需采集资源、建造建筑、编制部队，通过吞并领土、摧毁基地击败对手。实战中，精妙的微操能实现以最小代价换取最大伤害输出，包含一系列高难度操作：如“集中火力”指令多单位协同秒杀目标；“放风筝”利用射程与机动性优势，在规避伤害的同时持续输出；还包括依据装甲类型调整站位、多方向包抄进攻、利用地形掩体博弈等高阶技巧。

##### 《星际争霸》核心特性画像

- **典型多智能体系统**：多方势力围绕领土与资源展开博弈。
- **大规模协作需求**：单人需操控数百单位协同达成共同目标。
- **不完全信息博弈**：视野受限于本地摄像头与战争迷雾，玩家需主动移动视角、主动探索未知区域以拼凑全局信息。
- **组合爆炸级动作空间**：通过鼠标交互，在约 $10^8$ 种动作组合中抉择，且合法动作集随游戏进程动态变化。
- **超长时延奖励与归因难题**：单局动辄数万帧，早期决策的后果极度滞后，导致信用分配面临巨大挑战，蕴含丰富的探索空间。

#### 5.2.2 问题复杂度量化分析

以标准 $128 \times 128$ 像素地图为例，任意时刻地图上存在 50~400 个单元，每个单元拥有复杂内在状态（能量值、血量、待执行指令等），由此催生出天文数字级的状态空间。即便仅考虑单元位置排布，400 个单元在 $16384$ 个格子上的组合数即达 $(128 \times 128)^{400} = 16384^{400} \approx 10^{1685}$ 量级。

采用分支因子 $b$ 与博弈深度 $d$ 刻画复杂度（$b^d$）：国际象棋 $b \approx 35, d \approx 80$；围棋 $b = 30 \sim 300, d = 150 \sim 200$。而在《星际争霸》中，假设玩家操控 50~200 个单元，分支因子 $b$ 落区间 $[u^{50}, u^{200}]$（$u$ 为单位平均可用动作数）。为估算 $u$，引入简化假设：① 单位射程内最多锁定 16 个敌人；② 移动动作离散为 8 个基本方向；③ 建造动作仅在工人当前位置触发（移动与建造拆解为两步）；④ 仅考虑人族单位。综合测算，单位可执行动作数 1~43 个，典型值 20~30。考虑动作冷却时间，并非每帧全动作可用，保守估计每帧每单位约 10 个有效动作（暂忽略建筑动作）。由此推导，分支因子 $b \in [10^{50}, 10^{200}]$。典型对局时长 25 分钟，按 24 帧/秒计算，博弈深度 $d \approx 36,000$。这解释为何传统搜索算法在此寸步难行。

#### 5.2.3 核心研究挑战

相较于序贯博弈，协作式即时策略博弈的难度呈指数级跃升，主要体现在：

- **并发博弈与异构协同**：序贯博弈轮流落子，而 RTS 中多玩家同步推进剧情，动作并发生效。战场单元种类繁多、功能迥异（异构），如何最大化发挥各单元功效、实现异构协同是核心难点。
- **（隐含延续上节特性带来的挑战）：** 大规模部队的协作控制、战争迷雾下的部分可观测建模、组合动作空间的高效搜索、超长序列下的信用分配与探索平衡，均构成亟待攻克的硬骨头。