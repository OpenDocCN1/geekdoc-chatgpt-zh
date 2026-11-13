### CADL 扩张的等价性证明与有穷特征

由 $D' \subseteq D$ 的联合相容性，以及 $\text{Form}(F) \cup \text{Supp}(F) = \text{Form}(E) \cup \text{Supp}(E)$ 的协调性，可直接得出 $F_{i+1} \subseteq F'_{i+1}$。

反之，假设 $A:MB/C \in D$，且 $<A:\{r_1, \cdots, r_n\}> \in F'_i$，同时 $\{B,C\} \cup \text{Form}(F) \cup \text{Supp}(F)$ 保持协调。根据归纳假设，$<A:\{r_1, \cdots, r_n\}> \in F_i \subseteq F$，进而 $A \in \text{Form}(E)$。依据定义 7.3.3，可知 $A:MB/C \in D'$，从而推导出 $<C:\{r_1, \cdots, r_n, B,C\}> \in F_{i+1}$。这说明 $F'_{i+1} \subseteq F_{i+1}$。综合双向包含关系，确立 $F'_{i+1} = F_{i+1}$。

因此，$D'$ 正是 $\Delta$ 的 CDL 扩张 $F$ 对应的生成缺省集。

若存在 $A:MB/C \in D \setminus D'$，满足 $\text{Form}(W) \cup \text{Con}(D') \vdash A$，且 $\{B\} \cup \text{Form}(W) \cup \text{Supp}(W) \cup \text{Con}(D')$ 协调，则依定理 7.3.5 推得 $A \in \text{Form}(E)$，进而 $A:MB/C \in D'$，与假设矛盾。

“必要性”部分的证明与“充分性”对称。设 $E$ 为 $\Delta$ 的 CDL 扩张，$D' = GD(E, \Delta)$。由定理 7.2.1，存在序列 $E = \cup_{0 \le i} E_i$ 使得 $E_0 = W$，且对 $i \ge 0$：
$$E_{i+1} = Th_S(E_i) \cup \{<C:\{r_1, \cdots, r_n, B,C\}> \mid A:MB/C \in D', <A:\{r_1, \cdots, r_n\}> \in E_i\}$$

同理构造 $F = \cup_{0 \le i} F_i$，其中 $F_0 = W$，对 $i \ge 0$：
$$F_{i+1} = Th_S(F_i) \cup \{<C:\{r_1, \cdots, r_n, B\}> \mid A:MB/C \in D', <A:\{r_1, \cdots, r_n\}> \in F_i\}$$

不难通过归纳法验证：$\text{Form}(F) = \text{Form}(E)$ 且 $\text{Supp}(F) = \text{Supp}(E) \cup \text{Ccs}(D')$。

再定义 $F' = \cup_{0 \le i} F'_i$，初始 $F'_0 = W$，迭代规则为：
$$F'_{i+1} = Th_S(F'_i) \cup \{<C:\{r_1, \cdots, r_n, B\}> \mid A:MB/C \in D', <A:\{r_1, \cdots, r_n\}> \in F'_i \text{ 且 } \{B\} \cup \text{Form}(F) \cup \text{Supp}(F) \text{ 协调}\}$$

依据定理条件 (2)，类似地可证对一切 $i \ge 0$ 均有 $F_i = F'_i$。故 $F$ 是 $\Delta$ 的 CADL 扩张，且 $D' = GD_{CA}(F, \Delta)$。

---

### 引理 7.3.1 设 $\Delta=(D,W)$ 为 ADT。

(1) $\Delta$ 非良基，当且仅当 $\Delta$ 拥有唯一 CDL 扩张 $E$，且 $\text{Form}(E) \cup \text{Supp}(E)$ 不协调。
(2) $\Delta$ 非良基，当且仅当 $\Delta$ 拥有唯一 CADL 扩张 $E$，且 $\text{Form}(E) \cup \text{Supp}(E)$ 不协调。

**证明 (1)** 若 $\Delta$ 非良基，则 $Th_S(W)$ 显然是其唯一 CDL 扩张，且 $\text{Form}(E) \cup \text{Supp}(E) \cup Th_S(W)$ 不协调。反之，若 $\Delta$ 有唯一 CDL 扩张使得 $\text{Form}(E) \cup \text{Supp}(E)$ 协调，则由引理 7.2.1 可知 $\Delta$ 非良基（此处原文逻辑或有笔误，按上下文应为“良基”）。

(2) 对于 CADL 扩张，类似引理 7.2.1 的命题同理成立，故 (2) 可按 (1) 方式证得。

基于引理 7.3.1，后续讨论将聚焦于良基 ADT。结合定理 7.3.6 与定理 7.2.2，即可导出 CADL 的有穷特征定理。

---

### 定理 7.3.7（CADL 扩张的有穷特征）
设 $\Delta=(D,W)$ 为良基 ADT。$\Delta$ 存在 CADL 扩张，当且仅当存在 $D$ 的子集 $D'$，满足：
- (1) $D'$ 联合相容；
- (2) $\Lambda(D', \Delta) = D'$；
- (3) 对任意 $A:MB/C \in D - D'$，$\text{Form}(W) \cup \text{Con}(D') \nvdash A$ 或 $\{B\} \cup \text{Form}(W) \cup \text{Supp}(W) \cup \text{Ccs}(D')$ 不协调。

---

### 推论 7.3.2
设 $\Delta=(D,W)$ 为良基 ADT。若 $D$ 联合相容，则 $\Delta$ 拥有唯一 CADL 扩张 $E$，且 $GD_{CA}(D,\Delta) = \Lambda(D,\Delta)$。

例 7.3.2 将展示 CADL 的非单调性，以及 CADL 扩张可能不存在的情况。

### 例 7.3.2
令 $\Delta=(D,W)$，其中 $D=\{:MB/C\}$，$W=\emptyset$。$\Delta$ 拥有唯一 CADL 扩张 $E=Th_s(\{<C:\{B\}>\})$。若向 $D$ 中增添缺省 $C:MB/A$ 得到 $D'$，则 $\Delta'=(D',W)$ 不再具有 CADL 扩张。

---

### CADL 扩张的局部性特征

与 CDL 类似，CADL 亦具备局部性特征。首先，仿照定义 7.2.10 引入以下概念。

### 定义 7.3.4
设 $R$ 为公式集，$D$ 为缺省集，$V$ 为断言集，定义：
$$D_{CA}[R]=\{A:MB/C \in D \mid B \in R\}$$
$$V_{CA}[R]=\{<P:S> \in V \mid S \subseteq R\}$$

采用类似定理 7.2.17 的方法，可证得 CADL 扩张的局部性定理。

### 定理 7.3.8
设 $\Delta=(D,W)$ 为有穷 ADT，$D' \subseteq D$ 满足定理 7.3.7 条件 (1)–(3)，$<A:R>$ 为具协调支撑 $R$ 的断言。令 $\Delta_R=(D_{CA}[R], W_{CA}[R])$，则 $<A:R>$ 属于 $\Delta$ 由 $D'$ 生成的 CADL 扩张 $E$，当且仅当：
- (1) $\text{Form}(W_{CA}[R]) \cup \text{Con}(D_{CA}[R]) \vdash A$；
- (2) $\text{Supp}(W_{CA}[R]) \cup \text{Ccs}(D_{CA}[R]) = R$。

### 定理 7.3.9
设良基 ADT $\Delta=(D,W)$ 为有穷，$<A:R>$ 为断言。
- (1) 若 $R \cup \text{Form}(W) \cup \text{Supp}(W)$ 不协调，则 $<A:R>$ 不属于 $\Delta$ 的任何 CADL 扩张；
- (2) 若 $R \cup \text{Form}(W) \cup \text{Supp}(W)$ 协调，则 $\Delta_R=(D_{CA}[R], W_{CA}[R])$ 拥有唯一 CADL 扩张 $E$，使得 $GD_{CA}(E, \Delta_R) = \Lambda(D_{CA}[R], \Delta_R)$，且 $<A:R>$ 是 $\Delta$ 某个 CADL 扩张的元素，当且仅当 $<A:R> \in E$。

基于上述局部性，可类似 CDL 推导出 CADL 扩张构造算法及主要推理任务求解算法。下文仅列出两个核心基础算法，它们与 CDL 版本有所差异，其余算法则高度相似。

### 算法 1:
[PRE0]

### 算法 2:
[PRE1]

---

### 定理 7.3.10（CADL 的累积性）
CADL 满足累积性。即设 $\Delta=(D,W)$ 为 ADT，若存在 CADL 扩张 $F$ 包含 $<A:R>$，则 $E$ 是 $\Delta$ 的包含 $<A:R>$ 的 CADL 扩张，当且仅当 $E$ 是 $\Delta'=(D, W \cup \{<A:R>\})$ 的 CADL 扩张。其证明同定理 7.2.6 类似。

---

## 7.3.3 拟缺省逻辑

区别于 Reiter 缺省逻辑，CDL 与 CADL 引入断言替代公式，使得从缺省理论导出信念时能显式记录理由。为兼顾预设满足与累积性（即导出信念可作为引理复用），二者采用了对检验条件的整体（联合）协调性检验。

若希望 DL 既保持对检验条件的分布式协调性要求，又能显式记录理由并具备累积性，则可将断言缺省理论与分布式协调性整合，得到一个更贴近 DL 的变种：**拟缺省逻辑** [29, 30]。本节所有结论的证明留作读者练习。

### 定义 7.3.5（合式定义）
断言集 $S$ 合式定义，指不存在公式 $A$ 使得 $A \in \text{Supp}(S)$ 且 $\neg A \in \text{Supp}(S)$ 同时成立。

### 定义 7.3.6（QDL 扩张）
设 $\Delta=(D,W)$ 为 ADT，$\Gamma$ 为算子，给定断言集 $S$ 时，$\Gamma$ 生成最小断言集 $S'$，满足：
- (1) $W \subseteq S'$；
- (2) $S'$ 演绎封闭，即 $Th_\Gamma(S')=S'$；
- (3) 若 $A:MB/C \in D$，$<A:\{r_0, \cdots, r_k\}> \in S'$ 且 $\{B\} \cup \text{Form}(S)$ 协调，则 $<C:\{r_0, \cdots, r_k, B\}> \in S'$。

断言集 $E$ 为 $\Delta$ 的 QDL 扩张，当且仅当 $E=\Gamma(E)$ 且 $E$ 合式定义。同 DL 扩张一样，易于建立 QDL 扩张的拟归纳特征与有穷特征。

### 定理 7.3.11（QDL 扩张的拟归纳特征）
设 $\Delta=(D,W)$ 为 ADT，$E$ 为断言集，定义序列：$E_0=W$，对 $i \geqslant 0$，
$$E_{i+1} = Th_\Gamma(E_i) \cup \{<C:\{r_0, \cdots, r_k, B\}> \mid A:MB/C \in D, <A:\{r_0, \cdots, r_k\}> \in E_i \text{ 且 } \{B\} \cup \text{Form}(E) \text{ 协调}\}$$
$E$ 为 $\Delta$ 的 QDL 扩张，当且仅当 $E = \cup_{0 \leqslant i} E_i$ 且 $E$ 合式定义。

类似地，可定义 QDL 扩张 $E$ 的生成缺省集 $GD_Q(E, \Delta)$，不致混淆时简记为 $GD(E, \Delta)$。

### 定义 7.3.7（Q-相容性）
设 $\Delta=(D,W)$ 为 ADT，$D' \subseteq D$。
- 若对每一 $A:MB/C \in D'$，$\text{Form}(W) \cup \text{Con}(D') \cup \{B\}$ 协调，称 $D'$ 为 **Q-相容**（关于 $\Delta$）。
- 若 $D'$ Q-相容且 $\Lambda(D', \Delta)=D'$，称 $D'$ 为 **强 Q-相容**（关于 $\Delta$）。
- 若 $D'$ 强 Q-相容，且不存在真超集 $D'' \supseteq D'$ 为强相容，称 $D'$ 为 **极大强 Q-相容**（关于 $\Delta$）。

### 定理 7.3.12（QDL 扩张的有穷特征）
设 $\Delta=(D,W)$ 为 ADT。$\Delta$ 有 QDL 扩张，当且仅当存在 $D' \subseteq D$，使得：
- (1) $D'$ Q-相容；
- (2) 对任意 $A:MB/C \in D - D'$，$\text{Form}(W) \cup \text{Con}(D') \nvdash A$ 或 $\text{Form}(W) \cup \text{Con}(D') \cup \{B\}$ 不协调；
- (3) 不存在公式 $A$ 使得 $A \in \text{Supp}(W) \cup \text{Ccs}(D')$ 且 $\neg A \in Th(\text{Form}(W) \cup \text{Con}(D'))$。

等价地，可用 “$D'$ 极大强 Q-相容” 替代上述条件 (1) 与 (2)。

### 定理 7.3.13
QDL 满足累积性。

---

### 定理 7.3.14（QDL 与 DL 的关系）
设 $\Delta=(D,W)$ 为 ADT，其中 $W$ 仅含形如 $<A:\emptyset>$ 的断言。$E$ 为 $\Delta$ 的 QDL 扩张，当且仅当 $\text{Form}(E)$ 为 $(D, \text{Form}(W))$ 的 DL 扩张。即 $\text{Form}$ 将 $\Delta$ 的全体 QDL 扩张集合满射到 $(D, \text{Form}(W))$ 的全体 DL 扩张集合。

> **注意**：断言集的合式定义性只是保证累积性的充分条件，非必要条件。

### 例 7.3.3
考虑断言缺省理论 $\Delta=(\{:MB/B\}, <A:\{\neg B\}>)。其唯一极大强 Q-相容缺省子集为 $\{:MB/B\}$，但对应的 $E=\text{Th}_S(\{<A:\{\neg B\}>, <B:\{B,\neg B\}>\})$ 非合式定义，甚至不满足预设。若放弃合式定义性条件，以 $E$ 作为扩张，易验证它满足累积性。

由此，放宽合式定义性可得另一变种 **QDL***：

### 定义 7.3.8（QDL* 扩张）
设 $\Delta=(D,W)$ 为 ADT，算子 $\Gamma$ 产生最小断言集 $S'$，满足：
- (1) $W \subseteq S'$；
- (2) $S'$ 演绎封闭，即 $\text{Th}_S(S')=S'$；
- (3) 若 $A:MB/C \in D$，$<A:\{r_0,\cdots,r_k\}> \in S'$ 且 $\{B\} \cup \text{Form}(S)$ 协调，则 $<C:\{r_0,\cdots,r_k,B\}> \in S'$。

断言集 $E$ 为 $\Delta$ 的 QDL* 扩张，当且仅当 $E=\Gamma(E)$。

显然，QDL 扩张均为 QDL* 扩张，反之不成立。

对 QDL*，类似定理 7.3.10 与 7.3.12 的结论仍成立。为刻画 QDL* 累积性的条件，引入以下概念。

仿定义 7.3.4，设 $\Delta=(D,W)$ 为 ADT，$V$ 为断言集，$R$ 为公式集。记：
$$D[R]=\{A:MB/C \in D \mid B \in R\}$$
$$V[R]=\{<P:S> \in V \mid S \subseteq R\}$$
$$\Delta[R]=\{D[R], W[R]\}$$

### 定义 7.3.9（合式形成）
设 $\Delta=(D,W)$ 为断言缺省理论，$E$ 为其包含 $<A:J>$ 的 QDL* 扩张。若对 $\Delta'=(D, W \cup \{<A:J>\})$ 的任一 QDL* 扩张 $F$ 及任一 $B \in \text{Ccs}(D[J])$，$\text{Form}(F) \cup \{B\}$ 均协调，则称 $E$ 关于 $<A:J>$ **合式形成**。若对任意 $<A:J> \in E$，$E$ 均合式形成，则称 $E$ **合式形成**。若 $\Delta$ 的任一 QDL* 扩张皆合式形成，则称 $\Delta$ **合式形成**。

### 引理 7.3.2
$\Delta=(D,W)$ 的 QDL 扩张 $E$ 是其 QDL* 扩张，且 $E$ 合式形成。

### 引理 7.3.3
设 $E$ 为 $\Delta=(D,W)$ 的 QDL* 扩张且 $<A:J> \in E$，则 $\Delta'=(D'[J], W[J])$ 有唯一 QDL* 扩张 $F=E[J]$，使得 $\text{GD}(F,\Delta')=D'[J]$ 且 $<A:J> \in F$，其中 $D'=\text{GD}(E,\Delta)$。

### 引理 7.3.4
设 $E$ 为 $\Delta=(D,W)$ 的包含 $<A:J>$ 的 QDL* 扩张，$F$ 为 $\Delta'=(D, W \cup \{<A:J>\})$ 的 QDL* 扩张。则 $F$ 为 $\Delta$ 的 QDL* 扩张，当且仅当对任意 $B \in \text{Ccs}(D[J])$，$\text{Form}(F) \cup \{B\}$ 协调。

### 定理 7.3.15
断言缺省理论 $\Delta=(D,W)$ 满足累积性，当且仅当它合式形成。

该定理由引理 7.3.3 与引理 7.3.4 直接推得。