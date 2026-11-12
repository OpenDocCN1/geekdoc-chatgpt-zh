强化学习方法包括用于细粒度模拟和数据处理的无状态任务，以及用于维护重放缓冲区和网络参数的有状态计算。为了满足这些要求，RLlib 围绕逻辑集中和分层控制模型的原则构建分布式深度强化学习组件，该模型建立在 Ray 框架之上，这是一个用于 Python 任务分布式执行的框架。与当前深度强化学习库中用于实现并行的传统分布式模型相比，RLlib 建议使用逻辑集中控制模型的分层版本。

RLlib 中深度强化学习智能体的实现从底层策略图的定义开始，它提供了神经网络的定义，以及访问网络参数和计算损失函数的接口。这个策略图可以在任何深度学习框架中指定，而 RLlib 目前支持 TensorFlow 和 PyTorch。对于与环境的交互，RLlib 提供了一个包装已定义的策略图和环境的策略评估器类，并提供了收集经验的示例方法。该策略评估器类对所有智能体都是通用的，可以实例化为一个 Ray 远程参与者，并在多个工作人员之间复制，以便并行收集经验。RLlib 智能体的最后一个组件是独立于方法的策略优化器，负责体验采样、网络参数更新和重放缓冲区管理等关键性能任务。为了分发经验集合，优化器启动了前面讨论过的许多远程评估器。然后，根据可用的资源，优化器要么在本地计算梯度，要么将此任务委托给远程评估者。一旦获得梯度，优化器就更新策略图本地副本的参数，并将它们广播给运行该策略的所有远程评估者。

# 参考 文献

+   [1] Coulom R. Efficient selectivity and backup operators in Monte-Carlo tree search[C]. International Conference on Computers and Games, Berlin, 2006: 72-83.
+   [2] Črepiňšek M, Liu S H, Mernik M. Exploration and exploitation in evolutionary algorithms: A survey[J]. ACM Computing Surveys, 2013, 45 (3): 1-33.
+   [3] Kocsis L, Szepesvári C. Bandit based Monte-Carlo planning[C]. European Conference on Machine Learning, Berlin, 2006: 282-293.
+   [4] Sutton R S, Precup D, Singh S. Between MDPs and semi-MDPs: Learning, planning, and representing knowledge at multiple temporal scales[J]. Artificial Intelligence, 1999, 112 (1): 181-211.
+   [5] Dietterich T G. Hierarchical reinforcement learning with the MAXQ value function decomposition[J]. Journal of Artificial Intelligence Research, 2000, 13: 227-303.
+   [6] Parr R, Russell S. Reinforcement learning with hierarchies of machines[C]. Proceedings of the 10th International Conference on Neural Information Processing System, Cambridge, 1998: 1043-1049.
+   [7] Hauskrecht M, Meuleau N, Kaelbling L P, et al. Hierarchical solution of Markov decision processes using macro-actions[EB/OL]. http://arxiv.org/abs/1301.7381.[2021-08-01].
+   [8] da Silva B C, Konidaris G, Barto A. Learning parameterized skills[C]. Proceedings of the 29th