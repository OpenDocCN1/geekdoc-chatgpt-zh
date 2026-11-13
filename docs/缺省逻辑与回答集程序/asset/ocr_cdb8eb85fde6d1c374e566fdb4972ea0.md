$B_\alpha=\{A \in B_{|\Pi|} | \lambda(A)=\alpha\}$  
\(\Pi^I_\alpha=\{r \in \Pi | \lambda(\text{Head}(r))=\alpha\}\)

# **定理 4.3.1** 局部分层的正规程序至多有一个回答集。

**证明** 设 \(\Pi\) 是局部分层的正规程序，\(\lambda\) 是相应的层次映射。不失一般性，对某一序数 \(\mu\)，假设 \(\text{ran}(\lambda)=\{ \alpha | \alpha < \mu \}\)。若 \(\Pi\) 有回答集，令 \(S\) 是 \(\Pi\) 的任一回答集。依定义 4.1.3 和定理 2.3.5，\(S\) 是 \(\Pi^S\) 的回答集，当且仅当 \(S\) 是 \(\Pi^S\) 的最小 Herbrand 模型。对任一序数 \(\alpha < \mu\)，令

\(\Pi^S_{<\alpha}=\{r \in \Pi | \lambda(\text{Head}(r))<\alpha\} \quad \text{（注意，若 } \alpha=0 \text{，则 } \Pi^S_{<\alpha}=\emptyset \text{）}\)

记 \(\Pi^S_{<\alpha}\) 的最小 Herbrand 模型为 \(H^S_\alpha\)。因为 \(\Pi\) 是局部分层的，容易验证：\(S = \cup_{\alpha < \mu} H^S_\alpha\)。为证明 \(S\) 是 \(\Pi\) 的唯一回答集，首先证明下述命题：

(P1) 设 \(A\) 是满足 \(\lambda(A)<\alpha\) 的基原子，则 \(A \in H^S_\alpha\) 当且仅当 \(A \in S\)。

**证明** “当仅” 因为 \(\Pi^S_{<\alpha} \subseteq \Pi^S\)，故 \(\Pi^S_{<\alpha} \subseteq S\)。因此，对满足 \(\lambda(A)<\alpha\) 的基原子 \(A\)，若 \(A \in H^S_\alpha\)，则 \(A \in S\)。

“当” 设有基原子 \(A\)，使得 \(\lambda(A)<\alpha\) 且 \(A \notin H^S_\alpha\)。令 \(M = H^S_\alpha \cup \{B \in B_{|\Pi|} | \lambda(B) \geqslant \alpha\}\)，则 \(A \notin M\)。因为对每一原子 \(B \in \text{Head}(\Pi^S \setminus \Pi^S_{<\alpha})\)，\(\lambda(B) \geqslant \alpha\)，故 \(M\) 是 \(\Pi^S \setminus \Pi^S_{<\alpha}\) 的模型。依 \(\Pi\) 的局部分层性，对每一规则 \(r \in \Pi^S_{<\alpha}\)，\(\max\{\lambda(B) | B \in \text{Pos}(r)\} \leqslant \lambda(\text{Head}(r)) < \alpha\)。于是，若 \(\text{Pos}(r) \subseteq M\)，因为对每一原子 \(C \in \text{Head}(M \cap H^S_\alpha)\) 有 \(\lambda(C) \geqslant \alpha\)，故 \(\text{Pos}(r) \subseteq H_\alpha\)。由于 \(H^S_\alpha\) 是 \(\Pi^S_{<\alpha}\) 的 Herbrand 模型可导出 \(M\) 是 \(\Pi^S\) 的 Herbrand 模型，再由 \(S\) 是 \(\Pi^S\) 的最小 Herbrand 模型即可导出 \(S \subseteq M\)。因此，由 \(A \notin M\) 导出 \(A \notin S\)。

综上所述，命题（P1）获证。

其次递归纳法证明：若 \(\Pi\) 有回答集 \(S\) 和 \(U\)，则 \(S=U\)。考虑一序数 \(\alpha < \mu\)，设对每一 \(\beta < \alpha\)，\(\Pi^S_\beta = \Pi^U_\beta\)。特别地，对序数 0，依 \(\Pi\) 的局部分层性，显然有 \(\Pi^S_0 = \Pi^U_0 = \{r \in \Pi | \lambda(A)=0, \text{Pos}(r)=\emptyset\} \neq \emptyset\)。因此，\(\Pi^S_{<\alpha} = \Pi^U_{<\alpha}\) 且 \(H^S_\alpha = H^U_\alpha\)。由 (P1) 可推导出 \(S \cap (B_\Pi)_{<\beta} = U \cap (B_\Pi)_{<\beta}\) (4-8)

最后，对任一 \(r \in \Pi_\beta\)，依 \(\Pi\) 的局部分层性，若 \(C \in \text{Neg}(r)\)，则 \(\lambda(C) < \lambda(\text{Head}(r)) = \beta\)。由此导出 \(C \in S\) 当且仅当 \(C \in U\)。因此，\(\Pi^S_\alpha = \Pi^U_\alpha\)。于是根据超穷归纳原理，\(\Pi^S = \Pi^U\)。因为 \(S\) 和 \(U\) 分别是 \(\Pi^S\) 和 \(\Pi^U\) 的最小 Herbrand 模型，故 \(S=U\)。

综上所述，如果局部分层的正规程序有回答集 \(S\)，则 \(S\) 是 \(\Pi\) 的唯一回答集。

**注意**：有唯一回答集的正规程序不一定是局部分层的。例如，程序 \(\{A \leftarrow, B \leftarrow, B \leftarrow \text{not } B\}\) 有唯一回答集 \(\{A, B\}\)，但它不是局部分层的。

为保证回答集存在，下面引入协调性概念。

设 \(\Pi\) 是正规程序，对任意原子 \(A\)，令 \(\Pi^+_A\) 和 \(\Pi^-_A\) 分别是满足下述条件的最小原