定理 7.2.2 设 $\mathcal{A}=(D,W)$ 是良基的 ADT， $\mathcal{A}$ 有 CDL 扩张当且仅当存在 $D'\subseteq D$，使得
(1) $D'$ 是联合相容的；
(2) $\Lambda(D')=D'$；
(3) $D'$ 关于性质 (1) 与性质 (2) 是极大的，即不存在联合相容的 $D''\subseteq D$，使得 $\Lambda(D'')=D''$ 且 $D\subset D''$。
注意到 $\Lambda$ 算子的单调性及下述事实：若 $D\subseteq D''$，且 $D''$ 联合相容，则 $D'$ 也联合相容，我们易获下述结果。

引理 7.2.6 设 $\mathcal{A}=(D,W)$ 是良基的 ADT，令 MJC($\mathcal{A}$)=\(\{D'\subseteq D|D'$ 是使得 $\Lambda(D')=D'$ 且 $D'$ 联合相容的极大集}\)
则 MJC($\mathcal{A}$) \(\neq\emptyset\)。

定理 7.2.3 CDL 是单调的，即对断言缺省理论 $\mathcal{A}'=(D',W)$ 和任一缺省集 $D:D'\subseteq D$，若 $E'$ 是 $\mathcal{A}'$ 的 CDL 扩张，则存在 $\mathcal{A}=(D,W)$ 的 CDL 扩张 $E$，使得 $E'\subseteq E$。
证明 若 Form($W$)\(\cup\)Supp($W$) 不协调，则 $\mathcal{A}'$ 与 $\mathcal{A}$ 都不是良基的。显然，Ths($W$) 是它们的唯一 CDL 扩张，定理成立。
若 Form($W$)\(\cup\)Supp($W$) 协调，则 $\mathcal{A}'$ 与 $\mathcal{A}$ 是良基的。由于 GD($E',\mathcal{A}'$)\(\subseteq D\)，故存在 \(D^*\in\) MJC($\mathcal{A}$)，使得 GD($E',\mathcal{A}'$)\(\subseteq D^*\)。依定理 7.2.2，有 $\mathcal{A}$ 的 CDL 扩张 $E$，使得 GD($E,\mathcal{A}$)\(\subseteq D^*\)，因此 $E'\subseteq E$。

定理 7.2.4 每个断言缺省理论 $\mathcal{A}=(D,W)$ 有扩张。
证明 令 $\mathcal{A}'=(\emptyset, W)$。显然，Ths($W$) 是 $\mathcal{A}'$ 的 CDL 扩张。依定理 4.2.6， $\mathcal{A}$ 有 CDL 扩张 $E$，使得 Ths($W$)\(\subseteq E\)。

# 7.2.2 CDL 推理的局部性与累积性

对于 CDL 推理的一种局部性质，即给定断言缺省理论 $\mathcal{A}=(D,W)$ 和其扩张 $E$，判定一断言 <A, R> 是否属于 $E$，可以由 $\mathcal{A}$ 的某个子断言缺省理论确定，其中 $A$ 是公式，$R$ 是有穷公式集。

由定理 7.2.2 易获下述引理。

引理 7.2.7 设 $\mathcal{A}=(D,W)$ 是 ADT 且 Form($W$)\(\cup\)Supp($W$)\(\cup\)Ccs($D$)\(\cup\)Con($D$) 协调，则 $\mathcal{A}$ 存在唯一 CDL 扩张 $E$ 且 GD($E,\mathcal{A}$)=\(\Lambda\)(GD($E,\mathcal{A}$))。

证明 易知，\(\Lambda\)(GD($E,\mathcal{A}$)) 满足定理 7.2.2 中的条件，且它是唯一满足这些条件的 $D$ 的子集，因此 $\mathcal{A}$ 有唯一 CDL 扩张 $E$ 且 GD($E,\mathcal{A}$)=\(\Lambda\)(GD($E,\mathcal{A}$))。

引理 7.2.8 设 $\mathcal{A}=(D,W)$ 是有穷 ADT (即 $W$ 与 $D$ 均为有穷集合)，$E$ 是 $\mathcal{A}$ 的 CDL