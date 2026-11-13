# SLD消解与基本程序：从确定逻辑到否定即失败的语义桥梁

[PRE0]

在SLD消解的实际操作中，选择哪个原子进行展开至关重要。以此为例，若总选取约束末尾的原子消解，流程可终止；反之若固定选首个原子，可能陷入无限树的构建中。一般而言，给定确定程序 $P$，总存在一个选择函数 $R$，它能从任一确定约束 $G$ 中挑出一个待消解原子。理论证明了**选择规则的独立性**：无论 $R$ 如何选取，只要 $P \cup G$ 不可满足，必能找到一条基于该规则的成功消解路径。

## SLD演算：用证明论刻画成功与失败

命题确定程序的SLD消解过程，可形式化为一套**SLD演算系统**。此处约定：空集 $\varnothing$ 等价于真值 $T$；$v=s$ 与 $v=f$ 分别代表“成功”与“失败”；$G$ 为目标集。于是，$v=s\,G$ 与 $v=f\,G$ 成为演算的推导对象。

**公理**：$v=s\,\varnothing$。

**推理规则** 分成功、失败两类：

*   **(S) 成功规则**：若对某个 $B \in \text{Bodies}(A)$ 有 $v=s\,(G \cup B)$，则可推得 $v=s\,(G \cup \{A\})$。
*   **(F) 失败规则**：若对**所有** $B \in \text{Bodies}(A)$ 均有 $v=f\,(G \cup B)$，则可推得 $v=f\,(G \cup \{A\})$。

此处 $\text{Bodies}(A) = \{\text{Body}(r) \mid r \in \Pi, \text{Head}(r)=A\}$。规则(F)的前提数量等于 $\text{Bodies}(A)$ 的基数（可为0或无穷）。特别地，若 $\text{Bodies}(A)$ 为空（即 $A$ 仅出现在规则体中，从未作为头部出现），则前提为空，结论直接成立。

仿照经典逻辑“可推导”定义，SLD演算给出了成功/失败的可推导性。注意：因规则可无限应用，此处的“推导”定义为**不含无限分支的树**（树本身可无限大）。所谓无限分支，即节点为推导对象，且每节点要么是叶子兼公理，要么能应用规则生成后继。

**定义**：设 $\Pi$ 为命题确定程序，$G$ 为目标。
*   若 $v=s\,G$ 在 $\Pi$ 的SLD演算中可推导，称 $G$ 关于 $\Pi$ **成功**；
*   若 $v=f\,G$ 可推导，称 $G$ 关于 $\Pi$ **失败**。

### 实例演示：成功与失败的分水岭

**例 2.3.4** 程序 $\Pi = \{q,\; p \leftarrow q,\; r \leftarrow s,\; r \leftarrow p,\; q\}$，目标 $G=\{r\}$。

演算过程：
1.  $v=s\,\varnothing$ (公理)
2.  $v=s\,\{q\}$ (因 $\text{Bodies}(q)=\varnothing, G=\varnothing$)
3.  $v=s\,\{p, q\}$ (因 $\text{Bodies}(p)=\{\{q\}\}, G=\{q\}$)
4.  $v=s\,\{r\}$ (因 $\text{Bodies}(r)=\{\{s\},\{p,q\}\}$, 取 $B=\{p,q\}, G=\varnothing$)

结论：$\{r\}$ 成功，$r$ 是 $\Pi$ 的逻辑推论。

**修改程序**：将首条规则改为 $q \leftarrow s$，得 $\Pi^*$。此时：
*   $\models_F \{s\}$ (F规则零前提应用，$\text{Bodies}(s)=\varnothing$)
*   $\models_F \{q\}$ (因 $\text{Bodies}(q)=\{\{s\}\}$)
*   $\models_F \{p, q\}$ (因 $\text{Bodies}(p)=\{\{q\}\}$)
*   $\models_F \{r\}$ (因 $\text{Bodies}(r)=\{\{s\},\{p,q\}\}$, 所有分支均失败)

结论：$\{r\}$ 失败，$r$ 非 $\Pi^*$ 的逻辑推论。

> **关键注记**：对程序 $A \leftarrow A$，目标 $\{A\}$ **既不成功也不失败**。这揭示了SLD演算无法处理的“未知”区域。

### 核心元定理：可靠、完备与“不完全”的失败

**定理 2.3.8** 对任意命题确定程序 $\Pi$ 及原子 $A$：
1.  **一致性**：无目标同时成功且失败；
2.  **成功的完备性**：$\{A\}$ 成功 $\iff$ $A$ 是 $\Pi$ 的结论（即 $A \in C_n(\Pi)$）；
3.  **失败的可靠性**：$\{A\}$ 失败 $\implies$ $A$ 非 $\Pi$ 的结论。

**证明要旨**：
*   (1) 归纳于 $\models_S G$ 结构。基始：公理推导出 $G=\varnothing$，但 (F) 规则要求 $G \neq \varnothing$，故 $\models_F \varnothing$ 不可能。归纳步：若 $G$ 由 (S) 得到，假设 $\models_F G$ 成立，则必由 (F) 导出，要求所有分支 $\models_F G' \cup B$ 成立，与归纳假设矛盾。
*   (2) 归纳证明：$G$ 成功 $\iff G \subseteq T_\Pi \uparrow \omega$。基始 $G=\varnothing$ 显然成立。归纳步利用 $T_\Pi$ 算子定义：$A \in T_\Pi \uparrow \omega \iff \forall B \in \text{Bodies}(A), B \subseteq T_\Pi \uparrow \omega$，即可建立等价。
*   (3) 由 (1)(2) 直接得出：若 $\{A\}$ 失败，则不可能成功，故 $A \notin C_n(\Pi)$。

**语义启示**：SLD演算的**成功规则是可靠且完备**的（捕获所有逻辑推论）；**失败规则虽可靠，却不完备**——如 $A \leftarrow A$ 中 $A$ 非结论，却也不失败。这正是引入“部分赋值”与“否定即失败”机制的动因。

---

### 2.3.2 部分赋值：引入经典否定，迈向基本程序

现实建模常需在规则体中表达“非……”，如定义集合不相等：
$$ \text{Different}(x,y) \leftarrow \text{member}(z,x), \neg\text{member}(z,y) $$
允许经典否定 $\neg$ 现身规则体，即得**扩展确定子句** $A \leftarrow L_1,\dots,L_n$（$A$ 为原子，$L_i$ 为文字）及**扩展确定程序**。扩展约束形式为 $\leftarrow L_1,\dots,L_n$。

**逻辑解释**：设变元为 $x_1,\dots,x_k$，规则与约束分别对应：
$$ (\forall \vec{x})(\overline{L_1} \lor \cdots \lor \overline{L_n} \lor A), \quad (\forall \vec{x})(\overline{L_1} \lor \cdots \lor \overline{L_n}) $$
其中 $\overline{A}=\neg A,\; \overline{\neg A}=A$。

若进一步允许否定出现在**规则头部**，便得到**基本程序规则**：
$$ L_0 \leftarrow L_1,\dots,L_n \quad (n \ge 0) \tag{2-6} $$
记作 $\text{Head} \leftarrow \text{Body}$。对应**基本约束** $\leftarrow L_1,\dots,L_n$。

为赋予其语义，需拓展一阶模型概念。对公式集 $\Gamma$ 与赋值 $v$：
*   若 $\Gamma^v=1$，则 $S=\{L \in \mathbf{GLit} \mid L^v=1\}$ 为 $\Gamma$ 的模型；
*   若对所有 $v$ 均 $\Gamma^v=0$，则全文字集 $\mathbf{GLit}$ 为模型。
特别地，对 **Herbrand赋值** $v$，模型形式为：
$$ S = \{A \in B_\Gamma \mid A^v=1\} \cup \{\neg A \mid A \in B_\Gamma, A^v=0\} $$
若对所有 Herbrand 赋值 $\Gamma^v=0$，则 $B_\Gamma \cup \{\neg A \mid A \in B_\Gamma\}$ 为 Herbrand 模型。

基本规则与约束的逻辑解释同前，仅 $L_0$ 可为文字。基本程序 $\Pi$ 的逻辑解释 $\Pi_P$ 为其全体子句解释之集。**本书默认讨论命题基本程序**，即视含变元规则为其基例化全体的简写。

#### 核心概念：协调、封闭、支承与结论集

**定义 2.3.7**
*   **协调**：文字集 $S$ 不含互补文字对（$A$ 与 $\neg A$ 不共存）。
*   **逻辑封闭**：$S$ 协调，或 $S = \text{Lit}$（全文字集）。
*   **关于 $\Pi$ 封闭（即 $\Pi$ 的模型）**：对每条规则 $L_0 \leftarrow L_1,\dots,L_n \in \Pi$，若 $\text{Body} \subseteq S$ 则 $\text{Head} \in S$。
*   **结论集 $C_n(\Pi)$**：关于 $\Pi$ 封闭且逻辑封闭的**最小**文字集（存在性由 Zorn 引理保证）。
*   **支承**：$S$ 中每个 $L$ 均有规则 $\text{Head} \leftarrow \text{Body} \in \Pi$ 使 $\text{Head}=L$ 且 $\text{Body} \subseteq S$。
*   **约束封闭**：$S$ 不协调，或虽协调但约束体 $\{L_1,\dots,L_n\} \not\subseteq S$。

基本性质：$\text{Lit}$ 总是 $\Pi$ 的模型；$\varnothing$ 总被 $\Pi$ 支承。所有模型构成的族 $W$ 非空，按反向包含序 $\supseteq$ 极大元即为 $C_n(\Pi)$，故其存在且唯一。

*   $\Pi$ **协调** $\iff C_n(\Pi)$ 协调；否则**不协调**。
*   **头协调**（所有规则头部集合协调）$\implies$ 程序协调。

---

### 从基本程序到确定程序：DEF 映射的桥梁作用

为复用确定程序的成果，我们构造映射 **DEF** 将基本程序“编译”为确定程序。

**构造**：
对每个原子 $A \in \text{Atom}$，引入新符号 $A^+ \notin \text{Atom}$，集合记为 $\text{Atom}^+$。定义 $\text{DEF}: \text{Lit} \to \text{Atom} \cup \text{Atom}^+$：
$$ \text{DEF}(A) = A, \quad \text{DEF}(\neg A) = A^+ $$
自然拓展至文字集、规则、约束及程序。

[PRE1]

**性质**：
1.  $\text{DEF}$ 是 $\text{Atom}$ 上基本程序集到 $\text{Atom} \cup \text{Atom}^+$ 上确定程序集的**一一映射**。
2.  转换仅需将每个负文字 $\neg A$ 替换为 $A^+$。
3.  **矛盾规则集 $\text{Contr}$**：含 $A \leftarrow B, B^+$ 与 $A^+ \leftarrow B, B^+$（$A,B$ 为任意不同原子）。任意协调文字集 $S$，$\text{DEF}(S)$ 与 $\text{DEF}(\text{Lit})$ 均关于 $\text{Contr}$ 逻辑封闭。语义上：**任何协调文字集均满足 $\text{Contr}$**。
4.  $S$ 协调 $\iff \text{DEF}(S)$ 不含形如 $\{A, A^+\}$ 的原子对。

### 定理 2.3.9：结论集的精确对应

设 $\Pi$ 为基本程序，则：

1.  **协调情形**：
    $$ C_n(\Pi) = \{A \mid A \in \text{Atom} \cap \text{DEF}(C_n(\Pi))\} \cup \{\neg A \mid A^+ \in \text{Atom}^+ \cap \text{DEF}(C_n(\Pi))\} $$
2.  **不协调情形**：$C_n(\Pi) = \text{Lit}$。
3.  **核心等式**：$\text{DEF}(C_n(\Pi)) = C_n(\text{DEF}(\Pi) \cup \text{Contr})$。
    *   **特别地**，若 $\Pi$ 协调，则 $\text{DEF}(C_n(\Pi)) = C_n(\text{DEF}(\Pi))$。

> **深意**：此定理确立了基本程序语义与确定程序语义的**严格等价**。通过 DEF 映射与矛盾规则 Contr 的引入，我们将包含经典否定的复杂推理，完美归约到了已被充分理解的 SLD 消解框架之上。这为后续“稳定模型”、“良基语义”等非单调推理理论奠定了坚实的语法-语义基石。