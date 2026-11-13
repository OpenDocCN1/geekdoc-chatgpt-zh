条极大非封闭 $\leqslant$ 链的顶点数是有穷的，故 $\lambda(A)$ 可由 $G_\Pi$ 中的一条极大非封闭 $\leqslant$ 链唯一地确定。因此，上述定义是合适的。

从上述论证易知，条件“对任意原子 $B$，如果 $B \in \Pi_A^+ \cap \Pi_A^-$，则 $\lambda(B) < \lambda(A)$”自然成立，从而 $\Pi$ 是序协调的。

考虑程序 $\Pi = \{ p(x) \leftarrow \text{not } q(x),\ q(s(x)) \leftarrow \text{not } q(x)\}, x$ 是满足 $0 \le x < n$ 的自然数，$s$ 是后继函数。

\(\Pi^+_{p(2k)}=\{p(2k),q(2k-1),q(2k-3),\cdots,q(1)\}\)
\(\Pi^-_{p(2k)}=\{q(2k),q(2k-2),\cdots,q(0)\}\)
\(\Pi^+_{p(2k+1)}=\{p(2k+1),q(2k),q(2k-2),\cdots,q(0)\}\)
\(\Pi^-_{p(2k+1)}=\{q(2k+1),q(2k-1),\cdots,q(1)\}\)
\(\Pi^+_{q(2k)}=\{q(2k),q(2k-2),\cdots,q(0)\}\)
\(\Pi^-_{q(2k)}=\{q(2k-1),q(2k-3),\cdots,q(1)\}\)

其中，$0 \le k \le [n/2]$（即 $n/2$ 的整数部分），相依图所有极大链依下述关系确定：

对 $0 \le x < n,\ p(0) \leqslant q(0)$
\(p(x) \leqslant q(x) \leqslant q(x-1) \leqslant \cdots \leqslant q(1) \leqslant q(0)\)

定义层次映射 $\lambda$ 为 $\lambda(p(x))=x+1,\ \lambda(q(x))=x$，则该程序是序协调的。

注意：如果 $x$ 是任意自然数，则该程序不是序协调的。

# 定理 4.3.2 序协调的正规程序有回答集。

证明 可以证明：序协调的正规程序 $\Pi$ 的任一极大强相容子集是 $\Pi$ 的一个回答集的生成规则集。

设 $\Pi^*$ 是 $\Pi$ 的任一极大强相容子集，$S=\text{Head}(\Pi^*)$，若有 $r \in \Pi \backslash \Pi^*$，使得 $\text{Pos}(r) \subseteq S$ 且 $\Pi^* \cup \{r\}$ 是相容的，则可得 $\text{Head}(r) \in S$ 的结论。设若不然，令 $A=\text{Head}(r)$，则 $A \notin S$。因为 $\text{Pos}(\Pi^*) \subseteq S$ 且 $\Pi^* \cup \{r\}$ 是相容的，故 $\text{Neg}(\Pi^*) \cap S = \emptyset$。于是，$\text{Neg}(r) \subseteq \Pi_A^-$，$\text{Pos}(r) \subseteq \Pi_A^+$。依 $A \notin S$ 和 $\Pi^*$ 的极大强相容性，则 $A \in \text{Neg}(r)$，故 $A \in \Pi_A^+ \cap \Pi_A^-$，与 $\Pi$ 的序协调性矛盾。因此，对任意 $r \in \Pi \backslash \Pi^*$，必有 $\text{Pos}(r) \nsubseteq S$ 或 $\Pi^* \cup \{r\}$ 是不相容的，依定理 4.2.2*，$S$ 是 $\Pi$ 的回答集且 $\text{GR}(S, \Pi)$ 是 $S$ 的生成规则集。

因为局部分层的正规程序是序协调的，由定理 4.3.2 可以加强定理 4.3.1，如下：

# 定理 4.3.1* 局部分层的正规程序有唯一回答集。

定义 4.3.3 设 $\Pi$ 是正规程序，若存在 $\Pi$ 的一个层次映射 $\lambda$，使得对每一规则 $r \in \Pi$ 和每一原子 $A \in \text{Pos}(r) \cup \text{Neg}(r),\ \lambda(A) < \lambda(\text{Head}(r))$ 成立，则称 $\Pi$ 是无环程序。

从相依图的角度看，无环正规程序 $\Pi$ 的相依图 $G_\Pi$ 中没有任何由正边与负边