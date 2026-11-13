

在 5.3 节中，程序的正环及其对应的正环公式，都是定义在实例化后的程序上。对于例 5.3.1 的 Hamilton 程序的环，首先要用其问题实例部分提到的常元以实例化出现在描述问题的规则中的变元。于是，上面的图 G 产生三个正环

```
{reached(1), reached(2)}
{reached(3), reached(4)}
{reached(1), reached(2), reached(3), reached(4)}
```

显然对于不同的图可能产生不同的正环。

由于程序的实例化可能会引起程序规模的指数空间增长，这成为计算回答集的一个瓶颈。注意到，2.3.2 节中有穷扩展程序的 Clark 完备化是直接定义在包含变元的程序上的，若能类似于基正规程序的环公式思想，对包含变元的回答集程序引入类似于 Clark 完备化的一阶完备化理论和相应的一阶环公式以描述程序的回答集，则可以利用一阶自动定理证明器来计算其模型（即回答集），从而避免程序的实例化瓶颈。然而，对一个由有穷条包含变元的规则组成的正规程序，即使其完备是有穷的一阶公式集，因为它可能有无穷多个逻辑不等价的环公式，这致使它的一阶正环公式集可能是无穷的。因此，重要的问题：确定这样的程序是否只有有穷个逻辑不等价的一阶正环公式。值得庆幸的是，确实有算法解决这一问题。这些想法导致了回答集程序一阶环与环公式及一阶回答集等思想的形成与发展。

若无特别声明，出现在本节的正规程序总是指包含变元但不包含真函数符号（非零元函数符号）且可能包含否定符号的有穷条正规程序规则组成的程序，通过引入这样的程序的一阶完备和一阶环与环公式的概念，导出由这些概念描述程序的一阶回答集（即可能包含变元的一阶语句集）的重要结果。类似的思想可以拓广到嵌套程序。

设 E 是回答集程序规则（或回答集程序）或一阶公式（或一阶公式集），var(E) 和 Const(E) 分别表示 E 中出现的变元符号和常元符号集。为了简便，这里用小写粗体表示向量，如 $\boldsymbol{x}$ 表示向量 $\langle x_1, \cdots, x_n \rangle$；两个相同长度的有穷长向量 $s$ 和 $t$ 相等，记为 $s=t$，表示它们的对应分量分别相等，即 $s_1=t_1, \cdots, s_n=t_n$。

令 $\phi$ 是一阶语句，$D$ 是有穷常元集。$\phi$ 在 $D$ 上的实例化，记为 $\phi|_D$，是按照如下递归定义生成的公式：

- 若 $\phi$ 不含任何量词，则 $\phi|_D$ 是分别代替 $d=d$ 为 $\top$、$d_1=d_2$ 为 $\bot$ 的结果，其中 $d, d_1$ 和 $d_2$ 是 $D$ 中的常元且 $d_1$ 不同于 $d_2$（唯一命名假设）；
- $\exists x\, \phi|_D = (\bigvee_{d\in D} \phi[d/x])|_D$，其中 $\phi[d/x]$ 是用 $d$ 替换 $\phi$ 中所有 $x$ 的自由出现得到的结果；

- $(\varphi_1 \vee \varphi_2)|_D = \varphi_1|_D \vee \varphi_2|_D$；
- $(\neg \varphi)|_D = \neg (\varphi|_D)$。

其他联结词，比如 $\wedge$ 和 $\forall$，通常作为缩写，即 $\varphi_1 \wedge \varphi_2$ 和 $\forall x\, \phi$ 分别是 $\neg (\neg \varphi_1 \vee \neg \varphi_2)$ 和 $\neg \exists x\, \neg \phi$ 的缩写。

一阶闭理论（即一阶语句集）$T$ 在 $D$ 上的实例化是 $T$ 中的语句在 $D$ 上的实例化的集合，记为 $T|_D$。

给定一变量集 $V$ 和一常量集 $D$，$V$ 到 $D$ 的映射称为 $V$ 在 $D$ 上的变元赋值（或变元指派）。令 $r$ 是一规则，$\theta$ 是一变元指派，则 $r\theta$ 是用 $\theta$ 中变元的值代替 $r$ 中的对应变元而得到结果。

我们用 EQ 表示等词的一阶闭理论（包含描述自反、对称和传递的一阶公式）。若 $\Pi$ 是正规逻辑程序，$\Pi|_D$ 表示程序 $\{r\theta \mid r \in \Pi,\ \theta \text{ 是 } \text{var}(r) \text{ 到 } D \text{ 的变元指派}\}$。

例如，给定 $D=\{a,b\}$，$\forall x(p(x) \leftarrow x=a)$ 在 $D$ 上的实例化逻辑等价于 $p(a) \wedge \neg p(b)$。

对于正规程序 $\Pi=\{p(a) \leftarrow q(x,y)\}$，$\Pi|_D$ 是

$$\{ p(a) \leftarrow q(a,a),\ p(a) \leftarrow q(a,b),\ p(a) \leftarrow q(b,a),\ p(a) \leftarrow q(b,b) \}$$

### 定义 5.3.1
令 $\Pi$ 是正规程序，$r=p(t) \leftarrow \text{Body} \in \Pi$，其中 $t=\langle t_1, \cdots, t_n \rangle$，$t_1, \cdots, t_n$ 是项。$r$ 的标准化是规则 $p(\boldsymbol{x}) \leftarrow t=\boldsymbol{x} \cup \text{Body}$，其中 $\boldsymbol{x}=\langle x_1, \cdots, x_n \rangle$ 且 $x_1, \cdots, x_n$ 是两两互不相同且不在 $r$ 中出现的变元。$\Pi$ 的标准化是 $\Pi$ 中所有规则标准化后的结果。称出现在规则 $r$ 中但不出现在 $\text{Head}(r)$ 中的变元为局部变元。

以上讨论的程序总是有穷的，通过重命名，可以使得 $\Pi$ 的每条标准化后的规则头中的变元至多出现 $x_1, \cdots, x_n$ 且它们都不在 $\Pi$ 中出现。以下总假设程序都是标准化的。

通过程序的标准化，程序的一阶完备化可定义如下。

### 定义 5.3.2
令 $\Pi$ 是正规程序，$p$ 是 $\Pi$ 中出现的谓词。$p$ 关于 $\Pi$ 的完备，记为 $\text{COMP}(p, \Pi)$，是如下公式：

$$\forall \boldsymbol{x} \left( p(\boldsymbol{x}) \leftrightarrow \bigvee_{1 \le i \le k} \exists \boldsymbol{y}_i\, \overline{\text{Body}_i} \right)$$

其中，

- $p(\boldsymbol{x}) \leftarrow \text{Body}_1, \cdots, p(\boldsymbol{x}) \leftarrow \text{Body}_k$ 是 $\Pi$ 的标准化结果的规则头中出现谓词 $p$ 的所有规则；
- $\overline{\text{Body}_i}$ 是 $\text{Body}_i$ 中所有元素的合取式，并且其中的 “not” 用 “$\neg$” 替换；
- $\boldsymbol{y}_i$ 是规则 $p(\boldsymbol{x}) \leftarrow \text{Body}_i$ 中的所有局部变元 $y_1, \cdots, y_m$ 组成的向量 $\langle y_1, \cdots, y_m \rangle$。

特别地，若 $p$ 不出现在 $\text{Head}(\Pi)$ 中，则 $\text{Comp}(p,\Pi)$ 是 $\forall x\, \neg p(x)$。

程序 $\Pi$ 的完备化，记为 $\text{Comp}(\Pi)$，是 $\Pi$ 中所有谓词完备化公式的集合，即

$$\text{Comp}(\Pi)=\{\text{Comp}(p,\Pi) \mid p \text{ 是 } \Pi \text{ 中出现的谓词}\}$$

注意：这里 $\text{Comp}(\Pi)$ 并没有包含等词公理。

### 定义 5.3.3
令 $\Pi$ 是正规程序，$\Pi$ 的（一阶）正相依图，记为 $G^+_\Pi$，是有向无穷图，其顶点集是（一阶）原子集 $\{p(t_1, \cdots, t_n) \mid p \text{ 是 } \Pi \text{ 中出现的谓词符号，} t_i(1 \leqslant i \leqslant n) \text{ 是个体变元符号或是在 } \Pi \text{ 中出现的个体常元符号}\}$，记作 $V^+_\Pi$。对任意顶点 $A$ 和顶点 $B$，$\langle A,B\rangle$ 是 $G^+_\Pi$ 的一条弧当且仅当存在 $r \in \Pi$ 和置换 $\theta$，使得 $\{A\}=\text{Head}(r)\theta$ 且 $B=\text{Pos}(r)\theta$ 且 $G^+_\Pi$ 中没有多于一条同样的弧。

### 定义 5.3.4
令 $\Pi$ 是正规程序，$G^+_\Pi$ 是 $\Pi$ 的正相依图。称有穷非空顶点集 $L \subseteq V^+_\Pi$ 为 $\Pi$ 的一阶正环，当且仅当对任意 $A,B \in L$，有且仅有 $\Pi$ 的正相依图 $G^+_\Pi$ 中一条从 $A$ 到 $B$ 的路径，使得该路径中的所有顶点都属于 $L$。有时，也称正环中的原子为环原子。

### 例 5.3.2
令 $\Pi=\{p(x) \leftarrow p(x)\}$，则 $\text{Comp}(\Pi)=\{\forall x(p(x) \leftrightarrow p(x))\}=\{\top\}$。$\Pi$ 的正相依图 $G^+_\Pi$ 的顶点集是 $\{p(\xi) \mid \xi \text{ 是一变元符号}\}$，所有弧的集合是 $\{\langle p(\xi),p(\xi)\rangle \mid \xi \text{ 是一变元}\}$。

### 定义 5.3.5
令 $\Pi$ 是正规程序，$L$ 是 $\Pi$ 的一阶正环，$p(t) \in L$。原子 $p(t)$ 关于 $L$ 和 $\Pi$ 的外部支承，是如下公式：

$$\bigvee_{1 \leqslant i \leqslant n} \exists y_i \left( \overline{\text{Body}_i}\, \theta \wedge \bigwedge_{q(u) \in \text{Pos}(\theta) \land q(v) \in L} (u \neq v) \right) \tag{5-4}$$

记为 $ES(p(t),L,\Pi)$，其中，

- $p(x) \leftarrow \text{Body}_1, \cdots, p(x) \leftarrow \text{Body}_n$ 是 $\Pi$ 的标准化结果的规则头中出现谓词 $p$ 的所有规则；
- $\overline{\text{Body}_i}$ 是 $\text{Body}_i$ 中所有元素的合取式，且其中的 "not" 用 "$\neg$" 替换；
- $y_i$ 是规则 $p(x) \leftarrow \text{Body}_i$ 中的局部变量，通过变元重命名假设 $y_i \cap \text{var}(L) = \emptyset$，其中 $\text{var}(L)$ 表示 $L$ 中出现的所有变元的集合；
- $\theta$ 是置换 $\{t_1/x_1, \cdots, t_k/x_k\}$，其中 $x=(x_1, \cdots, x_k)$，$t=(t_1, \cdots, t_k)$；
- $u \neq v$ 表示 $\neg(u=v)$。

### 定义 5.3.6
令 $\Pi$ 是正规程序，$L$ 是关于 $\Pi$ 的一阶正环。$L$ 关于 $\Pi$ 的一阶正环公式是如下公式：

$$\forall x \left( \left( \bigvee_{p \in L} p \right) \rightarrow \left( \bigvee_{p \in L} ES(p, L, \Pi) \right) \right) \tag{5-5}$$

记为 $LF(L,\Pi)$，其中 $x$ 是 $L$ 出现的所有变元组成的向量。

### 例 5.3.2（续）
考虑正规程序 $\Pi$ 的一阶正环 $L=\{p(z)\}$，假设其中规则 $p(x) \leftarrow p(x)$ 的标准化为 $p(y) \leftarrow x=y \wedge p(x)$。$p(z)$ 关于 $L$ 的外部支承公式为

$$ES(p(z), L, \Pi) = \exists x\, (x=z \wedge x \neq z \wedge p(x))$$

故其环公式 $LF(L, \Pi) = \forall z\, \neg p(z)$。

### 例 5.3.3
考虑程序 $\Pi=\{p(x) \leftarrow p(y)\}$。易知，$L_k=\{p(x_1), \cdots, p(x_k)\}$ 是该程序的一阶正环，其中 $k$ 是大于或等于 1 的自然数。对每一 $p(x_i)$，$ES(p(x_i), L_k, \Pi)$ 逻辑等价于如下公式：

$$\exists y\, \left( p(y) \wedge \bigwedge_{1 \le j \le k} x_i \neq y \right)$$

$LF(L_k, \Pi)$ 逻辑等价于如下公式：

$$\forall x_1 \cdots \forall x_k\, \left( (p(x_1) \vee \cdots \vee p(x_k)) \to \exists y\, (p(y) \wedge (y \neq x_1) \wedge \cdots \wedge (y \neq x_k)) \right)$$

### 定理 5.3.1
令 $\Pi$ 是正规程序，$F$ 是关于 $\Pi$ 的外部关系的基事实集，$D$ 是 $\Pi$ 和 $F$ 中出现的所有常元集，$F(\Pi|_D)$ 是 $\Pi|_D$ 的所有一阶正环公式集。基事实集 $M$ 是 $(\Pi|_D) \cup F$ 的回答集当且仅当 $M$ 是 $\text{Comp}(\Pi \cup F) \cup LF(\Pi|_D)$ 的命题模型（在命题逻辑意义下，即基原子被视为命题逻辑的原子）。

该定理的证明比较烦琐，但是其思想比较直观，即用一个等价程序 $\Pi_{\text{eq}}$ 证明：

- $LF(\Pi_{\text{eq}})|_D = LF(\Pi)|_D$，且 $\Pi_{\text{eq}} \cup F$ 的回答集与 $\Pi \cup F$ 回答集一一对应；
- $(\text{Comp}(\Pi_{\text{eq}} \cup F) \cup LF(\Pi_{\text{eq}}))|_D$ 的模型与 $\Pi_{\text{eq}} \cup F$ 的回答集一一对应。

其中，eq 是一个不在 $\Pi$ 中出现的二元谓词，$\Pi_{\text{eq}}$ 是用 $\text{eq}(s,t)$ 替换其中的 $s=t$ 得到的。因为 $s \neq t$ 是 $\text{not}(s=t)$ 的缩写，所以 $s \neq t$ 用 $\text{not eq}(s, t)$ 替换。

注意：eq 不出现在 $\Pi_{\text{eq}}$ 的任何规则的头部，故 $\Pi_{\text{eq}}|_D \cup EQ|_D$ 与 $\Pi|_D \cup EQ|_D$ 有相同的回答集。于是有如下断言：

### 引理 5.3.1
令 $\Pi$ 是一正规逻辑程序，$D$ 是包含 $\text{Const}(\Pi)$ 的有穷常元集。$M \subseteq \text{GAtom}_{(\Pi|_D)}$ 是 $\Pi|_D$ 的回答集，当且仅当 $M \cup EQ|_D$ 是 $\Pi_{\text{eq}}|_D \cup EQ|_D$ 的回答集，其中，$\text{Const}(\Pi)$ 是出现在 $\Pi$ 中的所有常元的集合，$EQ|_D = \{\text{eq}(d,d) \mid d \in D\}$。

证明 因为 $\text{Head}(\Pi)$ 中不含有 eq 谓词符号，故 $\Pi_{\text{eq}}|_D \cup EQ|_D$ 与 $\Pi|_D \cup EQ|_D$ 等价（有相同的回答集）。

上述论证表明 $\Pi|_D$ 和 $\Pi_{\text{eq}} \cup EQ|_D$ 是等价的，但后者在没有失去 $\Pi$ 的任何环的意义下是比前者更为有效的使 $\Pi$ 基于 $D$ 的方式。例如，$\{p(x), p(y)\}$ 是程序 $\Pi=\{p(x) \leftarrow p(y),\ x \neq x\}$ 的一个一阶正环。但是对任意集合 $D$，$\Pi|_D = \emptyset$，故其没有一阶正环。然而，$\Pi_{\text{eq}}|_D$ 和 $\Pi$ 有完全相同的一阶正环。下面的命题表明这对有穷正规程序确实成立。

### 引理 5.3.2
令 $\Pi$ 是一正规逻辑程序，$D$ 是包含 $\text{Const}(\Pi)$ 的有穷常元集。若 $GL$ 是 $\Pi_{\text{eq}}|_D$ 的环，则 $\Pi$ 有一阶正环 $L$，使得 $GL=L\theta$，其中 $\theta$ 是变元集 $\text{var}(L)$ 到 $D$ 的变元指派。反之，若 $L$ 是 $\Pi$ 的一阶正环且 $\theta$ 是变元集 $\text{var}(L)$ 到 $D$ 的变元指派，则 $L\theta$ 是 $\Pi_{\text{eq}}|_D$ 的一阶正环。

证明 首先，若令 $A, B$ 是 $\Pi_{\text{eq}}|_D$ 中不涉及 eq 的两个原子，$d_1, \cdots, d_n$ 是在 $A, B$ 中出现但不在 $\text{Const}(\Pi)$ 中出现的常元，$A', B'$ 是用 $d_i$ 分别替换 $A, B$ 中出现的 $x_i$ 得到的原子。可以证明 $\langle A,B\rangle$ 是 $\Pi_{\text{eq}}|_D$ 的相依图中的弧，当且仅当 $\langle A', B'\rangle$ 是 $\Pi$ 的相依图中的弧。

$\langle A,B\rangle$ 是 $\Pi_{\text{eq}}|_D$ 的相依图中的弧，当且仅当 $A \leftarrow B, \text{Body} \in \Pi_{\text{eq}}|_D$；当且仅当存在 $\alpha \leftarrow \beta, \text{Body} \in \Pi$ 且有一个变元集到 $D$ 的变元指派 $\theta$，使得 $A=\alpha\theta$ 且 $B=\beta\theta$；当且仅当存在 $\alpha \leftarrow \beta, \text{Body} \in \Pi$ 且有一个置换 $\theta'$，使得 $A'=\alpha\theta'$ 且 $B'=\beta\theta'$，其中 $\theta'$ 是将 $\theta$ 中的 $d_i$ 替换为 $x_i$ 得到的；当且仅当 $\langle A', B'\rangle$ 是 $\Pi$ 的相依图中的弧。

其次，因为 $GL=\{A_1, \cdots, A_n\}$ 是 $\Pi_{\text{eq}}|_D$ 的一阶正环，则其相依图有一路径 $\langle A_1, \cdots, A_n, A_{n+1}\rangle (A_{n+1}=A_1)$。注意，对每一 $i: 1 \leqslant i \leqslant n$，$A_i$ 不涉及 eq 且 $\langle A_i, A_{i+1}\rangle$ 是 $\Pi_{\text{eq}}|_D$ 的相依图中的弧，故对 $1 \leqslant i \leqslant n$，$\langle A'_i, A'_{i+1}\rangle$ 是 $\Pi$ 的相依图中的弧。因此，$L=\{A'_1, \cdots, A'_n\}$ 是 $\Pi$ 的环且 $GL=L\theta$，$\theta=\theta_1 \circ \cdots \circ \theta_n$，其中 $\theta_i(i=1, \cdots, n)$ 是使得 $\langle A_i, A_{i+1}\rangle=\langle A'_i, A'_{i+1}\rangle$ 的置换。

同理可证，若 $L=\{A'_1, \cdots, A'_n\}$ 是 $\Pi$ 的环，$\theta$ 是变元集 $\text{var}(L)$ 到 $D$ 的变元指派，则 $L\theta$ 是 $\Pi_{\text{eq}}|_D$ 的环。只需注意，依假设 $\text{Const}(\Pi) \subseteq D$，故每一 $A'_i\theta(1 \leqslant i \leqslant n)$ 是 $\Pi$ 的相依图中的顶点。

下面的断言表明，定义 5.1.3 关于基例化正规逻辑程序的外部支承和环公式及程序的完备的定义是分别与定义 5.3.5 和定义 5.3.6 关于一阶正环的外部支承和一阶环公式及一阶完备的定义等价。

### 引理 5.3.3
令 $\Pi$ 是一正规逻辑程序，$L$ 是 $\Pi_{\text{eq}}$ 的一阶正环，$D$ 是满足 $\text{Const}(\Pi) \subseteq D$ 的有穷常元集，则对任意 $A=p(t) \in L$ 和任何从 $\text{var}(L)$ 到 $D$ 的变元指派 $\theta$，有

$$ES(A\theta, L\theta, \Pi_{\text{eq}}|_D) \models ES(A, L, \Pi_{\text{eq}})\theta|_D$$

证明 不失一般性，假设 $\Pi$ 只有一条以谓词 $p$ 为头的规则 $r:p(\mathbf{x}) \leftarrow \text{Body}$，其中 $\mathbf{x}$ 不包含 $L$ 中的变元，且 $L$ 中的变元都不在 $\Pi$ 中出现。显然，有

$$ES(A\theta, L\theta, \Pi_{\text{eq}}|_D) \models \bigvee_{\delta: \mathbf{x}=\theta d,\ \text{Body}_{\phi_0}(\theta L\theta=\varnothing)} \overline{\text{Body}_{eq}}\, \delta$$

其中，$\delta$ 是 $r$ 中出现的变元的集合到 $D$ 的变元指派。同时，因为

