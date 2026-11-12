
的额外监控由 Logger 组件完成，该组件创建 TensorBoard 事件文件，用于对收集的统计数据进行可视化分析。

- 可靠且可重复原则。确保实验结果的一致性和可重复性是深度强化学习领域的关键挑战之一。虽然由于环境和智能体的随机性质，结果的可变性是不可避免的，但某些不一致可以通过更严格的报告标准和可共享的评估设置来避免。

## 2. Horizon

与前面讨论的 Dopamine 框架相反，Horizon 将自己定位为解决生产强化学习问题的第一个端到端平台，考虑到这种方法可能会转变为如何构建自动系统。Horizon 处理包含数百万个观察值的数据集，包括模拟器不能用于评估策略的情况，学习必须小心进行，以保持已建立的生产系统和用户经验。Horizon 框架主要包括以下主要功能。

- 数据预处理。传统的深度强化学习智能体在包含当前状态、所选动作、连续状态和奖励的 MDP 转换上进行训练。由于像 OpenAI Gym 这样的传统基准使使用模拟器来提供反馈，这样的转换可以立即创建并存储在经验缓冲区中以供学习。然而，在生产环境中，反馈循环很慢，所有的观察结果都被立即记录下来，这可能需要几个小时甚至几天的时间来获得对所选操作的奖励。因此，Horizon 提供了 Apache Spark 数据预处理管道（称为时间轴管道），该管道分析生产日志并创建强化学习智能体使用的转换。

- 特征归一化。生产数据经常是稀疏的、有噪声的和任意分布的。因为已经表明，当在正态分布特征上训练时，神经网络学习得更好，特征标准化工作流对于端到端深度强化学习系统是至关重要的。因此，Horizon 分析训练数据，并为每个特征导出一个归一化函数。Horizon 没有重新生成整个数据集，而是将该归一化函数集成到 PyTorch 神经网络中，该网络将在前向传递过程中对每个数据样本进行归一化。

- 数据理解工具。将深度强化学习方法应用于不良环境可能会使评估指标倒退，并推动开发过程调整不相关的因素。Horizon 建议从收集的生产日志中学习一个环境模型，并使用模型和启发式方法的组合来检查问题的有效性，并确定环境构建的重要特征。

- 深度强化学习智能体实现。为了支持具有 (非常大的) 离散和连续操作空间的环境，Horizon 提供了几个基于价值和基于策略的智能体的实现。对于具有可管理数量的动作的离散动作域，Horizon 提供了传统的深度问答网络和部分 Rainbow 智能体。为了处理可能包含短暂动作的非常大的离散动作域，Horizon 引入了参数动作 DQN，这是 DQN 智能体的变体，可以基于一组特征过滤无效动作。对于连续的动作空间，Horizon 实现了深度确定性策略梯度和 SAC 智能体，这两种智能体都简单而有效。

- 生产环境训练。一旦输入数据经过预处理和规范化，Horizon 就可以利用 PyTorch 的图形处理器(graphics processing unit, GPU)功能，在非常大的数据样本上高效地执行分布式训练。与模拟环境相比，从一开始就在线训练强化学习智能体可能是不可取的。应用到生产系统的初始随机策略可能会让用户接触未经测试的实验策略，或者在安全关键系统的情况下，对系统产生不可逆的影响。因此，Horizon 建议对当前非强化学习生产策略收集的数据进行离线学习初始策略。

- 反事实策略评估。与前面提出的观点类似，由于行为的不可预测性，无法在生产环境中直接评估正在训练的策略。因此，Horizon 使用反事实策略评估(counterfactual policy evaluation, CPE)方法，该方法能够基于历史数据估计策略的质量，而无须将其应用到生产系统中。在训练过程中，Horizon 执行以下 CPE 估计器之一：分步直接法估计器、分步重要性抽样估计器、分步双稳健估计器、顺序双稳健估计器、顺序加权双稳健估计器或 MAGIC 估计器。这些技术的一般思想是学习对环境奖励函数的估计，以预测没有观察到但可能发生的奖励。关于每种方法的更多细节，感兴趣的读者可以参考相关文献。

- 优化模型服务。在实际应用中，生产环境可以在包含数千台机器的集群中运行，确保模型的高效和稳健服务非常重要。Horizon 通过使用 PyTorch 和 ONNX 格式来创建一个包含智能体配置的可移植包来应对这一挑战。应用到许多机器上的策略将把新的经验收集到生产日志中，这些经验可以被馈送到时间线预处理管道，并在学习过程的下一次迭代中使用。

## 3. Ray RLlib

如前所述，深度强化学习工作流通常包括经验收集和策略优化两个阶段。理论上，人们可以通过将特定的分布式系统应用于每个阶段并将它们拼接在一起来实现并行性。然而，在实践中，由于组件之间的紧密耦合和系统之间的高通信成本，这种方法是不合理的。因此，当前的深度强化学习库倾向于将深度强化学习智能体实现为单个强连接的过程，并将并行方法应用于整个过程，而不是单个模块。不幸的是，这种策略使得这种实现很难扩展、组合和重用。研究表明，尽管深度强化学习智能体的设计看起来有很大的差异，但在大多数智能体设计中都包含了如策略评估和基于梯度的优化等几部分内容。因此，显然需要一种实现高效抽象可组合并行组件的解决方案。该框架必须解决的一个关键问题是在深度强化学习智能体工作流的不同阶段充分处理高度不规则的计算模式。首先，方法任务的持续时间可以从毫秒(如采取行动)到数小时甚至数天(如接收来自环境的反馈)。其次，不同阶段的训练需要异构硬件(例如，经验采样通常在多个中央处理器(central processing unit, CPU)上运行，而策略优化在 GPU 上运行)。最后，深度强化学习方法包括用于细粒度模拟和数据处理的无状态任务，以及用于维护重放缓冲区和网络参数的有状态计算。为了满足这些要求，RLlib 围绕逻辑集中和分层控制模型的原则构建分布式深度强化学习组件，该模型建立在 Ray 框架之上，这是一个用于 Python 任务分布式执行的框架。与当前深度强化学习库中用于实现并行的传统分布式模型相比，RLlib 建议使用逻辑集中控制模型的分层版本。

RLlib 中深度强化学习智能体的实现从底层策略图的定义开始，它提供了神经网络的定义，以及访问网络参数和计算损失函数的接口。这个策略图可以在任何深度学习框架中指定，而 RLlib 目前支持 TensorFlow 和 PyTorch。对于与环境的交互，RLlib 提供了一个包装已定义的策略图和环境的策略评估器类，并提供了收集经验的示例方法。该策略评估器类对所有智能体都是通用的，可以实例化为一个 Ray 远程参与者，并在多个工作人员之间复制，以便并行收集经验。RLlib 智能体的最后一个组件是独立于方法的策略优化器，负责体验采样、网络参数更新和重放缓冲区管理等关键性能任务。为了分发经验集合，优化器启动了前面讨论过的许多远程评估器。然后，根据可用的资源，优化器要么在本地计算梯度，要么将此任务委托给远程评估者。一旦获得梯度，优化器就更新策略图本地副本的参数，并将它们广播给运行该策略的所有远程评估者。

## 参考文献

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
- [30] Zang P, Zhou P, Minnen D, et al. Discovering options from example trajectories[C]. Proceedings of the 26th Annual International Conference on Machine Learning, Montreal, 2009: 1217-1224.
- [31] Digney B L. Learning hierarchical control structures for multiple tasks and changing environments[C]. Proceedings of the 5th International Conference on Simulation of Adaptive Behavior, Zurich, 1998: 321-330.
- [32] Mcgovern E A. Autonomous discovery of temporal abstractions from interaction with an environment[D]. Amherst: University of Massachusetts Amherst, 2002.
- [33] Hengst B. Discovering hierarchy in reinforcement learning with HEXQ[C]. International Conference on Machine Learning, Beijing, 2002: 243-250.
- [34] Konidaris G, Barto A. Building portable options: Skill transfer in reinforcement learning[C]. International Joint Conference on Artificial Intelligence, Macao, 2007: 895-900.
- [35] Konidaris G, Kuindersma S, Barto A G, et al. Constructing skill trees for reinforcement learning agents from demonstration trajectories[C]. Proceedings of the 30th International Conference on Neural Information Processing Systems, Vancouver, 2010: 1162-1170.
- [36] Mankowitz D J, Mann T A, Mannor S. Adaptive skills, adaptive partitions (ASAP) [C]. Proceedings of the 30th International Conference on Neural Information Processing Systems, Barcelona, 2016: 1596-1604.
- [37] Vigorito C M. Intrinsically motivated exploration in hierarchical reinforcement learning[C]. Congress on Evolutionary Computation, Vancouver, 2016: 1550-1557.
- [38] Bacon P L, Harb J, Precup D. The option-critic architecture[C]. The 31st AAAI Conference on Artificial Intelligence, San Francisco, 2017: 1726-1734.
