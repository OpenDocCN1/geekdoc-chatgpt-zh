# CDL扩张的极大联合相容特征与推理局部性深度解析

在前文基础上，我们继续深入探讨累积缺省逻辑（CDL）的核心数学性质。本节将重点揭示：CDL扩张如何由“极大联合相容生成缺省集”唯一确定，以及这种对应关系如何支撑起推理的**单调性**、**累积性**与**局部性**三大核心特质。

---

### 定义 7.2.9  联合相容与极大联合相容

给定断言缺省理论 $\Delta = (D, W)$，对于任意子缺省集 $D' \sqsubseteq D$，若以下四类要素的并集逻辑上相容（即不导出矛盾），则称 $D'$ 为**关于 $\Delta$ 的联合相容**集：
1.  背景理论公式集 $\text{Form}(W)$；
2.  背景理论支撑集 $\text{Supp}(W)$；
3.  $D'$ 的先行件后件并集 $\text{Ccs}(D') \cup \text{Con}(D')$。

进一步，若联合相容集 $D'$ 不存在真超集 $D''$ 使其仍保持联合相容，则称 $D'$ 为**极大联合相容**集。这实际上刻画了在不破坏一致性前提下，能被“同时激活”的最大缺省规模。

---

### 引理 7.2.4  CDL扩张生成集的三大核心性质

设 $\Delta = (D, W)$ 为 ADT，$E$ 为其 CDL 扩张，记生成缺省集为 $\text{GD}(E, \Delta)$。则：

1.  **联合相容性**：$\text{GD}(E, \Delta)$ 必是联合相容的。
    *   *依据*：直接由引理 7.2.1 与推论 4.2.1 推导得出。
2.  **算子不动点性**：$\Lambda(\text{GD}(E, \Delta)) = \text{GD}(E, \Delta)$。
    *   *证明思路*：单调性给出 $\Lambda(\text{GD}) \sqsubseteq \text{GD}$。反向包含需利用扩张构造序列 $E_i$ 与归纳法：任一生成缺省 $A: MB/C$ 必出现于某层 $E_i$，配合推论 7.2.1 与引理 7.2.3，归纳证明其落入 $\Lambda(\text{GD})$。
3.  **极大性**：不存在联合相容集 $D' \sqsubseteq D$ 使得 $\Lambda(D')=D'$ 且严格包含 $\text{GD}(E, \Delta)$。
    *   *反证法*：假设存在这样的 $D'$。利用 $D'$ 的联合相容性与推论 7.2.2 可知其元素满足协调条件。归纳法可证 $D'(\Delta) \sqsubseteq \text{GD}(E, \Delta)$，进而 $\Lambda(D') \sqsubseteq \text{GD}(E, \Delta)$。结合不动点假设 $\Lambda(D')=D'$，导出 $D'=\text{GD}(E, \Delta)$，与真包含假设矛盾。

> **核心洞见**：CDL 扩张的生成缺省集，精准落在了“联合相容、$\Lambda$ 不动点、极大”这三个条件的交集上。

---

### 引理 7.2.5  从极大联合相容不动点反向构造扩张（构造性证明）

设 $\Delta = (D, W)$ 为 ADT。若 $D' \sqsubseteq D$ 满足：联合相容、$\Lambda(D')=D'$、且关于前两性质极大（即属于 MJC 集合），则 $\Delta$ 必存在 CDL 扩张 $E$ 使得 $\text{GD}(E, \Delta) = D'$。

**构造路径**：
定义序列 $F_i$ ($i \ge 0$)：
*   $F_0 = W$；
*   $F_{i+1} = \text{Th}_S(F_i) \cup \{ \langle C : \{r_0,\dots,r_k, B, C\} \rangle \mid A: MB/C \in D'(\Delta), \langle A : \{r_0,\dots,r_k\} \rangle \in F_i \}$。

由联合相容性与不动点性保证 $F_i$ 合式定义，取并集 $E = \cup F_i$。归纳法可验证 $E$ 满足四大关键性质：
1.  **自生成方程**：$E = \text{Th}_S(W \cup \{\langle C: \dots \rangle \mid A: MB/C \in D', \langle A: \dots \rangle \in E \})$；
2.  **公式层刻画**：$\text{Form}(E) = \text{Th}_S(W \cup \text{Con}(D'))$；
3.  **支撑层刻画**：$\text{Supp}(E) = \text{Supp}(W) \cup \text{Ccs}(D') \cup \text{Con}(D')$；
4.  **全局协调**：$\text{Form}(E) \cup \text{Supp}(E)$ 协调。

最终通过双向归纳（$F_i \subseteq E_i$ 与 $E_i \subseteq E$），证明 $\Theta(E, \Delta) = E$，确认 $E$ 正是 $\Delta$ 的 CDL 扩张。

---

### 定理 7.2.2  CDL扩张存在性的有穷特征（判据定理）

设 $\mathcal{A}=(D,W)$ 为良基 ADT。**$\mathcal{A}$ 拥有 CDL 扩张 $\iff$ 存在 $D'\subseteq D$ 满足：**
1.  $D'$ 联合相容；
2.  $\Lambda(D') = D'$（不动点）；
3.  $D'$ 关于 (1)(2) 极大（即 $D' \in \text{MJC}(\mathcal{A})$）。

*推论*：**引理 7.2.6** 指出：良基 ADT 的 MJC 集合非空（$\text{MJC}(\mathcal{A}) \neq \emptyset$），从而保证了扩张的存在性（定理 7.2.4 亦可由此得出：从空缺省理论起步，单调扩展至完整理论）。

---

### 定理 7.2.3  CDL推理的单调性

对断言缺省理论 $\mathcal{A}'=(D',W)$ 与 $\mathcal{A}=(D,W)$（$D'\subseteq D$），若 $E'$ 是 $\mathcal{A}'$ 的 CDL 扩张，则必存在 $\mathcal{A}$ 的 CDL 扩张 $E$ 使得 $E' \subseteq E$。

*证明要点*：良基情况下，$\text{GD}(E',\mathcal{A}') \subseteq D$。由 MJC 非空性，选取极大联合相容不动点 $D^* \supseteq \text{GD}(E',\mathcal{A}')$，依定理 7.2.2 得到对应扩张 $E \supseteq E'$。

---

## 7.2.2 CDL推理的局部性与累积性：从全局到局部的计算还原

### 引理 7.2.7  唯一扩张的充分条件

若 $\mathcal{A}=(D,W)$ 为 ADT 且 $\text{Form}(W)\cup\text{Supp}(W)\cup\text{Ccs}(D)\cup\text{Con}(D)$ 整体协调，则 $\mathcal{A}$ 拥有**唯一** CDL 扩张 $E$，且 $\text{GD}(E,\mathcal{A}) = \Lambda(\text{GD}(E,\mathcal{A}))$。
*原因*：此时 $\Lambda(\text{GD}(E,\mathcal{A}))$ 是唯一满足定理 7.2.2 三条件的子集。

### 引理 7.2.8  有穷理论下的支撑集饱和

设 $\mathcal{A}$ 为有穷 ADT，$E$ 为其扩张，$\langle A:R \rangle \in E$，则 $\langle A, \text{Supp}(E) \rangle \in E$。
*机制*：有穷性保证 $\text{Form}(E)$ 与 $\text{Supp}(E)$ 均有穷。$\langle A:R \rangle \in E$ 蕴含 $R \subseteq \text{Supp}(E)$，结合引理 7.2.8 即得结论。

---

### 定义 7.2.10  相对公式集的投影操作

*   缺省投影：$D[R] = \{ A: MB/C \in D \mid \{B,C\} \subseteq R \}$
*   断言投影：$V[R] = \{ \langle A:J \rangle \in V \mid J \subseteq R \}$

---

### 引理 7.2.9  扩张的局部投影等价性

设 $\Delta=(D,W)$ 良基，$E$ 为其 CDL 扩张，$\text{GD}(E,\Delta)=D'$，$R$ 为协调公式集。构造子理论 $\Delta_{E,R} = (D[R], W[R])$。则 $\Delta_{E,R}$ 拥有**唯一**扩张 $F = \cup F_i$，满足：
$$F = E[R] \quad \text{且} \quad F \supseteq E[R]$$
*证明*：$D'[R]$ 关于 $\Delta_{E,R}$ 联合相容，引用引理 7.2.7 得唯一扩张。归纳法配合推论 7.2.1/7.2.2 逐层确立 $E_i[R] = F_i$。

---

### 引理 7.2.10  CDL推理的局部判定准则（核心定理）

设 $\Delta=(D,W)$ 为**有穷** ADT，$D'$ 为某扩张 $E$ 的生成缺省集，$\langle A:R \rangle$ 为断言且 $R$ 协调。令 $\Delta_R = (D[R], W[R])$。则：
$$\langle A:R \rangle \in E \quad \Longleftrightarrow \quad \begin{cases} 
\text{(1) } \text{Form}(W[R]) \cup \text{Con}(\Lambda(D'[R], \Delta_R)) \vdash A \\
\text{(2) } \text{Supp}(W[R]) \cup \text{Ccs}(\Lambda(D'[R], \Delta_R)) \cup \text{Con}(\Lambda(D'[R], \Delta_R)) = R 
\end{cases}$$

*证明逻辑*：
1.  **必要性**：$\langle A:R \rangle \in E \Rightarrow \langle A:R \rangle \in E[R] = F$（引理 7.2.9）。由推论 7.2.2 解析 $F$ 的 Form 与 Supp 结构，得条件 (1) 与 $R \subseteq \text{Supp}(F)$。定义 7.2.10 给出反向包含，确立等式 (2)。
2.  **充分性**：条件 (2) 给出 $\text{Supp}(F)=R$；条件 (1) 给出 $A \in \text{Form}(F)$。定理 1.3.1 保证有限支撑 $J$ 使 $\langle A:J \rangle \in F$。引理 7.2.8（支撑饱和）升级得 $\langle A:R \rangle \in F = E[R] \subseteq E$。

---

### 终极洞见：局部性原理

**引理 7.2.10 宣告了 CDL 推理的“局部性质”**：
> 判定断言 $\langle A:R \rangle$ 是否属于大理论 $\Delta=(D,W)$ 的某扩张，**完全等价于** 判定其是否属于**投影小理论** $\Delta_R=(D[R], W[R])$ 的（唯一）扩张。

这意味着：面对海量缺省规则，推理引擎只需聚焦于与目标断言支撑集 $R$ 相关的“局部切片” $D[R], W[R]$ 即可完成判定，无需构建全局扩张。这为 CDL 的高效实现提供了坚实的理论基石。