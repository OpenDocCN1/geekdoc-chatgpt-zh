[PRE0]

**图 5.10 QTRAN-base 算法收敛曲线**

针对 3m 场景的训练结果（见图 5.11），VDN、QMIX 与 QTRAN-base 三大算法均展现出优异表现，测试胜率均成功突破 90% 大关。前期探索性实验还表明，QTRAN-base 在性能上限与运行稳健性上，均优于 QTRAN-alt 变体。

[PRE1]

**图 5.11 3m 场景胜率对比（对手难度系数=7）**

受限于篇幅，本章仅呈现单一场景下的实证数据。有兴趣的研究者可自行复现更多地图环境，进一步深度剖析各算法的适用边界与优劣差异。

### 参考文献

[1] Foerster J, Farquhar G, Afouras T, et al. Counterfactual multi-agent policy gradients[C]. Proceedings of the AAAI Conference on Artificial Intelligence, New Orleans, 2018, 32 (1): 2974-2982.

[2] Sunehag P, Lever G, Gruslys A, et al. Value-decomposition networks for cooperative multi-agent learning[EB/OL]. https://arxiv.org/abs/1706.05296.[2021-10-01].

[3] Rashid T, Samvelyan M, de Witt C S, et al. QMIX: Monotonic value function factorisation for deep multi-agent reinforcement learning[C]. International Conference on Machine Learning, Stockholm, 2018: 4295-4304.

[4] Son K, Kim D, Kang W J, et al. QTRAN: Learning to factorize with transformation for cooperative multi-agent reinforcement learning[C]. International Conference on Machine Learning, Long Beach, 2019: 5887-5896.

[5] Usunier N, Synnaeve G, Lin Z, et al. Episodic exploration for deep deterministic policies: An application to StarCraft micromanagement tasks[EB/OL]. https://arxiv.org/abs/1609.02993. [2021-10-01].

[6] Peng P, Wen Y, Yang Y D, et al. Multiagent bidirectionally-coordinated nets: Emergence of human-level coordination in learning to play StarCraft combat games[EB/OL]. https://arxiv.org/abs/1703.10069.[2021-10-01].

[7] Foerster J, Nardelli N, Farquhar G, et al. Stabilising experience replay for deep multi-agent reinforcement learning[C]. International Conference on Machine Learning, Sydney, 2017: 1146-1155.

[8] Shao K, Zhu Y H, Zhao D B. StarCraft micromanagement with reinforcement learning and curriculum transfer learning[J]. IEEE Transactions on Emerging Topics in Computational Intelligence, 2019, 3 (1): 73-84.

[9] Kong X Y, Xin B, Liu F C, et al. Revisiting the master-slave architecture in multi-agent deep reinforcement learning[EB/OL]. https://arxiv.org/abs/1712.07305.[2021-10-01].

[10] Tang Z T, Zhao D B, Zhu Y H, et al. Reinforcement learning for build-order production in StarCraft II[C]. 2018 Eighth International Conference on Information Science and Technology, Cordoba, 2018: 153-158.

[11] Zambaldi V, Raposo D, Santoro A, et al. Relational deep reinforcement learning[EB/OL]. https://arxiv.org/abs/1806.01830.[2021-10-01].

[12] Sun P, Sun X H, Han L, et al. TStarBots: Defeating the cheating level built-in AI in StarCraft II in the full game[EB/OL]. https://arxiv.org/abs/1809.07193.[2021-10-01].

[13] Lee D, Tang H R, Zhang J O, et al. Modular architecture for StarCraft II with deep reinforcement learning[C]. The 14th Artificial Intelligence and Interactive Digital Entertainment Conference, Edmonton, 2018: 187-193.

[14] Pang Z J, Liu R Z, Meng Z Y, et al. On reinforcement learning for full-length game of StarCraft [C]. Proceedings of the AAAI Conference on Artificial Intelligence, Honolulu, 2019, 33 (1): 4691-4698.

[15] Sukhbaatar S, Szlam A, Fergus R. Learning multiagent communication with backpropagation[J]. Advances in Neural Information Processing Systems, 2016, 29: 1-9.

[16] Liu Y, Wang W X, Hu Y J, et al. Multi-agent game abstraction via graph attention neural network[C]. Proceedings of the AAAI Conference on Artificial Intelligence, New York, 2020: 7211-7218.

## 第 6 章 竞争式序贯博弈对抗决策智能体设计

### 6.1 引言

博弈论作为剖析军事、政治、经济、社会等领域竞合关系的基石理论，其影响力早已渗透至各个维度。伴随人工智能技术的爆发式增长，博弈论更是“如虎添翼”——曾被视为难以破解的复杂博弈难题，正在被层出不穷的前沿算法逐一攻克。高效搜索策略、安全剪枝机制、并行计算优化、子博弈求解框架以及自博弈训练范式的成熟，为在复杂环境、超大状态空间、不完全不确定信息条件下的智能决策提供了坚实支撑。

德州扑克作为当前竞争式序贯博弈对抗中的“皇冠上的明珠”，也是大规模不完全信息动态博弈算法的核心基准测试床之一，其地位仅次于围棋，位列人工智能领域最受瞩目的挑战榜单前列。2018 年，美国陆军斥资千万美元专项资助德州扑克求解技术研究，足见其战略价值。本章将以德州扑克为切入点：第 6.2 节夯实规则基础、剖析复杂度与关键技术；第 6.3 节回顾 AI 发展脉络、构建智能博弈系统，并从双人无限注与多人无限注两个维度开展实证验证。

### 6.2 面向德州扑克的序贯博弈对抗决策

#### 6.2.1 无限注德州扑克规则详解

为适配计算机博弈研究，以下分别阐述双人与多人无限注德州扑克的规则体系（注：为便于算法开发测试，计算机博弈规则与实体娱乐规则存在细微差异）。

**1. 双人无限注德州扑克规则**

对局伊始，双方玩家筹码均设定为 20,000。依据座位分配角色：小盲注位（P1）与大盲注位（P2）。P1 强制投入 50 筹码，P2 强制投入 100 筹码。单局终局后，筹码重置为 20,000（保证局间独立性，利于算法评测），双方座位互换（抵消位置优势）。标准 52 张牌（除大小王）构成牌库，对局历经翻牌前、翻牌、转牌、河牌四个回合（见图 6.1）。

*   **翻牌前：** 各发两张底牌，仅持有者可见，属私有信息。
*   **后三轮：** 共发 5 张公共牌（翻牌 3 张、转牌 1 张、河牌 1 张），双方均可见。

[PRE2]

**(a) 翻牌前下注**

[PRE3]

**(b) 翻牌下注**

[PRE4]

**(c) 转牌下注**

[PRE5]

**(d) 河牌下注**

**图 6.1 德州扑克四阶段演示**

胜负判定依据：玩家从 2 张底牌与 5 张公共牌中选 5 张组成最强牌型，牌型大者胜出。牌型强弱序为：皇家同花顺 > 同花顺 > 四条 > 葫芦 > 同花 > 顺子 > 三条 > 两对 > 对子 > 高牌（详见图 6.2）。

行动顺序：翻牌轮由小盲注位（P1）率先行动；其余三轮均由大盲注位（P2）先行。可选动作涵盖：Call（跟注）、Check（过牌）、Raise（加注）、Fold（弃牌）。

*   **Call：** 将总下注额补齐至与上家一致。若为本轮首个动作或上家未加注，则称作 Check（无需投入筹码）。
*   **Raise：** 在跟注基础上追加投入。单轮双方合计 Raise 次数上限 4 次。每次追加额最低 100（即一个大盲注），且不得低于本轮最大追加额。下注总额不得超剩余筹码；若等于剩余筹码，即为 All-in（全压）。
*   **Fold：** 放弃争夺，即时判负，已投筹码归入底池。

轮次结束条件：双方均 Call，或一方 Raise 另一方 Call 后。若一方 Fold，对局即时终结。若一方 All-in 另一方 Call，直接发完剩余公共牌进入摊牌。

四轮结束后进行摊牌比大，赢家通吃底池，平局平分。同牌型下，先比参与牌型的牌面数值，大者胜；若仍同则比踢脚牌。花色不分大小。摊牌时需亮底牌；弃牌者无需亮牌。

| 牌型代号 | 英文名称 | 中文名称 | 牌型构成简述 |
| :--- | :--- | :--- | :--- |
| [Card Image] | Royal Flush | 皇家同花顺 | 同花色最大顺子 (10-J-Q-K-A) |
| [Card Image] | Straight Flush | 同花顺 | 同花色连续五张 |
| [Card Image] | Four of a Kind | 四条(金刚) | 四张同点数 + 单张 |
| [Card Image] | Full House | 葫芦 | 三条 + 一对 |
| [Card Image] | Flush | 同花 | 同花色非连续五张 |
| [Card Image] | Straight | 顺子 | 不同花色连续五张 |
| [Card Image] | Three of a Kind | 三条 | 三张同点数 + 两单张 |
| [Card Image] | Two Pair | 两对 | 两个对子 + 单张 |
| [Card Image] | One Pair | 对子 | 一个对子 + 三单张 |
| [Card Image] | High Card | 高牌 | 无以上组合，比单张大小 |

**图 6.2 德州扑克 5 张牌组合强弱对照**

**2. 多人无限注德州扑克规则（以六人制为例）**

核心机制与双人制一致，差异主要体现在座位编号、首动权轮转与轮次结束判定上。

六玩家记为 A1~A6，顺时针落座。A1 为小盲，A2 为大盲。
*   **首动权：** 翻牌前由 A3 先行；其余轮次由 A2 先行（若 A2 已弃牌，则顺时针寻找首个未弃牌玩家）。
*   **轮次终结：** 所有未弃牌玩家均已行动，且总下注额一致。
*   **按钮轮转：** 局末位置顺延，A2→A1，A3→A2，以此类推。

#### 6.2.2 问题复杂度深度剖析

德州扑克之所以成为继围棋后 AI 领域的“硬骨头”，是大规模不完全信息动态博弈算法的标杆测试床。本节将从状态空间规模、信息集数量、信息集动作维度三个维度量化其复杂度，为后续算法设计提供理论基石。

**1. 状态空间维度**

在双人无限注设定下，翻牌前私有牌组合数为 $C_{52}^2 C_{50}^2$，翻牌圈扩展为 $C_{52}^2 C_{50}^2 C_{48}^3$，后续轮次以此类推。表 6.1 对比了双视角、单视角及引入同构抽象（利用花色对称性进行无损压缩）后的状态量级。

**表 6.1 双人无限注德州扑克各阶段状态量级对比**

| 阶段 | 双视角状态数 | 单视角状态数 | 同构压缩后状态数 |
| :--- | :--- | :--- | :--- |
| 翻牌前 | 1,624,350 | 1,326 | 169 |
| 翻牌圈 | 28,094,757,600 | 25,989,600 | 1,286,792 |
| 转牌圈 | 1,264,264,092,000 | 1,221,511,200 | 55,190,538 |
| 河牌圈 | 556,276,200,480,000 | 56,189,515,200 | 2,428,287,420 |

注：各阶段下注动作（Fold/Call/Raise）与牌面状态正交，Raise 的离散化粒度决定了博弈树的“宽度”，进而影响下一轮状态爆发。因玩家动作空间与手牌无关，状态空间总量约可估算为 $3.162 \times 10^{17}$ 量级。

**2. 信息集数量估算**

假设单轮加注上限 4 次，枚举可推进至下一阶段的动作序列（前提：玩家未弃牌）。用符号 `-`（首决策点）、`r`（Raise）、`c`（Call）、`f`（Fold）标记。

*   **翻牌前：** 决策点 8 个 (`-`, `c`, `cr`, `crr`, `crrr`, `r`, `rr`, `rrr`)，可延续序列 7 个 (`cc`, `crc`, `crrc`, `crrrc`, `rc`, `rrc`, `rrrc`)，终止序列 7 个 (`f`, `rf`, `rrf`, `rrrf`, `crf`, `crrf`, `crrrf`)。
*   后续轮次同理推演，汇总见表 6.2。

**表 6.2 双人无限注德州扑克各阶段动作序列统计**

| 阶段 | 决策点数量 | 可延续序列数 | 终止序列数 |
| :--- | :---: | :---: | :---: |
| 翻牌前 | 8 | 7 | 7 |
| 翻牌圈 | 10 | 9 | 8 |
| 转牌圈 | 10 | 9 | 17 |
| 河牌圈 | 10 | 9 | 17 |

整个博弈树上的信息集总量达到了惊人的数量级：

[PRE6]

**3. 信息集动作空间维度**