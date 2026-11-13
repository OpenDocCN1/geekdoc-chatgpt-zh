# 4.3 正规程序的推理机制

依据前述定理，若将正规程序 $\Pi$ 视作缺省理论 $\Delta=(D_\Pi, \varnothing)$，并规定：集合 $S$ 为 $\Pi$ 的回答集，当且仅当 $\text{Th}(S)$ 构成 $\Delta$ 的一个扩张。在此设定下，定理 4.1.1 即可直接由定理 4.2.6 推导得出。

## 核心转换与复杂度分析

借助 DEF 映射，回答集程序可转化为不含经典否定的正规程序。依照定理 4.1.3，该映射在如下意义上保持回答集等价：文字集 $S$ 是程序 $\Pi$ 的回答集，当且仅当 $\text{DEF}(S)$ 是转换程序 $\Pi^T$ 的回答集。尽管完成了这一转换，正规程序的成员问题（即判定程序是否存在回答集）依然面临缺省否定与约束冲突带来的难题。回答集必须是程序头原子集的子集，且这些头原子仅能出现在生成规则的正体中，严禁现身于任何生成规则的负体中；一旦违背，极易导致程序无解。

### 例 4.2.1（续）

假设 $\Pi'$ 存在回答集 $S$，则规则 “$B \leftarrow$” 与 “$A \leftarrow B, \text{not } C$” 必为关于 $S$ 的生成规则，且满足 $\{B, A\} \subseteq S$。又因规则 “$C \leftarrow A, \text{not } E$” 满足 $\{A\} \subseteq \{B, A\}$ 且 $\{E\} \cap \{B, A\} = \emptyset$，它理应也是生成规则之一，从而迫使其头原子 $C \in S$。这与生成规则 “$A \leftarrow B, \text{not } C$” 要求 $C \notin S$ 产生直接冲突。

回答集程序的成员问题本质上是不可判定的；即便限定在有限 Herbrand 域上，协调性检验虽可判定，但计算复杂度依然居高不下。具体而言：含认知折取与缺省否定的程序为 $\Sigma_2^p$-完全；仅含缺省否定者为 NP-完全；两者皆无则归属 P 类。因此，探寻保障程序协调性的充分条件显得尤为关键。

头原子同时现身于规则正、负体（即便是同一规则）中，是导致回答集不存在、不唯一及高复杂度的核心成因。引入易于检验的充分条件以保证协调性或唯一性，势在必行。下文将引入正规程序的相依图，并剖析几类特殊正规程序——它们满足特定易检验条件，从而确保回答集必然存在。

**约定说明：** 除非特别声明，本节“正规程序 $\Pi$”均指其基实例化 $\text{ground}_\Pi$。即 $\Pi$ 中每条规则均为基规则，含变元规则仅作为基实例集的简写。同理，“文字”与“原子”均指基文字与基原子。

## 4.3.1 特殊正规程序族

正规程序 $\Pi$ 的**原子相依图**定义为带正负标记的有向图 $G_\Pi=(V,E)$：顶点集 $V$ 覆盖 $\Pi$ 中所有原子；若存在规则 $r \in \Pi$ 使 $\text{Head}(r)=A$ 且 $B \in \text{Pos}(r)$（或 $B \in \text{Neg}(r)$），则从 $A$ 向 $B$ 连一条正边（或负边）$\langle A,B\rangle$。

- **偶依赖/奇依赖**：若 $G_\Pi$ 中存在一条由偶数条（奇数条）负边构成的路径连接 $A$ 到 $B$，称 $B$ **偶依赖**（**奇依赖**）于 $A$，记作 $A \trianglelefteq^+ B$（$A \trianglelefteq^- B$）。统称“依赖”记为 $A \trianglelefteq B$。
- **正依赖/负依赖**：若存在一条非空路径全由正边（负边）组成，称 $B$ **正依赖**（**负依赖**）于 $A$，记作 $A \leqslant_0 B$（$A \leqslant B$）。
- **正环/负环**：若 $A \leqslant_0 A$（$A \leqslant A$），即存在序列 $B_1, \dots, B_n (n \geqslant 0)$ 使 $A \leqslant_0 B_1 \leqslant_0 \dots \leqslant_0 B_n \leqslant_0 A$（负边同理），称该序列为**正环**（**负环**）。$n=0$ 时称为**伪正环**（**伪负环**）。

显而易见，$\leqslant^+$, $\leqslant$, $\leqslant_0$ 均具传递性，唯 $\leqslant$ 不具传递性。

定义反向关系 $\geqslant^+$ 为 $B \geqslant^+ A \iff A \leqslant^+ B$。若 $G_\Pi$ 中不存在无限递减链 $A_0 \geqslant^+ A_1 \geqslant^+ A_2 \cdots$，则称 $\leqslant^+$ 为**良基**的。$\geqslant^-$, $\geqslant$, $\geqslant_0$ 及对应依赖关系的良基性定义同理。若 $\leqslant_0$ 良基，则称 $\Pi$ 为**正序协调**的。

给定正规程序 $\Pi$，若映射 $\lambda$ 为每个原子指派一个序数，则称 $\lambda$ 为 $\Pi$ 的**层次映射**。

### 定义 4.3.1（局部分层与分层）

正规程序 $\Pi$ 为**局部分层**的，当且仅当存在层次映射 $\lambda$，使得对任意规则 $r \in \Pi$：
1.  $\forall A \in \text{Pos}(r), \lambda(A) \leqslant \lambda(\text{Head}(r))$；
2.  $\forall A \in \text{Neg}(r), \lambda(A) < \lambda(\text{Head}(r))$。

称 $\lambda$ 为 $\Pi$ 的相应层次映射。进一步，若 $\Pi$ 中任一谓词 $p$ 满足 $\lambda(p(t_1)) = \lambda(p(t_2))$（$t_1, t_2$ 为任意基项），则称 $\Pi$ 为**分层**的。

> **注**：单子 $\text{Head}(r)$ 视同单原子处理。

从相依图视角看，$\Pi$ 局部分层 $\iff$ 图中至少含一条负边的依赖关系 $\leqslant$ 是良基的。

**示例**：程序 $\Pi_1 = \{p(0) \leftarrow \text{not } q(x),\ q(s(x)) \leftarrow \text{not } q(x)\}$（$s$ 为后继函数，$x$ 为自然数）。设 $\lambda(p(0)) = \omega$，$\forall n>0, \lambda(q(s^n(0))) = n$，易证 $\lambda$ 使其局部分层。

显然，**分层 $\Rightarrow$ 局部分层**，逆不成立（$\Pi_1$ 即反例）。又**任意确定程序均为局部分层**（令所有原子 $\lambda(A)=0$ 即可）。

### 例 4.1.1（续）

程序 $\{p(a) \leftarrow \text{not } p(a)\}$ 与 $\{p(0), p(x+2) \leftarrow \text{not } p(x), q(x+1) \leftarrow p(x), \text{not } q(x), q(x) \leftarrow p(x), \text{not } q(x+1)\}$（$x$ 为自然数）均**非**局部分层。若从后者移除规则 $q(x) \leftarrow p(x), \text{not } q(x+1)$，设 $\lambda(p(x))=\lambda(q(x))=x$，剩余子程序即为局部分层。

### 例 4.1.2（续）

设 $\lambda(p(a))=\lambda(p(b))=0,\ \lambda(q(a))=\lambda(q(b))=1$，则程序 $\{p(a) \leftarrow \text{not } q(a), p(b) \leftarrow \text{not } q(b), q(a)\}$ 为局部分层。

---

### 层次化构造与唯一性定理

给定集合族 $\{S_\alpha \mid \alpha < \mu\}$（$\mu$ 为序数），定义 $S_{<\alpha} = \bigcup_{\beta < \alpha} S_\beta$。设 $\lambda: B_\Pi \to \{\alpha \mid \alpha < \mu\}$，对任意 $\alpha < \mu$ 定义：
$B_\alpha = \{A \in B_{|\Pi|} \mid \lambda(A)=\alpha\}$,
$\Pi^I_\alpha = \{r \in \Pi \mid \lambda(\text{Head}(r))=\alpha\}$。

**定理 4.3.1** 局部分层的正规程序**至多拥有一个**回答集。

#### 证明

设 $\Pi$ 局部分层，$\lambda$ 为对应层次映射。不失一般性，设 $\text{ran}(\lambda)=\{\alpha \mid \alpha < \mu\}$。若 $\Pi$ 有回答集，取任意回答集 $S$。由定义 4.1.3 与定理 2.3.5，$S$ 是 $\Pi^S$ 的回答集 $\iff$ $S$ 是 $\Pi^S$ 的最小 Herbrand 模型。

对任一 $\alpha < \mu$，构造子程序 $\Pi^S_{<\alpha} = \{r \in \Pi \mid \lambda(\text{Head}(r))<\alpha\}$（$\alpha=0$ 时为空），记其最小 Herbrand 模型为 $H^S_\alpha$。利用局部分层性质，可验证 $S = \bigcup_{\alpha < \mu} H^S_\alpha$。

为证唯一性，先证引理：

> **(P1)** 对满足 $\lambda(A)<\alpha$ 的基原子 $A$，$A \in H^S_\alpha \iff A \in S$。

**证**：
- **必要性**：$\Pi^S_{<\alpha} \subseteq \Pi^S \subseteq S$，故 $H^S_\alpha \subseteq S$。
- **充分性**：反设存在 $\lambda(A)<\alpha$ 且 $A \notin H^S_\alpha$。构造 $M = H^S_\alpha \cup \{B \mid \lambda(B) \geqslant \alpha\}$，则 $A \notin M$。因 $\text{Head}(\Pi^S \setminus \Pi^S_{<\alpha})$ 中原子层次均 $\geqslant \alpha$，$M$ 是 $\Pi^S \setminus \Pi^S_{<\alpha}$ 的模型。局部分层性保证 $\Pi^S_{<\alpha}$ 中规则正体原子层次 $< \alpha$，若 $\text{Pos}(r) \subseteq M$ 则 $\text{Pos}(r) \subseteq H^S_\alpha$，进而 $M$ 为 $\Pi^S$ 模型。因 $S$ 为最小模型，得 $S \subseteq M$，矛盾。证毕。

接下来以超穷归纳法证：若 $\Pi$ 有回答集 $S, U$，则 $S=U$。

假设对某 $\alpha < \mu$，一切 $\beta < \alpha$ 均有 $\Pi^S_\beta = \Pi^U_\beta$。特别地，$\alpha=0$ 时，局部分层性给出 $\Pi^S_0 = \Pi^U_0 = \{r \in \Pi \mid \lambda(A)=0, \text{Pos}(r)=\emptyset\} \neq \emptyset$。故 $\Pi^S_{<\alpha} = \Pi^U_{<\alpha}$ 且 $H^S_\alpha = H^U_\alpha$。由 (P1) 推得 $S \cap (B_\Pi)_{<\alpha} = U \cap (B_\Pi)_{<\alpha}$。

对任一 $r \in \Pi_\beta$ ($\beta=\alpha$)，若 $C \in \text{Neg}(r)$，则 $\lambda(C) < \beta$。上式给出 $C \in S \iff C \in U$。故 $\Pi^S_\alpha = \Pi^U_\alpha$。超穷归纳原理确立 $\Pi^S = \Pi^U$，最小模型唯一性推出 $S=U$。

综上，局部分层正规程序若有回答集，则该回答集唯一。

> **注意**：拥有唯一回答集的程序**未必**局部分层。例：$\{A \leftarrow,\ B \leftarrow,\ B \leftarrow \text{not } B\}$ 唯一回答集为 $\{A, B\}$，但非局部分层。

---

## 协调性条件：确保解的存在

为保证回答集存在，引入**协调性**概念。

设 $\Pi$ 为正规程序，对任意原子 $A$，定义 $\Pi^+_A, \Pi^-_A$ 为满足下述条件的**最小**原子集：
- $A \in \Pi^+_A$；
- 对每一规则 $r \in \Pi$：
  - 若 $\text{Head}(r) \in \Pi^+_A$，则 $\text{Pos}(r) \subseteq \Pi^+_A$ 且 $\text{Neg}(r) \subseteq \Pi^-_A$；
  - 若 $\text{Head}(r) \in \Pi^-_A$，则 $\text{Pos}(r) \subseteq \Pi^-_A$ 且 $\text{Neg}(r) \subseteq \Pi^+_A$。

直观上，$\Pi^+_A$ 收集 $A$ 所**正依赖**的所有原子，$\Pi^-_A$ 收集 $A$ 所**负依赖**的所有原子。由此，正规程序本质上是一类具有正负之分的程序结构。