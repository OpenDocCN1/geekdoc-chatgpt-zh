## 归纳论证与支承集合的等价性证明

鉴于 $X_0 \subseteq X$、$L \in X$ 以及 $X_1$ 属于 $\text{Head}(\{ C \leftarrow B, F \in \Pi' \mid B \subseteq X_0 \})$ 的稳定模型集 $SM$，可推得 $L \in X_1$。进而，$B \subseteq S_1$ 且规则 $C \leftarrow B, F$ 落入 $\Pi'(X_2)$ 中。

**归纳步骤**  
设规则 $C \leftarrow B, F \in \Pi'$ 满足 $\max \{ \lambda(B) \} = n > 0$。因 $\Pi' \subseteq GR(S, \Pi)$，故 $B \neq \emptyset$ 且 $B \subseteq S$。借助 $S$ 关于 $\Pi'$ 的支承性与 $\Pi'$ 的紧凑性，对于任意满足 $\lambda(L)=n$ 的 $L \in B$，均存在规则 $C_L' \leftarrow B_L', F_L' \in \Pi'$，使得 $GAtom_C \cap S = \{ L \}$ 且 $\max \{ \lambda(B_L') \} \leqslant n-1$。

依据归纳假设，$C_L' \leftarrow B_L', F_L' \in \Pi'(X_{n+1})$。由于 $X_{n+1} \subseteq X$、$L \in X$ 以及 $X_{n+1} \in SM(\text{Head}(\{ C \leftarrow B, F \in \Pi' \mid B \subseteq X_n \}))$，从而 $L \in X_{n+1}$。因此 $B \subseteq X_{n+1}$ 且 $C \leftarrow B, F \in \Pi'(X_{n+2})$。

接下来，论证 $S \subseteq X$。因为 $S$ 是 $\Pi'$ 支承的，所以对任意 $L \in S$，存在规则 $C \leftarrow B, F \in \Pi'$，使得 $GAtom_C \cap S = \{ L \}$ 且 $S \models_n (B, F)$。令 $\lambda(L) = n < k$，则 $\max \{ \lambda(B) \} \leqslant k-1$。依据前述“归纳步骤”的证明，$C \leftarrow B, F \in \Pi'(X_{k+1})$。鉴于 $X_{k+1} \subseteq X$ 且 $L \in S$，存在 $M \in SM(\text{Head}(\Pi'(X_{k+1})))$ 使得 $L \in M$。这说明 $L \in X$，故 $S \subseteq X$。

显而易见，$X \subseteq S$，因此 $X=S$。不难看出，$\Pi'$ 是 $S$-相容的，进而 $\Pi'$ 是强 $S$-相容的。

最后，证明 $GR(S, \Pi)$ 是强 $S$-相容的。为此，只需验证 $(GR(S, \Pi), S) \in \mathcal{A}(GR(S, \Pi))$。

事实上，对任意 $r=C \leftarrow B, F \in GR(S, \Pi)$，因 $S \vDash_n B$，故有 $(\Pi' \cup \{r\}, S) \in \mathcal{A}(\Pi' \cup \{r\})$。

因此，由引理 5.2.6 可得 $(GR(S, \Pi), S) \in \mathcal{A}(GR(S, \Pi))$。

根据引理 5.2.9，可获得回答集特征的另一种等价表述。

### 定理 5.2.4

设 $\Pi$ 为嵌套程序，$S$ 为 $\Pi$ 支承且封闭于 $\Pi$ 的协调文字集。$S$ 是 $\Pi$ 的一个回答集，当且仅当存在 $\Pi' \subseteq GR(S, \Pi)$，使得 $S \in SM(\text{Head}(\Pi))$ 且 $\Pi'$ 在 $S$ 上紧凑。

**证明**  
“仅当”部分：依定义 5.2.6 和定理 5.2.4 显然成立。  
“当”部分：可由定理 5.2.4 和引理 5.2.9 推导得出。

## 5.2.4 嵌套公式的完备性与环公式

为定义嵌套公式的完备性，我们仅考察不含经典否定 $\neg$ 的公式、规则、程序与约束，分别称为正（嵌套）公式、正（嵌套）规则、正（嵌套）程序和正约束。

设 $F$ 为正公式，记 $F^{CL}$ 为将 $F$ 中的“，”、“;”、“not”分别替换为“$\wedge$”、“$\vee$”、“$\neg$”后得到的经典公式。具体转换规则如下：

- 若 $F$ 是原子，则 $F^{CL}=F$；
- 若 $F=G,H$，则 $(G,H)^{CL}=G^{CL} \wedge H^{CL}$；
- 若 $F=(G;H)$，则 $(G;H)^{CL}=G^{CL} \vee H^{CL}$；
- 若 $F=$ not $G$，则 $(\text{not } G)^{CL}=\neg(G^{CL})$。

对任意正规则 $r=C \leftarrow B, F$ 与正程序 $\Pi$，定义 $r^{CL}=B^{CL} \wedge F^{CL} \to C^{CL}$，$\Pi^{CL}=\{r^{CL} \mid r \in \Pi\}$。

特别地，$(C \leftarrow)^{CL}=C^{CL}$。针对正约束 $\leftarrow B, F$，有 $(\leftarrow B, F)^{CL}=B^{CL} \wedge F^{CL} \to \bot$，等价于 $\neg(B^{CL} \wedge F^{CL})$。

后续讨论均限定于不含正约束的正嵌套程序。易于验证，所有结论对包含正约束的正嵌套程序同样适用。

### 定义 5.2.10

给定有穷正嵌套程序 $\Pi$，其完备定义为：

\[
\Pi^{CL} \cup \{p \to \bigvee_{p \in G \in Atom_c} (B^{CL} \wedge F^{CL} \wedge (\wedge_{q \in G \in Atom_c \backslash \{p\}} \neg q)) \mid C \leftarrow B, F \in \Pi\}
\tag{5-2}
\]

记作 $\text{Comp}(\Pi)$。其中，若 $B$ 或 $F$ 为空，则对应的 $B^{CL}$ 或 $F^{CL}$ 视为 $\top$；若 $B^{CL} \wedge F^{CL} \wedge (\wedge_{p' \in C \backslash \{a\}} \neg p')$ 为空，则其析取视为 $\bot$。

### 例 5.2.4（续）

$\text{Comp}(\Pi) = \{p, \neg s \to q, p \wedge t \wedge \neg q \to r, s \wedge r \to t, p \to \top, q \to \neg s, r \to p \wedge t \wedge \neg q, t \to s \wedge r, \neg s\}$。不难发现，$\text{Comp}(\Pi)$ 逻辑等价于 $\{p, q \leftrightarrow \neg s, r \to p \wedge t \wedge \neg q, t \to s \wedge r, \neg s\}$。$\Pi$ 的回答集与 $\text{Comp}(\Pi)$ 的模型均为 $\{p,q\}$。

### 例 5.2.5

程序 $\Pi=\{ (p:r) \leftarrow q, q \leftarrow p, p \leftarrow \text{not } r, r \leftarrow r \}$ 拥有唯一回答集 $\{p, q\}$。然而 $\text{Comp}(\Pi) = \{ q \to p \vee r, p \to q, \neg r \to p, r \to r, q \to p, (p \to (q \wedge \neg r)) \vee (r \to (q \wedge \neg p)) \}$ 却包含模型 $\{p, q\}$ 和 $\{r\}$。

### 定理 5.2.5

对任一有穷正程序 $\Pi$，原子集 $S$ 封闭于 $\Pi$ 且为 $\Pi$ 支承的，当且仅当 $S$ 是 $\text{Comp}(\Pi)$ 的模型。

**证明**  
**充分性（“当”）**  
对任一 $C \leftarrow B, F \in \Pi$，若 $S \models_n B, F$，则 $S \models B^{CL} \wedge F^{CL}$。因 $S$ 是 $\Pi^{CL}$ 的模型，故 $S \models C$。因此，$S$ 封闭于 $\Pi$。对任意 $p \in S$，有 $S \models \{p \vee \bigvee_{p \in GAtom_c} (B^{CL} \wedge F^{CL} \wedge (\wedge_{p' \in GAtom_c \backslash \{p\}} \neg p')) \mid C \leftarrow B, F \in \Pi\}$。故存在 $C' \leftarrow B', F' \in \Pi$，使得 $p \in GAtom_c$，$S \models B'^{CL} \wedge F'^{CL}$，且对 $p' \in GAtom_c \backslash \{p\}$，$S \models \neg p'$，从而 $GAtom_c \cap S = \{ p \}$。所以，$S$ 是 $\Pi$ 支承的。

**必要性（“仅当”）**  
对任一 $r = C \leftarrow B, F \in \Pi$，若 $S \models B^{CL} \wedge F^{CL}$，则 $S \models_n B, F$。因 $S$ 封闭于 $\Pi$，故 $S \models C$。因此 $S$ 是 $\Pi^{CL}$ 的模型。对任一 $p \in S$，由 $S$ 是 $\Pi$ 支承的，存在 $C \leftarrow B, F \in \Pi$ 使得 $GAtom_c \cap S = \{ p \}$ 且 $S \models_n B, F$。于是，$S \models B^{CL} \wedge F^{CL} \wedge (\wedge_{p' \in GAtom_c \backslash \{p\}} \neg p')$。若 $p \in GAtom_c \backslash S$，则 $S \models \neg p$，即 $S$ 是 $p \to \bot$ 的模型，故对任一 $p \in GAtom$，$S \models \{p \vee \bigvee_{p \in GAtom_c} (B^{CL} \wedge F^{CL} \wedge (\wedge_{p' \in GAtom_c \backslash \{p\}} \neg p')) \mid C \leftarrow B, F \in \Pi\}$。这表明 $S$ 是 $\text{Comp}(\Pi)$ 的模型。

### 引理 5.2.10

对任意有穷正嵌套程序 $\Pi$ 和任意原子集 $S$，若 $S$ 是 $\Pi$ 的回答集，则 $S$ 是 $\text{Comp}(\Pi)$ 的模型。

**证明**  
由推论 5.2.4 和定理 5.2.5 直接得证。

给定正嵌套程序 $\Pi$ 与非空原子集 $X$，设原子集 $S$ 满足 $(\Pi(S), S) \in \Lambda(\Pi)$。若 $X \subseteq S$，则称 $X$ 关于 $S$ 是基于 $\Pi$ 的；若 $X \cap S = \emptyset$，则称 $X$ 关于 $S$ 是无基于 $\Pi$ 的。依据定理 5.2.4*，对程序 $\Pi$ 及其模型 $X$，若 $X$ 包含一个无基于 $\Pi$ 的子集（即对任意 $(GR(X, \Pi), S) \in \Lambda(GR(X, \Pi))$，$S \neq X$），则 $X$ 不是 $\Pi$ 的回答集。因为 $X$ 是 $GR(X, \Pi)$ 支承的，故其每一个无基于 $\Pi$ 的子集必包含一个“环”。

### 定义 5.2.11

正嵌套程序 $\Pi$ 的正相依图 $G_\Pi$ 定义为如下有向图：顶点集为 $GAtom_\Pi$；对任一规则 $C \leftarrow B, F \in \Pi$，任一 $p \in GAtom_c$ 与任一 $p' \in GAtom_b$ 之间存在一条从 $p$ 指向 $p'$ 的弧，$G_\Pi$ 中不含重复弧。称非空原子集 $Z$ 是 $G_\Pi$ 中的一个正环，当且仅当对任意 $p, p' \in Z$，在 $G_\Pi$ 中存在从 $p$ 到 $p'$ 的路径，且路径上所有顶点均属于 $Z$。对有穷程序 $\Pi$，$G_\Pi$ 中的正环 $Z = \{p_1, \ldots, p_k\}$ ($k \geqslant 1$) 称 $k$ 为 $Z$ 的长度。特别地，若 $Z$ 为单子，即 $Z=\{p\}$，称 $Z$ 为伪环，其长度为 1。

正嵌套程序的正环与紧凑性有着紧密联系。

### 引理 5.2.11

设 $\Pi$ 为正程序，$S \in SM(\text{Head}(\Pi))$。若 $\Pi$ 在 $S$ 上紧凑，则 $G_\Pi$ 中不存在正环 $Z \subseteq S$。若 $\Pi$ 有穷，则 $\Pi$ 在 $S$ 上紧凑，当且仅当 $G_\Pi$ 中无正环 $Z \subseteq S$。

**证明**  
假设 $G_\Pi$ 中存在正环 $Z=\{p_1, \ldots, p_k, \ldots\} \subseteq S$ ($k \geqslant 1$)。因 $S \in SM(\text{Head}(\Pi))$，故对任一 $p \in S$，存在某规则 $C \leftarrow B, F \in \Pi$ 使得 $p \in GAtom_C \cap S$。设 $C_k \leftarrow B_k, F_k \in \Pi$ ($1 \leqslant k$)、$C' \leftarrow B', F' \in \Pi$，满足 $p_1 \in GAtom_{C_1 \cap B'}$、$p_k \in GAtom_{C_k \cap B_{k-1}}$ ($2 \leqslant k$)。由 $\Pi$ 的紧凑性，存在层次映射 $\lambda$，使得 $\lambda(p_1) > \lambda(p_2) > \ldots > \lambda(p_k) > \ldots > \lambda(p_1)$，产生矛盾。

对于定理第二部分，只需证明“当”方向成立。设 $|\Pi|=m$，构造序列：

$S_0 = \emptyset$

对 $n \geqslant 0$，$S_{n+1} = \{ p \in \mathbf{GAtom}_C \cap S \mid C \leftarrow B, F \in \Pi, \forall p' \in B (p' \in (\mathbf{GAtom}_{\text{Head}(\Pi)} \cap S) \to p' \in \cup_{i \leqslant n} S_i) \}$

$S' = \cup_{0 \leqslant n \leqslant m} S_n$