\(\left( \bigvee_{p \in L_1\theta} p \right) \to \left( \bigvee_{A \in L_1} \text{ES}(A\theta, L_1\theta, \Pi) \right)\)

针对任意 \(p(t) \in L_1\)，不难验证 \(\text{ES}(p(t), L_1, \Pi) \theta \equiv \text{ES}(p(t), L_1\theta, \Pi) \theta\) 成立，具体展开为：

\(\bigvee_{1 \le i \le k} \left( \exists y_i \left[ \text{Body}_i, \delta_1 \wedge \bigwedge_{q(u) \in \text{Body}, \delta_i, q(v) \in L_1} (u \neq v) \right] \theta \right)\)

\(\equiv \bigvee_{1 \le i \le k} \exists y_i \left[ \text{Body}_i, \delta_2 \wedge \bigwedge_{q(u) \in \text{Body}, \delta_2, q(v) \in L_1 \theta} (u \neq v) \right]\)

其中：
- \(\delta_2 = \delta_1 \theta\)；
- \(p(x) \leftarrow Body_1, \cdots, p(x) \leftarrow Body_k\) 为正规化程序 \(\Pi\) 中的规则集；
- 对所有 \(1 \le i \le k\)，\(y_i\) 是规则 \(p(x) \leftarrow Body_i\) 的局部变元，通过变元重命名可假设 \(y_i\) 中变元均不出现在 \(L_2\) 中；由此可得 \(\text{LF}(L_2, \Pi)=\forall x\Phi(x)\theta \equiv \text{LF}(L_1, \Pi)\theta\)。

设 \(\Pi\) 为正规程序，\(\Delta\) 为 \(\Pi\) 的一阶正环集合的子集。称 \(\Delta\)（关于 \(\Pi\)）是**完备的**，当且仅当对 \(\Pi\) 的任意环 \(L\)，均存在 \(S \in \Delta\) 使得 \(S\) 包孕 \(L\)。

例如，针对示例 5.3.2 的程序 \(\Pi\)，一阶正环集 \(\{\{p(x)\}\}\) 关于 \(\Pi\) 即是完备的。

### 定理 5.3.2

设 \(\Pi\) 为正规程序，构造 \(D=\{c_1,c_2\} \cup \text{Const}(\Pi)\)，其中 \(c_1, c_2\) 为两个不在 \(\Pi\) 中出现的不同常元。则下列五个命题相互等价：

- (1) \(\Pi\) 存在有穷完备的一阶正环集合；
- (2) 存在自然数 \(N\)，使得对任意 \(\Pi\) 的环 \(L\)，均有 \(|\text{var}(L)|\leqslant N\)；
- (3) 对 \(\Pi\) 的任意环 \(L\) 及 \(A_1, A_2 \in L\)，恒有 \(\text{var}(A_1)=\text{var}(A_2)\)；
- (4) 对 \(\Pi_{eq}|D\) 上的任意环 \(L\)，不存在原子 \(A,B \in L\) 使得 \(c_1 \in \text{Const}(A) \setminus \text{Const}(B)\) 或 \(c_2 \in \text{Const}(B) \setminus \text{Const}(A)\)；
- (5) 对 \(\Pi_{eq}|D\) 的任意极大（按包含关系）环 \(L\)，不存在原子 \(A,B \in L\) 使得 \(c_1 \in \text{Const}(A) \setminus \text{Const}(B)\) 或 \(c_2 \in \text{Const}(B) \setminus \text{Const}(A)\)。

此处 \(\Pi_{eq}\) 系将 \(\Pi\) 中的等式 \(t_1=t_2\) 重命名为 \(\text{eq}(t_1,t_2)\) 所得程序，\(\text{eq}\) 为新二元谓词。因此 \(\Pi_{eq}\) 消除了等号，使得 \(\Pi_{eq}|D\) 保留了所有因处理等号而被删除的规则。

### 证明

**(2) ⇒ (1)**：令 \(\Delta\) 为 \(\Pi\) 所有一阶正环的集合，且每个环的变元均落在 \(\{x_1, \cdots, x_N\}\) 内。显然 \(\Delta\) 有限。对 \(\Pi\) 的任意环 \(L\)，取置换 \(\theta\) 将 \(\text{var}(L') \setminus \{x_1, \cdots, x_N\}\) 中的变元替换为 \(\{x_1, \cdots, x_N\} \backslash \text{var}(L')\) 中的变元，得 \(L'=L\theta\)。因 \(|\text{var}(L)|\leqslant N\)，该置换必存在且 \(L' \in \Delta\)。故 \(\Delta\) 为 \(\Pi\) 的有穷完备一阶正环集合。

**(3) ⇒ (2)**：\(\Pi\) 有限，故谓词符号有限。设 \(N=\max\{n \mid \exists \text{ 出现于 } \Pi \text{ 的 } n \text{ 元谓词 } p\}\)。对任意环 \(L\) 及 \(A_1, A_2 \in L\)，由 (3) 知 \(\text{var}(A_1)=\text{var}(A_2)\)，故 \(|\text{var}(L)|\leqslant N\)。

**(4) ⇒ (3)**：反设存在环 \(L\) 及 \(A_1, A_2 \in L\) 使 \(\text{var}(A_1)\neq\text{var}(A_2)\)。不失一般性，设 \(x \in \text{var}(A_1) \backslash \text{var}(A_2)\)。构造置换
\[ \theta = \{x / c_1\} \cup \{x' / c_2 \mid x' \in \text{var}(L) \backslash \{x\} \} \]
则 \(L'=L\theta\) 为 \(\Pi_{eq}|D\) 的一阶正环，且 \(c_1 \in \text{Const}(A_1) \backslash \text{Const}(A_2)\)，与 (4) 矛盾。

**(1) ⇒ (4)**：设 \(\Delta = \{L_1, \cdots, L_n\}\) 为 \(\Pi\) 的有穷完备一阶正环集合，\(N=\max\{|\text{var}(L_1)|, \cdots, |\text{var}(L_n)|\}\)。则任意 \(\Pi\) 的一阶正环 \(L\) 均被某 \(L^* \in \Delta\) 包孕，且 \(|\text{var}(L)|\leqslant |\text{var}(L^*)|\leqslant N\)。

假设存在 \(\Pi_{eq}|D\) 的一阶正环 \(L=\{A_1, \cdots, A_m\}\) 使 \(c_1 \in \text{Const}(A_1) \backslash \text{Const}(A_2)\)。将 \(L\) 中的 \(c_1\) 替换为新变元 \(y_i\) 得 \(L_i (1 \leqslant i \leqslant N+1)\)，均为 \(\Pi_{eq}|D\) 的一阶正环且含 \(A_2\)。由引理 5.3.2 证明知 \(L_1 \cup \cdots \cup L_{N+1}\) 亦为一阶正环，且变元数超 \(N\)。重复此法可构造任意长度环，与 \(\Delta\) 有限完备矛盾。

同理，若 \(c_2 \in \text{Const}(A_2) \setminus \text{Const}(A_1)\) 亦导出矛盾。故 (1) ⇒ (4) 成立。

**(4) ⇔ (5)** 显然。因 \(\Pi_{eq}|D\) 有限，任一一阶正环皆被某极大一阶正环包含。

定理 5.3.2 揭示：判定正规程序是否有有穷环完备集是可判定的。只需检查基程序 \(\Pi_{eq}|D\)（仅有有穷多个环）是否存在某环包含两原子，使一含 \(c_1\) 另一不含，或一含 \(c_2\) 另一不含即可。

### 示例 5.3.4

给定程序 \(\Pi = \{p(x) \leftarrow q(x), q(y) \leftarrow p(y), p(x) \leftarrow r(x), q(y) \leftarrow \text{not } s(y)\}\)，其一阶正环公式集为形如 \(\{p(\xi), q(\xi)\}\) 的原子对集合（\(\xi\) 为变元）。

计算 \(p(x)\) 的外部支承：先在新变元 \(z\) 上标准化所有 \(p\) 的规则：
p(z) \leftarrow z=x, q(x)
p(z) \leftarrow z=x, r(x)

因 \(x\) 为局部变元且出现在环中，以 \(x_1\) 替换之：
p(z) \leftarrow z=x_1, q(x_1)
p(z) \leftarrow z=x_1, r(x_1)

故 \(p(x)\) 的外部支承公式为
\(\exists x_1(x=x_1 \land q(x_1) \land x \neq x_1) \lor \exists x_1(x=x_1 \land r(x))\)
逻辑等价于 \(r(x)\)。

同理，\(q(x)\) 的外部支承等价于 \(\neg s(x)\)。一阶正环 \(\{p(x), q(x)\}\) 的环公式等价于 \(\forall x(p(x) \lor q(x) \rightarrow r(x) \lor \neg s(x))\)。
易见 \(\Pi\) 所有一阶正环公式均等价于上述语句。

# 第六章 回答集程序的归纳学习与遗忘理论

第五章阐述了基于（嵌套）回答集程序的完备性与环公式计算特征，含带变元正规程序的一阶环公式思想。本章将探讨析取回答集程序基于状态变换的归纳学习方法[23]及嵌套回答集程序的知识遗忘理论[24]。

## 6.1 基于状态转换的逻辑程序归纳学习框架

归纳学习旨在从背景知识与大量观察实例（正负例）中提炼普适规律，是知识发现的核心手段。其任务是在假设空间寻找满足约束的假设 \(H\)，使得：
- 背景知识 \(B\) 与 \(H\) 协调，即 \(B \cup H\) 一致；
- 每个正例 \(e^+\) 均为 \(B \cup H\) 的逻辑推论，即 \(B \cup H \vdash e^+\)；
- 每个负例 \(e^-\) 均非 \(B \cup H\) 的逻辑推论。

本节介绍支承语义下析取逻辑程序的归纳学习框架，背景知识为逻辑程序，观察为状态变换对 \((S,T)\)。该框架已用于学习描述基因调控的布尔网络模型。

**注**：规则 \(r_1\) 包孕 \(r_2\)（记 \(r_1 \leq r_2\)）指 \(\exists \theta: r_1\theta \subseteq r_2\)，即 \(\text{Head}(r_1\theta)=\text{Head}(r_2)\) 且 \(\text{Body}(r_1\theta)\subseteq \text{Body}(r_2)\)。若 \(r_1 \leq r_2\) 但 \(r_2 \nleq r_1\)，称 \(r_1\) 真包孕 \(r_2\)（记 \(r_1 < r_2\)）。

为简化，下文仅考虑命题情形。有限命题原子集记为 \(A\)，此时包孕无需置换（或视为恒等置换）。另假设：规则头无经典否定，规则体无缺省否定 `not` 及互补文字。文字 \(l\) 的补 \(\overline{l}\) 定为 \(\overline{p} = \neg p, \overline{\neg p} = p\)；规则头用 “\(\vee\)”，体用 “\(\wedge\)”。

为便利，给定规则 \(r\)，简记 \(\text{Head}(r)\) 为 \(\text{Hd}(r)\)，\(\text{Body}(r)\) 为 \(\text{Bd}(r)\)，\(\text{Bd}^+(r)=A \cap \text{Bd}(r)\)，\(\text{Bd}^-(r)=\{p\in A \mid \neg p \in \text{Bd}(r)\}\)，程序同理。

### 6.1.1 支承类语义

状态 \(S\) 为命题解释（原子集）：\(S\) 中原子为真，否则为假。设 \(P\) 为逻辑程序，\(I\) 为解释，记 \(\text{App}(P,I)=\{r\in P \mid I\vdash \text{Bd}(r)\}\)。

**定义 6.1.1（直接极小后继算子）** 设 \(P\) 为析取逻辑程序。算子 \(T_P^d : 2^A \to 2^{2^A}\) 定义为：对 \(I \subseteq A\)，
\[ T_P^d(I) = \{S \mid S \text{ 是 } \text{Hd}(\text{App}(P,I)) \text{ 的极小命中集}\} \]
\(T_P^d(I)\) 中元素称 \(I\) 在 \(P\) 下的后继状态。\(P\) 的状态变换为二元组 \(\langle I,J \rangle\)，表示 \(J\) 为 \(I\) 的后继。

特别地，\(\text{App}(P,I)=\emptyset\) 时 \(\text{Hd}(\text{App}(P,I))=\emptyset\)，其唯一极小命中集为 \(\emptyset\)。故对任意 \(I\)，\(T_P^d(I) \neq \emptyset\) 恒成立。

**注**：若 \(P\) 为正规程序，\(T_P^d(I) = \{\{q\in \text{Hd}(r) \mid r\in \text{App}(P,I)\}\}\)，此时 \(T_P^d\) 退化为单元素集，与经典 \(T_P\) 算子一致，可视为其推广。下文不引起混淆时将二者等同。

下述命题表明支承模型可用直接后果算子刻画。

**定理 6.1.1** 设 \(P\) 为逻辑程序，\(I \subseteq A\)。\(I\) 为 \(P\) 的支承模型 \(\iff I \in T_P^d(I)\)。

**证明**  
**必要性**：\(I \in T_P^d(I) \Rightarrow I\) 是 \(\text{Hd}(\text{App}(P,I))\) 的极小模型 \(\Rightarrow\) 极小命中集 \(\Rightarrow\) \(P\) 的支承模型。  
**充分性**：\(I\) 为支承模型 \(\Rightarrow I\models P\) 且 \(\forall p \in I, \exists r \in \text{App}(P,I)\) 使 \(\text{Hd}(r) \cap I=\{p\}\) \(\Rightarrow I\) 是 \(\text{Hd}(\text{App}(P,I))\) 的极小命中集 \(\Rightarrow\) 极小模型 \(\Rightarrow I \in T_P^d(I)\)。

以下给出析取程序支承类语义定义。

**定义 6.1.2（支承类）** 非空解释集 \(S\) 为 \(P\) 的支承类，当且仅当它是满足下式的极小集合（按包含序）：
\[ S = \{s \in T_P^d(I) \mid I \in S\} \tag{6-1} \]
对任意 \(s \in S\)，若 \(|T_P^d(s)| = 1\)，称 \(S\) 为简单支承类；若 \(|S|=1\)，则简单支承类为点支承类，其元素称 \(P\) 的严格支承模型。

**注**：支承类无真子集仍为支承类；正规程序的支承模型必为严格支承模型。析取程序支承类概念推广了正规程序的。

**示例 6.1.1** 考察 \(P = \{p \vee q \leftarrow r,\; r \leftarrow p \wedge \neg q\}\)。验证得支承模型为 \(\emptyset, \{p,r\}\)；稳定模型仅 \(\emptyset\)；唯一支承类为 \(\{\emptyset\}\)（即简单且点支承类），故 \(\emptyset\) 为唯一严格支承模型。

稳定模型虽为支承模型，但未必是严格支承模型。如 \(P = \{p \vee q \leftarrow \neg q\}, I = \{p\}\)。\(P\) 关于 \(I\) 的 GL-规约 \(P' = \{p \vee q\}\)，\(I\) 为 \(P'\) 最小模型，故为稳定模型。但 \(\text{Hd}(\text{App}(P, I)) = \{p \vee q\}\)，\(T_P^d(\{p\}) = \{\{p\}, \{q\}\}\)，故 \(\{p\}\) 非严格支承模型。

### 6.1.2 析取基消解与组合消解

本节介绍两种程序变换：析取基消解（推广正规程序基消解）与组合消解。

**定义 6.1.3（析取基消解）** 设 \(r, r'\) 为两条规则。若满足：
1. \(l \in \text{Bd}(r)\) 且 \(\overline{l} \in \text{Bd}(r')\)；
2. \(\text{Bd}(r') \setminus \{\overline{l}\} \subseteq \text{Bd}(r) \setminus \{l\}\)；
3. \(\text{Hd}(r') \subseteq \text{Hd}(r)\)。
则称 \(r\) 可关于 \(r'\) 在文字 \(l\) 上进行析取消解。其结果 \(\text{gr}(r, r')\) 定义为规则 \(\text{Hd}(r) \leftarrow \text{Bd}(r) \setminus \{l\}\)。