例式全体的缩写。

容易证明，结论映射 \(C_n\) 具有单调性和紧性，即

定理 2.3.3 对任意确定程序 \(\Pi_1\) 和 \(\Pi_2\)，若 \(\Pi_1 \sqsubseteq \Pi_2\)，则 \(C_n(\Pi_1) \sqsubseteq C_n(\Pi_2)\)。

定理 2.3.4 确定程序 \(\Pi\) 的一个结论是 \(\Pi\) 的一个有穷子集的结论，即若 \(A \in C_n(\Pi)\)，则存在 \(\Pi\) 的有穷子集 \(\Pi'\)，使得 \(A \in C_n(\Pi')\)。

确定程序 \(\Pi\) 的结论集可以通过自底向上的方式计算，为此，引入确定程序的算子。具体如下：对一个确定程序 \(\Pi\)，定义函数 \(T_\Pi: 2^{B_\Pi} \to 2^{B_\Pi}\)，使得对任意 \(S \sqsubseteq B_\Pi\)，\(T_\Pi(S) = \{A \in B_\Pi | A \leftarrow B_1, \cdots, B_m \text{ 是 } \Pi \text{ 中一个子句的基例式且 } \{B_1, \cdots, B_m\} \sqsubseteq S\}\)。

特别地，对于确定命题程序 \(\Pi\)，有函数 \(T_\Pi: 2^{\text{Atom}} \to 2^{\text{Atom}}\) 使得对 \(\Pi\) 的一个赋值 \(S\)，\(T_\Pi(S) = \{A \in B_\Pi | A \leftarrow B_1, \cdots, B_m \in \Pi \text{ 且 } \{B_1, \cdots, B_m\} \sqsubseteq S\}\)，其中 Atom 是所有命题原子的集合。

因为 \((2^{B_\Pi}, \cup, \cap)\) 是完备格，\(T_\Pi\) 是连续函数，所以可以证明 \(T_\Pi\) 有最小不动点且 \(\text{lfp}(T_\Pi) = T_\Pi \uparrow \omega\)。据此，\(\Pi\) 的最小 Herbrand 模型 \(M_\Pi\) 有不动点特征。

定理 2.3.5 令 \(\Pi\) 是确定程序，则 \(C_n(\Pi) = M_\Pi = \text{lfp}(T_\Pi) = T_\Pi \uparrow \omega\)。

证明 \(M_\Pi = \text{glb} \{S | S \text{ 是 } \Pi \text{ 的一个 Herbrand 模型}\}\)
\(= \text{glb} \{S | T_\Pi(S) \sqsubseteq S\}\)
\(= \text{lfp}(T_\Pi)\)
\(= T_\Pi \uparrow \omega\)

算子 \(T_\Pi\) 提供了确定程序 \(\Pi\) 的陈述语义和过程语义之间的联系，使得 \(M_\Pi\) 可以通过自底向上的方式计算：

\(T_\Pi \uparrow 0 = \emptyset\)
\(T_\Pi \uparrow (n+1) = T_\Pi(T_\Pi \uparrow n)\)
\(T_\Pi \uparrow \omega = \text{glb} \{T_\Pi \uparrow n | n \in \omega \} = \cup_{n \in \omega} T_\Pi \uparrow n\)

例 2.3.1 考虑确定程序 \(\Pi = \{p(f(x)) \leftarrow p(x), q(a) \leftarrow p(x)\}\)。令 \(M_1 = B_\Pi, M_2 = T_\Pi(M_1)\) 和 \(M_3 = \emptyset\)，则

\(T_\Pi(M_1) = \{q(a)\} \cup \{p(f(t)) | t \in B_\Pi\}\)
\(T_\Pi(M_2) = \{q(a)\} \cup \{p(f(f(t))) | t \in B_\Pi\}\)
\(T_\Pi(M_3) = \emptyset\)
\(M_\Pi = \text{lfp}(T_\Pi) = \emptyset\)

定义 2.3.5 Horn 程序规则是确定程序规则或确定约束，Horn 程序是 Horn 程序规则的集合。