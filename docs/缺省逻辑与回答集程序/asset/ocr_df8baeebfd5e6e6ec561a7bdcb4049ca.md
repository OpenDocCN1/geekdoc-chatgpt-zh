例 6.12 表明从逻辑程序中删除被包孕的规则可能会影响组合消解，即使它被具有相同规则体的规则包孕。

以下定理表明，与折取基消解类似，组合消解同样不影响 $T_p^d$ 算子。

# 定理 6.1.4 令 $P$ 是一个包含规则，形如定义 6.1.4 中 $r, r_1, \cdots, r_k$ 规则的逻辑程序，使得 $r, r_1, \cdots, r_k$ 可组合消解，并且 $Q = P \cup \{cr(r, r_1, \cdots, r_k)\}$。对任意的 $I \subseteq \boldsymbol{A}$，$Hd(App(P, I)) = Hd(App(Q, I))$ 成立。

证明 一方面，对任何 $I \subseteq \boldsymbol{A}$ 有 $Hd(App(P, I)) \subseteq Hd(App(Q, I))$。因此 $Hd(App(P, I)) \supseteq Hd(App(Q, I))$ 成立。

另一方面，令 $r' = cr(r, r_1, \cdots, r_k)$。假设 $M$ 是 $Hd(App(P, I))$ 的模型，但不是 $Hd(App(Q, I))$ 的模型。于是存在 $Hd(r') \in Hd(App(Q, I))$，使得 $M$ 不满足 $Hd(r')$ 且 $M$ 满足 $Bd(r')$，即 $M \vDash Bd^+ \cup \neg(Bd^- \cup Bd'^+)$。注意，$Hd(r') = Hd(r)$。

考虑以下两种情况：

+   (1) $\{b_1, \cdots, b_k\} - M = \emptyset$，则 $\{b_1, \cdots, b_k\} \subseteq M$，于是 $M$ 满足 $Bd(r)$。显然 $Hd(r) \in Hd(App(P, I))$。因此，$M$ 满足 $Hd(r)$，矛盾。

+   (2) $\{b_1, \cdots, b_k\} - M \neq \emptyset$，则存在 $i (1 \leqslant i \leqslant k)$，$b_i \notin M$。于是 $M \vDash \neg b_i$。则 $M \vDash Bd(r_i)$。因此，$Hd(r_i) \in Hd(App(P, I))$ 且 $M \vDash Hd(r_i)$。因为 $Hd(r_i) \subseteq Hd(r)$，所以 $M$ 满足 $Hd(r)$，矛盾。

因此，对任何 $I \subseteq \boldsymbol{A}$，$Hd(App(P, I)) \equiv Hd(App(Q, I))$。

# 6.1.3 归纳的学习任务与算法

本节的背景理论是一个逻辑程序，一个例子（或观察）是一个状态转换，即一个元组 $<I, J>$ 且 $I, J \subseteq \boldsymbol{A}$。令 $E$ 是观察实例集，记 $E^i = \{I | <I, J> \in E\}$，$E^o = \{J | <I, J> \in E\}$ 及 $E(I) = \{J | <I, J> \in E\}$。当 $E^i = 2^\boldsymbol{A}$ 时称观察 $E$ 是完全的。

# 定义 6.1.5 基于状态转换的归纳学习任务是给定背景理论 $B$ 和实例集 $E$，找出一个假设（逻辑程序）$H$，使得对于每个实例 $<I, J> \in E$，$J \in T_{B \cup H}^d(I)$。

上述归纳学习任务记为 $ILT(B, E)$。归纳学习任务中的假设 $<I, J> \in E$ 被称为 $ILT(B, E)$ 的一个解。为了达到学习 $H$ 的目的，给出的实例需要被限制。例如，令 $E = \{<\emptyset, \emptyset>, <\emptyset, \{p\}>\}$ 且 $B = \emptyset$。没有逻辑程序 $<I, J> \in E$ 满足 $\{\emptyset, \{p\}\} \subseteq T_P^d(\emptyset)$。因为 $\emptyset$ 和 $\{p\}$ 在集合包含下可比较，所以不能同时成为极小集。

另外，$E$ 中的实例必须遵循背景理论 $B$，即对于每条规则 $r \in B$，如果 $I \vDash Bd(r)$，则 $Hd(r) \cap J \neq \emptyset$。例如，令 $B = \{p \lor q \leftarrow \neg r\}$ 且 $E = \{<\emptyset, \emptyset>\}$。可以验证 $ILT(B, E)$ 没有解。因为 $p \lor q \in Hd(App(B \cup H, \emptyset))$，这说明对每个 $<I, J> \in E$，存在 $Hd(r) \cap J = \emptyset$。为此，下面给出连贯性和一致性的概念。