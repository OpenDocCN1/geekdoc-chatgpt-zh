# 检验集的扩张。

与 Reiter 的扩张类似，下面给出修正扩张的准归纳特征。

定理 7.1.1 令 $S$ 与 $T$ 是语句集，$\Delta=(D,W)$ 是缺省理论。如下定义两个语句序列 $<S_0, \cdots, S_m, \cdots >$ 和 $<T_0, \cdots, T_m, \cdots >$: $S_0 \subseteq S_2 \subseteq \cdots$ 和 $T_0 \subseteq T_1 \subseteq \cdots$，其中
(1) $S_0=W, T_0=\emptyset$;
(2) $S_{i+1}=\text{Th}(S) \cup \{ C | A:MB_1, \cdots, MB_n/C \in D, A \in S_i, \text{且不存在 } F \in \{B_1, \cdots, B_n\}, \text{使得 } S \cup \{C\} \vdash \neg F \}$;
(3) $T_{i+1}=T_i \cup \{ B | A:MB_1, \cdots, MB_n/C \in D, A \in S_i, B \in \{B_1, \cdots, B_n\} \text{且不存在 } F \in \{B_1, \cdots, B_n\} \text{使得 } S \cup \{C\} \vdash \neg F \}$.
则 $S$ 是 $\Delta$ 的以 $T$ 为检验集的 L 扩张，当且仅当 $S=\cup_{0 \leqslant i} S_i$ 和 $T=\cup_{0 \leqslant i} T_i$。

此定理的证明可利用下述两个引理。

引理 7.1.1 $\Xi_1(S,T)=\cup_{0 \leqslant i} S_i, \Xi_2(S,T)=\cup_{0 \leqslant i} T_i$。

证明 类似于引理 3.1.1 易证。

引理 7.1.2 $\Xi_1(S,T)=\cup_{0 \leqslant i} S_i, \Xi_2(S,T)=\cup_{0 \leqslant i} T_i$。

证明 施归纳法易证，对任意 $i \geqslant 0, S_i \subseteq \Xi_1(S,T)$ 和 $T_i \subseteq \Xi_2(S,T)$ 成立。

定义 7.1.4 令 $\Delta=(D,W)$ 是缺省理论，$S$ 是 $\Delta$ 的以 $T$ 为检验集的 L 扩张。$S$ 的生成缺省集，记作 $\text{GDL}(S,\Delta)$，是 $\{ A:MB_1, \cdots, MB_n/C \in D | A \in S, \{B_1, \cdots, B_n\} \subseteq T \}$。

推论 7.1.1 $S=\text{Th}(\text{Con}(\text{GDL}(S,\Delta))), T=\text{Ccs}(\text{GDL}(S,\Delta))$。

缺省理论 $\Delta=(D,W)$ 的算子 $\Lambda$，（极大）相容集和（极大）强相容集的概念与定义 3.2.1 和定义 3.2.2 相同。关于它们的性质在做适当的修改后，对于 $\Delta$ 的修正扩张仍然成立。特别地，我们有下述结果：

引理 7.1.3 令 $\Delta=(D,W)$ 是缺省理论，$S$ 是 $\Delta$ 的以 $T$ 为检验集的 L 扩张，则 $\Lambda(\text{GDL}(S,\Delta),\Delta)=\text{GDL}(S,\Delta)$ 且 $\text{GDL}(S,\Delta)$ 是（关于 $\Delta$）相容的。

引理 7.1.4 令 $\Delta=(D,W)$ 是缺省理论，$S$ 是 $\Delta$ 的以 $T$ 为检验集的 L 扩张，则 $\text{GDL}(S,\Delta)$ 是 $\Delta$ 的一个极大强相容缺省集。

引理 7.1.5 令 $\Delta=(D,W)$ 是缺省理论。若 $D' \subseteq D$ 是 $\Delta$ 的极大强相容缺省集，则 $D'$ 是 $\Delta$ 的一个 L 扩张的生成缺省集，即 $\text{Th}(\text{Con}(D'))$ 是 $\Delta$ 的以 $\text{Ccs}(D')$ 为检验集的 L 扩张。

类似于定理 3.2.1，容易证明下述定理（推论）。

定理 7.1.2 (L 扩张的有穷特征) 令 $\Delta=(D,W)$ 是缺省理论。$\Delta$ 有 L 扩张，当且仅当存在 $D$ 的子集 $D'$，使得 $D'$ 是极大强相容的。

推论 7.1.2 缺省理论 $\Delta=(D,W)$ 有不协调 L 扩张，当且仅当 $W$ 不协调。

类似于定理 3.1.3 和定理 3.3.4，容易证明 L 扩张的极小性和半单调性。