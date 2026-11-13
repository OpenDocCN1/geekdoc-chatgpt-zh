
ES(A,L,\Pi_{eq}) \theta | D
= \exists y \left( \overline{Body_{eq}} \theta' \wedge \left( \bigwedge_{q(u) \in Body_{eq}, q(v) \in L} (u \neq v) \right) \right) \theta | D
= \exists y \left( \overline{Body_{eq}} \theta' \theta \wedge \left( \bigwedge_{q(u) \in Body_{eq}, q(v) \in L} (u \theta \neq v \theta) \right) \right) | D
H \nvDash \exists y \left( \overline{Body_{eq}} \theta' \theta' \wedge \left( \bigwedge_{q(u) \in Body_{eq}, q(v) \in L} (u \theta \delta' \neq v \theta) \right) \right)
H_{\delta: x=\theta=\forall Body_{eq}} ( \overline{Body_{eq}} \delta )
= ES(A\theta, L, \Pi_{eq} | D)

其中，

- y 是在 Body 而不在 x 中出现的变元；
- \theta' 是使得 t=x\theta' 的置换；
- \delta' 是 y 中的变元到 D 的变元指派。

### 引理 5.3.4
令 \Pi 是一个正规逻辑程序，D 是包含 Const(\Pi) 的有穷常元集，则 LF(\Pi_{eq})|D 与 LF(\Pi_{eq}|D) 等价，其中，LF(Z) 表示正规程序 Z 的所有环公式的集合。

### 证明
对任意 \Pi_{eq} 的环 L，LF(L, \Pi_{eq})|D 逻辑等价于如下语句构成的理论：

(( \bigvee_{p \in L} p ) \to ( \bigwedge_{p \in L} ES(A, L, \Pi_{eq}) )) \sigma | D

其中，\sigma 是从 var(L) 到 D 的变元指派。

易知，该公式逻辑等价于

(( \bigvee_{p \in L \sigma} p ) \to ( \bigwedge_{p \in L} ES(A, L, \Pi_{eq}) \sigma | D ))

由引理 5.3.3，它也逻辑等价于

(( \bigvee_{p \in L \sigma} p ) \to ( \bigwedge_{p \in L} ES(A \sigma, L \sigma, \Pi_{eq} | D) ))

最后得到的公式就是 LF(L \sigma, \Pi_{eq}|D)，故由引理 5.3.2，LF(\Pi_{eq})|D 与 LF(\Pi_{eq}|D) 逻辑等价。

### 引理 5.3.5
令 \Pi 是一个正规逻辑程序，D 是包含 Const(\Pi) 的有穷常元集，则对于 \Pi 中的任意谓词符号 p, Comp(p, \Pi_{eq})|D 与 { Comp(p(x)\sigma, \Pi_{eq}|D) | \sigma 是变元 x 到 D 的变元指派 } 逻辑等价。

### 证明
不失一般性，设 \Pi 只有一条以谓词 p 为头的规则 r: p(x) \leftarrow Body，则

Comp(p,\Pi_{eq}) \models \forall x(p(x) \leftrightarrow \exists y Body_{eq})

故 Comp(p,\Pi_{eq})|D 是对所有从 x 到 D 的变元指派 \sigma 的如下公式的合取：
p(x)\sigma \leftrightarrow (\exists y Body_{eq}\sigma | D)

它逻辑等价于

p(x)\sigma \leftrightarrow (\bigvee_{\tau} Body_{eq}\sigma\tau)

其中，\tau 是从 y 到 D 的变元指派，此公式即 Comp(p(x)\sigma, \Pi_{eq}|D)。

下面证明定理 5.3.1。

令 IU(D)={eq(d,d)|d \in D} \cup \{\neg eq(d,d') | d, d' 是 D 中不同的常量}，则有 M 是 (\Pi|D) \cup F 的回答集，当且仅当 M 是 (\Pi \cup F)|D 的回答集；
当且仅当 M \cup EQ|D 是 (\Pi_{eq} \cup F)|D \cup EQ|D 的回答集 ( 引理 5.3.1 );
当且仅当 M \cup EQ|D 是 Comp((\Pi_{eq} \cup F)|D \cup EQ|D) \cup LF((\Pi_{eq} \cup F)|D \cup EQ|D) 的命题模型 ( 定理 5.1.3 );
当且仅当 M \cup EQ|D 是 Comp_1((\Pi \cup F)_{eq}|D) \cup LF(\Pi_{eq}|D \cup F \cup EQ|D) \cup IU(D) 的命题模型，这里 IU(D) 逻辑等价于 Comp(eq, \Pi_{eq}|D \cup F \cup EQ|D), Comp_1((\Pi \cup F)_{eq}|D) 是 (\Pi \cup F)_{eq}|D 的不包含 eq 谓词的完备化公式集;

当且仅当 M \cup EQ|D 是 Comp_1((\Pi \cup F)_{eq}|D) \cup LF(\Pi_{eq}|D) \cup IU(D) 的命题模型;

当且仅当 M \cup EQ|D 是 Comp_1((\Pi \cup F)_{eq}|D) \cup LF(\Pi_{eq}|D) \cup IU(D) 的命题模型 ( 引理 5.3.4 和引理 5.3.5 ), 其中，Comp_1((\Pi \cup F)_{eq}) 是 \Pi \cup F 的非 eq 谓词的完备化公式集;

当且仅当 M \cup EQ|D 是 Comp_1((\Pi \cup F)_{eq}) \cup \{ \forall xy(eq(x,y) \leftrightarrow x=y) \} \cup LF(\Pi_{eq})|D 的命题模型 ( \{ \forall xy(eq(x,y) \leftrightarrow x=y) \} | D 逻辑等价于 IU(D) );

当且仅当 M \cup EQ|D 是 (Comp(\Pi \cup F) \cup \{ \forall xy(eq(x,y) \leftrightarrow x=y) \} \cup LF(\Pi))|D 的命题模型 ( Comp(p, \Pi) 与 Comp(p, \Pi_{eq}) 在条件 \forall xy(eq(x,y) \leftrightarrow x=y) 下逻辑等价，且 LF(L, \Pi) 与 LF(L, \Pi_{eq}) 在条件 \forall xy(eq(x,y) \leftrightarrow x=y) 下也逻辑等价，其中 p 是一谓词符号，L 是 \Pi 的一阶正环 );

当且仅当 M \cup EQ|D 是 Comp(\Pi \cup F)|D \cup LF(\Pi)|D \cup \{ \forall xy(eq(x,y) \leftrightarrow x=y) \} |D 的命题模型;

当且仅当 M \cup EQ|D 是 (Comp(\Pi \cup F) \cup LF(\Pi))|D \cup IU(D) 的命题模型;

当且仅当 M 是 (Comp(\Pi \cup F) \cup LF(\Pi))|D 的命题模型 ( M, \Pi 和 F 不包含谓词 eq )。

注意：如果正规逻辑程序 \Pi 包含这样的一阶正环 L，其中的某些原子包含变元，则这个程序可能包含无穷多的环。因为容易证明，将 L 中的变元替换为新的变元仍然是\(\Pi\)的环且这些环所对应的环公式是逻辑等价的，因此，有必要探讨这些环与对应环公式之间的某种等价关系。

### 定义 5.3.7
令 \(L_1, L_2\) 是二原子集，如果存在替换 \(\theta\) 使得 \(L_1\theta=L_2\)，则称 \(L_1\) 包孕 \(L_2\)。如果它们相互包孕对方，则称它们等价。

例如， \(\{p(x)\}\) 与 \(\{p(y)\}\) 是等价的， \(\{p(x_1), p(x_2)\}\) 包孕 \(\{p(x)\}\)，反之则不然。

### 推论 5.3.1
令 \(\Pi\) 是正规程序，\(L_1\) 和 \(L_2\) 是 \(\Pi\) 的等价的一阶正环，则 \(\text{LF}(L_1, \Pi) \models \text{LF}(L_2, \Pi)\)。

### 引理 5.3.6
令 \(\Pi\) 是正规程序，\(L_1\) 是 \(\Pi\) 的一阶正环，\(L_2\) 是原子集且满足 \(\text{Const}(L_2) \subseteq \text{Const}(\Pi)\)。若 \(L_1\) 包孕 \(L_2\)，则 \(L_2\) 也是 \(\Pi\) 的一阶正环，且 \(\text{LF}(L_1, \Pi) \models \text{LF}(L_2, \Pi)\)。

### 证明
令 \(\theta\) 是 \(\text{var}(L_1)\) 上的置换使得 \(L_1\theta=L_2\)。由于 \(L_1\) 是 \(\Pi\) 的一阶正环，则存在 \(L_1=\{A_1, \cdots, A_n\}\) 且 \(L_1\) 是 \(G_\Pi\) 的正环。显然， \(\{A_1\theta, \cdots, A_n\theta\}\) 也是 \(G_\Pi\) 的正环，故 \(L_1\theta\) 是 \(\Pi\) 的一阶正环。

令 \(\text{LF}(L_1, \Pi)=\forall x\Phi(x)\)，其中 \(x\) 是 \(L_1\) 中出现的变元组成的向量，且

\(\Phi(x) = \left[ \left( \bigvee_{p \in L_1} p \right) \to \left( \bigvee_{A \in L_1} S(A, L_1, \Pi) \right) \right]\)

由于 \(L_2=L_1\theta\)，故 \(\text{LF}(L_2, \Pi)\) 是如下公式：

\(\left( \bigvee_{p \in L_1\theta} p \right) \to \left( \bigvee_{A \in L_1} \text{ES}(A\theta, L_1\theta, \Pi) \right)\)

对于 \(p(t) \in L_1\)，显然有 \(\text{ES}(p(t), L_1, \Pi) \theta \equiv \text{ES}(p(t), L_1\theta, \Pi) \theta\)，即

\(\bigvee_{1 \le i \le k} \left( \exists y_i \left[ \text{Body}_i, \delta_1 \wedge \bigwedge_{q(u) \in \text{Body}, \delta_i, q(v) \in L_1} (u \neq v) \right] \theta \right)\)
\(\equiv \bigvee_{1 \le i \le k} \exists y_i \left[ \text{Body}_i, \delta_2 \wedge \bigwedge_{q(u) \in \text{Body}, \delta_2, q(v) \in L_1 \theta} (u \neq v) \right]\)

其中，

- \(\delta_2 = \delta_1 \theta\)；
- \(p(x) \leftarrow Body_1, \cdots, p(x) \leftarrow Body_k\) 是正规化的程序 \(\Pi\) 中的规则；
- 对于所有的 \(1 \le i \le k\)，\(y_i\) 是规则 \(p(x) \leftarrow Body_i\) 中的局部变元，且通过变元重命名，我们可以假定 \(y_i\) 中的变元都不在 \(L_2\) 中出现；所以 \(\text{LF}(L_2, \Pi)=\forall x\Phi(x)\theta \equiv \text{LF}(L_1, \Pi)\theta\)。

令 \(\Pi\) 是正规程序，\(\Delta\) 是 \(\Pi\) 的一阶正环集合的子集。称 \(\Delta\)（关于 \(\Pi\)）是完备的当且仅当对 \(\Pi\) 的任意环 \(L\)，都有 \(S \in \Delta\)，使得 \(S\) 包孕 \(L\)。

例如，对例 5.3.2 的程序 $\Pi$，一阶正环集 $\{\{p(x)\}\}$ 关于 $\Pi$ 是完备的。

### 定理 5.3.2
令 $\Pi$ 是正规程序，$D=\{c_1,c_2\} \cup \text{Const}(\Pi)$，其中 $c_1$ 和 $c_2$ 是不同的不在 $\Pi$ 中出现的常元。则下面的命题是等价的：

- (1) $\Pi$ 有有穷完备的一阶正环集合；
- (2) 存在自然数 $N$，使得对任何 $\Pi$ 的环 $L$， $|\text{var}(L)|\leqslant N$；
- (3) 对 $\Pi$ 的任意环 $L$ 和 $A_1, A_2 \in L$， $\text{var}(A_1)=\text{var}(A_2)$；
- (4) 对 $\Pi_{eq}|D$ 上的任意环 $L$，不存在原子 $A,B \in L$，使得 $c_1 \in \text{Const}(A) \setminus \text{Const}(B)$ 或 $c_2 \in \text{Const}(B) \setminus \text{Const}(A)$；
- (5) 对 $\Pi_{eq}|D$ 的任何极大（包含关系下）环 $L$，不存在原子 $A,B \in L$，使得 $c_1 \in \text{Const}(A) \setminus \text{Const}(B)$ 或 $c_2 \in \text{Const}(B) \setminus \text{Const}(A)$。

以上命题中的 $\Pi_{eq}$ 是将 $\Pi$ 中的 $t_1=t_2$ 重命名为 $\text{eq}(t_1,t_2)$ 而得到的程序，其中 eq 是一个新的二元谓词符号。因此 $\Pi_{eq}$ 中不再出现等号，从而 $\Pi_{eq}|D$ 保留所有因为处理等号而删掉的规则。

### 证明
(1) $\Leftarrow$ (2): 令 $\Delta$ 是 $\Pi$ 的所有一阶正环的集合，其中每个环包含的变元都在 $\{x_1, \cdots, x_N\}$ 中。易知，$\Delta$ 是有穷的。对于 $\Pi$ 的任一环 $L$，总存在 $L'=L\theta$，其中 $\theta$ 是对属于 $\text{var}(L')$ 而不属于 $\{x_1, \cdots, x_N\}$ 的变元用 $\{x_1, \cdots, x_N\} \backslash \text{var}(L')$ 中的变元替换而得到的置换。因为 $|\text{var}(L)|\leqslant N$，易知，该置换存在且 $L' \in \Delta$。所以，$\Delta$ 是 $\Pi$ 的有穷完备的一阶正环集合。

(2) $\Leftarrow$ (3): 由于 $\Pi$ 是有穷的，故 $\Pi$ 仅出现有穷多个谓词符号。令 $N=\max\{n | \text{存在在}\Pi \text{中出现的谓词 } p, \text{使得 } p \text{ 是 } n \text{元谓词}\}$。对于 $\Pi$ 的任意环 $L$ 和 $A_1, A_2 \in L$，根据命题 (3)，$\text{var}(A_1)=\text{var}(A_2)$。因此， $|\text{var}(L)|\leqslant N$。

(3) $\Leftarrow$ (4): 假设存在 $\Pi$ 的环 $L$ 和 $A_1, A_2 \in L$ 满足 $\text{var}(A_1)\neq\text{var}(A_2)$。不失一般性，令 $x \in \text{var}(A_1) \backslash \text{var}(A_2)$

\[ \theta = \{x / c_1\} \cup \{x' / c_2 | x' \in \text{var}(L) \backslash \{x\} \} \]

显然，$L'=L\theta$ 是 $\Pi_{eq}|D$ 的一阶正环且 $c_1 \in \text{Const}(A_1) \backslash \text{Const}(A_2)$，这与命题 (4) 的前提矛盾。

(4) $\Leftarrow$ (1): 令 $\Delta = \{L_1, \cdots, L_n\}$ 是 $\Pi$ 的有穷完备的一阶正环集合，$N=\max\{|\text{var}(L_1)|, \cdots, |\text{var}(L_n)|\}$。易知，对于 $\Pi$ 的任意一阶正环 $L$，存在 $L^* \in \Delta$ 满足：$L^*$ 包含 $L$ 且 $|\text{var}(L)|\leqslant |\text{var}(L^*)|\leqslant N$。

设 $L=\{A_1, \cdots, A_m\}$ 是 $\Pi_{eq}|D$ 的一阶正环且 $c_1 \in \text{Const}(A_1) \backslash \text{Const}(A_2)$，则 $L_1, \cdots, L_{N+1}$ 也是 $\Pi_{eq}|D$ 的一阶正环，其中 $L_i (1 \leqslant i \leqslant N+1)$ 是将 $L$ 中的 $c_1$ 替换为一个新的变元符号 $y_i$ 而得到。显然 $L_1, \cdots, L_{N+1}$ 都包含 $A_2$。由引理 5.3.2 的证明可知， $L_1 \cup \cdots \cup L_{N+1}$ 也是 $\Pi_{eq}|D$ 的一阶正环且 $|var(L_1 \cup \cdots \cup L_{N+1})| > N$。

重复此过程，可以构造出任意长度的一阶正环，这与 $\Delta$ 有有穷完备的一阶正环集合矛盾。

类似地，若 $c_2 \in Const(A_2) \setminus Const(A_1)$，则同样导出与 $\Delta$ 有有穷完备的一阶正环集合矛盾的结论。因此，“(4) $\Leftarrow$ (1)”成立。

(4) $\Leftrightarrow$ (5) 显然成立。这是因为 $\Pi_{eq}|D$ 是有穷的，故对此程序任意一阶正环，有一个极大的一阶正环包含它。

定理 5.3.2 表明，一个正规程序是否有有穷的环完备集是可判定的，即只需要检查 $\Pi_{eq}|D$（这是一个基程序）是否存在一个环（只有有穷多个），它包含两个原子，其中一个原子中包含 $c_1$ 而另一个原子不包含 $c_1$，或者其中一个原子包含 $c_2$ 而另一个原子不包含 $c_2$。

### 例 5.3.4
对于程序 $\Pi = \{p(x) \leftarrow q(x), q(y) \leftarrow p(y), p(x) \leftarrow r(x), q(y) \leftarrow \text{not } s(y)\}$，其一阶正环公式集是形如 $\{p(\xi), q(\xi)\}$ 的原子对的集合，其中，$\xi$ 是变元。为计算 $p(x)$ 的外部支承，首先在某个新变元（如 $z$）上标准化所有关于 $p$ 的规则：

p(z) \leftarrow z=x, q(x)
p(z) \leftarrow z=x, r(x)

因为 $x$ 在上述规则中是局部变元且它出现在环中，我们用另一变元（如 $x_1$）替换它：

p(z) \leftarrow z=x_1, q(x_1)
p(z) \leftarrow z=x_1, r(x_1)

于是， $p(x)$ 的外部支承公式是

$\exists x_1(x=x_1 \land q(x_1) \land x \neq x_1) \lor \exists x_1(x=x_1 \land r(x))$

它逻辑等价于 $r(x)$。

类似地， $q(x)$ 的外部支承公式逻辑等价于 $\neg s(x)$。一阶正环 $\{p(x), q(x)\}$ 的环公式逻辑等价于 $\forall x(p(x) \lor q(x) \rightarrow r(x) \lor \neg s(x))$。

容易看出， $\Pi$ 中所有一阶正环的环公式逻辑等价于上述一阶语句。
