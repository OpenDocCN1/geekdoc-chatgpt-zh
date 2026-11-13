显然，对任意 $V \in T_\Pi \uparrow (n+1)$，存在 $V' \in T_\Pi \uparrow n$。因此，$T_\Pi \uparrow \omega$ 中每一元素是 $T_\Pi$ 的一个最小不动点。例如，对程序 $\Pi = \{A; B, C; E \leftarrow A, D; F \leftarrow B\}$，则

$T_\Pi \uparrow 0 = \{\emptyset\}$

$T_\Pi \uparrow 1 = \{\{A\}, \{B\}\}$

$T_\Pi \uparrow 2 = \{\{A,C\}, \{A,E\}, \{B,D\}, \{B,F\}\} = T_\Pi \uparrow \omega$

类似于定理 2.3.10，容易验证，对不包含缺省否定的回答集程序 $\Pi$，若 $S$ 是 $\Pi$ 的一个回答集，则 $S \in T_\Pi \uparrow \omega$。

既不包含缺省否定又不包含认知析取的回答集程序是基本程序，故其如有回答集，则该回答集是程序的最小 Herbrand 模型（在 2.3.2 节陈述的扩展 Herbrand 模型）。

为了拓广定义 4.1.1 到任意回答集程序，需要引入归约的概念。直观上，归约是用适当的部分赋值消除回答集程序中的缺省否定以得到不包含缺省否定的回答集程序，从而通过后者的回答集得到前者的回答集。

定义 4.1.2 给定回答集程序 $\Pi$ 和部分赋值 $S$， $\Pi$ 对于 $S$ 的归约是集合：

$\{ 	ext{Head}(r) \leftarrow 	ext{Pos}(r) \mid r \in \Pi, 	ext{Neg}(r) \cap S = \emptyset \} \quad (4-6)$

记作 $\Pi^S$， $\Pi^S$ 可以通过如下方式得到：

+   (1) 若 $\{L_{m+1}, \dots, L_n\} \cap S 
+   eq \emptyset$，则删除这一规则；
+   
+   (2) 若 $\{L_{m+1}, \dots, L_n\} \cap S = \emptyset$，则删除该规则中的扩展文字 $	ext{not } L_{m+1}, \dots, 	ext{not } L_n$。

定义 4.1.3 部分赋值 $S$ 是回答集程序 $\Pi$ 的回答集，当且仅当 $S$ 是 $\Pi^S$ 的回答集。

定义 4.1.4 设 $\Pi$ 是回答集程序，$S$ 是部分赋值。令

$GR(S, \Pi) = \{ r \in \Pi \mid 	ext{Head}(r) \cap S 
eq \emptyset, 	ext{Pos}(r) \subseteq S, 	ext{Neg}(r) \cap S = \emptyset \} \quad (4-7)$

若 $S$ 是 $\Pi$ 的回答集，则称 $GR(S, \Pi)$ 是 $S$ 的生成规则集。

上述定义给出了回答集程序的陈述语义。直观上，回答集程序 $\Pi$ 的回答集是由基于 $\Pi$ 的规则的一种适当推理机制构建的所有可能信念集；此信念集必须满足程序的每一规则和合理性原则：任何不被程序强迫相信的信息都不应该被相信。

例 4.1.1 程序 $\{p(a) \leftarrow 	ext{not } p(a)\}$ 和 $\{p(a) \leftarrow , -p(a) \leftarrow\}$ 都没有回答集；而程序 $\{p(0), p(x+2) \leftarrow 	ext{not } p(x), q(x+1) \leftarrow p(x), 	ext{not } q(x), q(x) \leftarrow p(x), 	ext{not } q(x+1)\}$（其中 $x$ 是自然数），有无穷多个回答集，每个回答集由原子 $p(0), p(3), p(4), p(7), p(8), \dots$ 和对每一满足 $p$ 的 $n$，选择 $q(n)$ 或 $q(n+1)$ 之一组成。

注意：尽管回答集程序的回答集是由其归约（不含 not 的程序）的回答集确定，但有本质的区别。例如，基本程序的回答集中没有原子 $A$，表示 $A$ 为假；而