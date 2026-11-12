# 1. Option

Option 也就是时序扩展动作（temporally extended action，TEA），也可以称为宏动作[7]或者技能（skill）[8]。

# 定义 3.2 (Option) 可以形式化表示为一个三元组 $\langle I, \pi, \beta \rangle$ ，其中：

+   (1) \( I \) 表示所有初始状态集合，\( I \in S \) 。
+   (2) \( \pi : S \times A \rightarrow [0,1] \) 是 Option 内部策略。
+   (3) \( \beta : S^+ \rightarrow [0,1] \) 是终止条件，表示 Option 在某个状态上的终止概率。

当且仅当当前状态 \( s \in I \) 时 Option \( \langle I, \pi, \beta \rangle \) 是有效的。Option 的初始状态集合和终止条件共同限定了 Option 的适用范围。如果内部策略以及终止条件都只依赖当前状态，则称其为马尔可夫 Option。

给定一组 Option，它们的起始状态集隐式地定义了在状态 \( s \in S \) 下的可用 Option 集合 \( \mathcal{O}_S \) 。\( \mathcal{O}_S \) 类似于可用动作集合 \( A_S \) ，可以将一个原子动作看成一个 Option 的特例从而将两种集合统一。一个原子动作可以看成初始状态 \( I = s \) 、内部策略 \( \pi(s, a) = 1 \) 、终止函数 \( \beta(s) = 1 \) 的 Option。因此，可以认为智能体每一时刻 \( t \) 的动作选择都是在 Option 集合中进行的，其中一些是单步 Option，也就是原子动作，其他的多步 Option，也就是时序扩展动作。而多步 Option 的内部策略又可以选择其他 Option，这样就可以构建任意深度的分层嵌套的动作层级。

# 2. MAXQ

Dietterich[5]提出了基于 MAXQ 的值函数分层分解技术。MAXQ 的核心思想是把原始大型 MDP \( (S, A, P(s'|s, a), R(s'|s, a)) \) 问题 \( M \) 分解成一系列小的子任务 \( \{M_0, M_1, \cdots, M_n\} \) 。每个子任务 \( M_i \) 都是一个 SMDP 问题，其中，\( M_0 \) 是根任务。所有子任务构成一个具有单一根节点的有向无环图（directed acyclic graph，DAG），如图 3.2 所示。MAXQ 分层任务结构具有时间抽象、状态抽象和子任务共享等特点。同时，分层结构中的多个子任务策略可以同时学习。

![](bbox=[0.235, 0.735, 0.765, 0.885])

图 3.2 MAXQ 分层任务结构图