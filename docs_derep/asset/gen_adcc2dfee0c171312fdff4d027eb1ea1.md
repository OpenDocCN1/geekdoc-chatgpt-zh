## Horizon：面向生产级落地的端到端 RL 平台

与先前探讨的 Dopamine 侧重研究不同，Horizon 定位为业界首个面向生产环境的端到端强化学习平台，致力于解决自动化系统构建中的实际落地难题。它能处理包含海量观测值的数据集，专为模拟器无法介入、反馈循环漫长且必须保障线上系统稳定与用户体验的场景设计。该框架的核心能力矩阵涵盖以下维度：

- **数据预处理管道**。传统 DRL 依赖模拟器即时生成的 MDP 转移元组（状态、动作、次态、奖励）填充经验池。但生产环境中奖励信号往往滞后数小时甚至数天，所有交互日志需先行落地。为此，Horizon 基于 Apache Spark 构建了“时间轴管道”，自动解析生产日志，重组为智能体可训练的标准化转移数据。
- **特征归一化机制**。生产侧特征普遍呈现稀疏、噪声大、分布任意等特性。鉴于神经网络在高斯分布特征上收敛更优，Horizon 引入标准化工作流：离线分析训练集推导各特征归一化函数，并将其嵌入 PyTorch 模型前向传播路径，实现样本级在线标准化，避免全量数据重写开销。
- **数据理解与环境诊断工具**。盲目在劣质环境上应用 DRL 易导致指标退化、优化方向偏离。Horizon 主张从历史日志学习环境模型，结合启发式规则校验问题建模合理性，识别环境构建中的关键特征与潜在陷阱。
- **多元化智能体算法库**。针对超大离散/连续动作空间，Horizon 集成了价值基础与策略基础两大族群算法：中小规模离散动作支持经典 DQN 及 Rainbow 变体；海量离散动作引入参数化动作 DQN (Parametric DQN)，通过特征过滤无效动作；连续控制则提供 DDPG 与 SAC 两大主流实现。
- **生产级训练范式：离线预热 + 在线精调**。利用 PyTorch GPU 加速实现大规模分布式训练。考虑到随机初始策略上线风险（用户体验受损或安全隐患），Horizon 强制采用“离线学习启动”策略：先基于现有非 RL 策略的历史日志训练初始模型，再谨慎过渡至在线迭代。
- **反事实策略评估 (CPE) 体系**。生产环境无法直接试错，Horizon 引入 CPE 离线评估新策略质量。训练周期内支持逐步直接法 (DM)、逐步重要性采样 (IS)、逐步双稳健 (DR)、序列双稳健、序列加权双稳健及 MAGIC 等多种估计器。核心思想均为学习奖励模型预测反事实收益，具体技术细节可查阅专业文献。
- **高性能模型服务与闭环迭代**。面向千节点级集群部署，Horizon 通过 PyTorch 与 ONNX 导出标准化模型包，保障推理高效稳健。线上策略产出的新交互数据自动回流“时间轴管道”，驱动下一轮训练迭代，形成数据-模型-服务飞轮。

## Ray RLlib：解耦并行计算的分布式强化学习库

DRL 工作流本质包含“经验采集”与“策略优化”两阶段。理论上可为各阶段配专用分布式系统再拼接，但实践中组件强耦合、通信开销大，难以落地。现有库多将智能体封装为单一重耦合进程，对整体并行而非模块并行，导致扩展、复用极难。研究发现，尽管算法形态各异，但策略评估、梯度优化等核心模块高度共性化，**急需一套支持高效抽象与组件组合的并行框架**。

关键挑战在于处理 DRL 流程中高度不规则的计算模式：
1.  **时延跨度巨大**：从毫秒级动作决策到小时/天级环境反馈；
2.  **硬件异构需求**：采样常跑多 CPU，训练依赖 GPU；
3.  **状态与无状态混杂**：模拟/预处理无状态，经验池维护与参数同步有状态。

RLlib 基于 Ray（Python 分布式任务框架）构建，采用**逻辑集中、分层控制**的分布式模型，区别于传统数据并行或模型并行范式。其架构三大基石：

- **策略图**：底层抽象，封装神经网络拓扑、参数访问接口与损失函数计算，框架无关（现支持 TensorFlow/PyTorch）。
- **策略评估器**：通用交互组件，包装策略图与环境，提供采样接口。可实例化为 Ray Remote Actor，跨多 Worker 并行部署，实现经验采集水平扩展。
- **策略优化器**：算法无关的性能核心，统筹经验采样、参数更新、回放缓冲管理。优化器启动远程评估器采集数据，按资源情况本地或下发计算梯度，聚合后更新策略图本地副本并广播同步至所有评估器。

该设计实现了采集与训练解耦、异构硬件调度灵活、有状态组件显式管理，极大提升了分布式 DRL 的工程落地效率。

### 参考文献

- [1] Coulom R. Efficient selectivity and backup operators in Monte-Carlo tree search[C]. International Conference on Computers and Games, Berlin, 2006: 72-83.
- [2] Črepiňšek M, Liu S H, Mernik M. Exploration and exploitation in evolutionary algorithms: A survey[J]. ACM Computing Surveys, 2013, 45 (3): 1-33.
- [3] Kocsis L, Szepesvári C. Bandit based Monte-Carlo planning[C]. European Conference on Machine Learning, Berlin, 2006: 282-293.
- [4] Sutton R S, Precup D, Singh S. Between MDPs and semi-MDPs: Learning, planning, and representing knowledge at multiple temporal scales[J]. Artificial Intelligence, 1999, 112 (1): 181-211.
- [5] Dietterich T G. Hierarchical reinforcement learning with the MAXQ value function decomposition[J]. Journal of Artificial Intelligence Research, 2000, 13: 227-303.
- [6] Parr R, Russell S. Reinforcement learning with hierarchies of machines[C]. Proceedings of the 10th International Conference on Neural Information Processing System, Cambridge, 1998: 1043-1049.
- [7] Hauskrecht M, Meuleau N, Kaelbling L P, et al. Hierarchical solution of Markov decision processes using macro-actions[EB/OL]. http://arxiv.org/abs/1301.7381.[2021-08-01].
- [8] da Silva B C, Konidaris G, Barto A. Learning parameterized skills[C]. Proceedings of the 29th International Conference on Machine Learning, Edinburgh, 2012: 1679-1686.
- [9] Parr R E. Hierarchical control and learning for Markov decision processes[D]. Berkeley: University of California, 1998.
- [10] Thorndike E L. Review of animal intelligence: An experimental study of the associative processes in animals[J]. Psychological Review, 1898, 5(5): 551-553.
- [11] Bellman R, Kalaba R. On the role of dynamic programming in statistical communication theory[J]. IRE Transactions on Information Theory, 1957, 3(3): 197-203.
- [12] Bellman R. A Markovian decision process[J]. Journal of Mathematics and Mechanics, 1957, 6(5): 679-684.
- [13] Watkins C J C H, Dayan P. Q-learning[J]. Machine Learning, 1992, 8(3-4): 279-292.
- [14] Mnih V, Kavukcuoglu K, Silver D, et al. Human-level control through deep reinforcement learning[J]. Nature, 2015, 518(7540): 529-533.
- [15] Hausknecht M, Stone P. Deep recurrent Q-learning for partially observable MDPs[EB/OL]. http://arxiv.org/abs/1507.06527.[2021-08-01].
- [16] Wang Z Y, Schaul T, Hessel M, et al. Dueling network architectures for deep reinforcement learning[C]. Proceedings of the 33rd International Conference on Machine Learning, Anaheim, 2016: 1995-2003.
- [17] Mnih V, Badia A P, Mirza M, et al. Asynchronous methods for deep reinforcement learning[C]. Proceedings of the 33rd International Conference on Machine Learning, Anaheim, 2016: 1928-1937.
- [18] Fortunato M, Azar M G, Piot B, et al. Noisy networks for exploration[EB/OL]. http://arxiv.org/abs/1706.10295.[2021-08-01].
- [19] Bellemare M G, Srinivasan S, Ostrovski G, et al. Unifying count-based exploration and intrinsic motivation[C]. Proceedings of the 30th Neural Information Processing Systems, New York, 2016: 1471-1479.
- [20] Hessel M, Modayil J, van Hasselt H, et al. Rainbow: Combining improvements in deep reinforcement learning[EB/OL]. http://arxiv.org/abs/1710.02298.[2021-08-01].
- [21] Horgan D, Quan J, Budden D, et al. Distributed prioritized experience replay[EB/OL]. http://arxiv.org/abs/1803.00933.[2021-08-01].
- [22] Thrun S, Schwartz A. Finding structure in reinforcement learning[C]. Proceedings of the 34th International Conference on Neural Information Processing Systems, 1994: 385-392.
- [23] Srinivas A, Krishnamurthy R, Kumar P, et al. Option discovery in hierarchical reinforcement learning using spatio-temporal clustering[EB/OL]. http://arxiv.org/abs/1605.05359.[2021-08-01].
- [24] Krishnan S, Garg A, Liaw R, et al. SWIRL: A sequential windowed inverse reinforcement learning algorithm for robot tasks with delayed rewards[J]. The International Journal of Robotics Research, 2019, 38 (2/3): 126-145.
- [25] Konidaris G, Kuindersma S, Grupen R, et al. Robot learning from demonstration by constructing skill trees[J]. The International Journal of Robotics Research, 2012, 31 (3): 360-375.
- [26] Jonsson A, Gómez V. Hierarchical linearly-solvable Markov decision problems[C]. Proceedings of the 26th International Conference on Automated Planning and Scheduling, London, 2016: 193-201.
- [27] Goetschalckx R, Hamidi M, Tadepalli P, et al. Active imitation learning of hierarchical policies[C]. International Joint Conference on Artificial Intelligence, Milan, 2015: 3554-3560.
- [28] McGovern A, Barto A G. Automatic discovery of subgoals in reinforcement learning using diverse density[C]. Proceedings of the 18th International Conference on Machine Learning, Williamstown, 2001: 361-368.
- [29] Menache I, Mannor S, Shimkin N. Q-cut-dynamic discovery of sub-goals in reinforcement learning[C]. European Conference on Machine Learning, Berlin, 2002: 295-306.