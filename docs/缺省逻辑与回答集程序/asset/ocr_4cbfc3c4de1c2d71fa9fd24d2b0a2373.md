$Z=\{A_1, \cdots, A_k\} (k \geqslant 1)$ 的长度是 $k$。特别地，若 $Z$ 是单子，即 $Z=\{A\}$，则称 $Z$ 为伪正环，其长度为 1。

无环的正规程序没有正环；反之却不真。例如，程序 $\{A \leftarrow \text{not } A\}$ 没有正环，但不是无环的。根据定理 4.2.2*，任一正规程序 $\Pi$ 有唯一回答集 $\varnothing$，当且仅当对任一 $r \in \Pi$， $\text{Pos}(r) \neq \varnothing$。因此，通常只需考虑至少包含一条体非空的规则的程序。

# 定理 5.1.1 令 $\Pi$ 是正规程序，若 $\Pi$ 是紧凑的，则 $\Pi$ 没有正环。若 $\Pi$ 是有穷的，则 $\Pi$ 是紧凑的，当且仅当 $\Pi$ 没有正环。

证明 设 $\Pi$ 有正环 $Z=\{A_1, \cdots, A_k\} (k \geqslant 1)$，则有 $A_i \leftarrow \text{Pos}(r, \text{not Neg}(r) \in \Pi (1 \leqslant i \leqslant k)$，使得 $A_{i+1} \in \text{Pos}(r) (1 \leqslant i \leqslant k-1)$ 和 $A_1 \in \text{Pos}_k$。由于程序 $\Pi$ 的紧凑性，因此有层次映射 $\lambda$，使得 $\lambda(A_1) > \lambda(A_2) > \cdots > \lambda(A_k) > \lambda(A_1)$，矛盾。

对定理的第二部分，只需证 “当” 成立。设 $|\Pi| = m$，令

```
对 $n \geqslant 0, S_0 = \varnothing$
\( S_{n+1} = \{ \text{Head}(r) | r \in \Pi, \forall A' \in \text{Pos}(r) (A' \in \text{Head}(\Pi) \rightarrow A' \in \cup_{i \leqslant n} S_i) \} \)
\( S = \cup_{0 \leqslant n \leqslant m} S_n \)
```

显然，

```
对 $n \geqslant 0, S_1 = \{ A | A \leftarrow \text{Pos, not Neg} \in \Pi, \text{Pos} \cap \text{Head}(\Pi) = \varnothing \}$
\( S_n \subseteq S_{n+1} \text{ 且 } S \subseteq \text{Head}(\Pi) \)
```

由于 $\Pi$ 没有正环，故对任意 $n \geqslant 0, S_n$ 中不包含任何正环。若有 $A_1 \in \text{Head}(\Pi) \setminus S$，根据 $S$ 的定义，则对 $A_1 \leftarrow \text{Pos}_1, \text{not Neg}_1 \in \Pi$ 有相应的 $A_2 \in \text{Pos}_1 \cap (\text{Head}(\Pi) \setminus S)$。类似地，对 $A_2 \leftarrow \text{Pos}_2, \text{not Neg}_2 \in \Pi$ 有相应的 $A_3 \in \text{Pos}_2 \cap (\text{Head}(\Pi) \setminus S)$， $\cdots$，如此得到 $\text{Head}(\Pi) \setminus S$ 中序列 $<A_1, A_2, A_3, \cdots>$。因为 $\Pi$ 是有穷的，必有 $i, j \leqslant m$，使得 $A_i = A_j$ 且 $(A_i, \cdots, A_j)$ 是 $\Pi$ 的正环，这与 $\Pi$ 没有正环的假设矛盾。因此，$\text{Head}(\Pi) \setminus S = \varnothing$，从而 $S = \text{Head}(\Pi)$。

定义层次映射 $\lambda$，使得若 $A \in S_{n+1} \setminus S_n$，则 $\lambda(A) = n+1$；若 $A \in \text{Pos}(\Pi) \setminus \text{Head}(\Pi)$，则 $\lambda(A) = 0$。容易验证，$\lambda$ 是合式定义的，即对任意 $A \in \text{Head}(\Pi) \cup \text{Pos}(\Pi)$，有且仅有一个 $n \geqslant 0$，使得 $\lambda(A) = n$。

对任意 $r \in \Pi$，有最小的 $n \geqslant 0$，使得 $\text{Head}(r) \in S_{n+1}$，且 $\text{Pos}(r) \cap \text{Head}(\Pi) \subseteq \cup_{i \leqslant n} S_i$，依 $\lambda$ 的定义，$\lambda(\text{Head}(r)) > \max \{ \lambda(A') | A' \in \text{Pos}(r) \}$。因此，$\Pi$ 是紧凑的。

注意：若无穷的正规程序没有正环，它可能不是紧凑的。例如，程序 $\{ A_{n+1} \leftarrow A_n | n \geqslant 0 \}$ 没有正环，但不是紧凑的。即使定义 $\lambda$ 为 $\text{GAtom}$ 到序数的映射，仍有无正环的程序不是紧凑的，例如，$\{ A_{\omega+n+1} \leftarrow A_{\omega+n} | n \geqslant 0 \}$ 没有正环，但不是紧凑的。

# 例 5.1.1 程序 $\Pi = \{ A \leftarrow B, B \leftarrow A, A \leftarrow \text{not } C, C \leftarrow D, D \leftarrow C, C \leftarrow \text{not } A, E \leftarrow E \}$， $\Pi$ 有正环 $Z_1 = \{E\}, Z_2 = \{A, B\}$ 和 $Z_3 = \{C, D\}$。删去规则 $E \leftarrow E$ 得到程序 $\Pi' = \{ A \leftarrow B, B \leftarrow A$,