```
FOR each \(\Pi' \subseteq \Pi\) DO
    IF GR(\(\Pi, \Pi', S\)) and L \(\notin\) S then RETURN (false)
RETURN (true)
END
```

# 5.2.3 嵌套程序的紧凑性

由推论 5.2.4，若 \(S\) 是 \(\Pi\) 的一个回答集，则 \(S\) 封闭于 \(\Pi\) 且是 \(\Pi\) 支承的。然而，其逆不真。典型的例子是，程序 \(\Pi = \{A \leftarrow A\}\) 有唯一回答集 \(\emptyset\)，\(\{A\}\) 是 \(\Pi\) 支承且封闭于 \(\Pi\) 的，但它不是 \(\Pi\) 的回答集。排除这样的程序的一种方式是引入基于层次映射的紧凑性作为一个语法条件。

一个层次映射 \(\lambda\) 是从文字集到自然数集（任意拓广到序数组成的类）部分分函数。

定义 5.2.9 对嵌套程序 \(\Pi\) 和文字集 \(S\), \(\Pi\) 在 \(S\) 上是紧凑的，当且仅当有层次映射 \(\lambda\) 满足：对任意 \(C \leftarrow B, F \in \Pi\)，有

+   - 若 \(L \in \mathbf{G}Lit_C \cap S\)，则 \(\lambda(L) > \max \{ \lambda(K) | K \in B \cap S \}\)；
+   - 若 \(\Pi\) 在 \(\mathbf{G}Lit_\Pi\) 上是紧凑的，且对每一 \(C \leftarrow B, F \in \Pi\) 和任意 \(L \in \mathbf{G}Lit_C\), \(\lambda(L) > \max \{ \lambda(K) | K \in B \}\),

则称 \(\Pi\) 是紧凑的。

通常，对任一文字集 \(B\)，记 \(\max(\lambda(B)) = \max \{ \lambda(K) | K \in B \}\)。

引理 5.2.8 令 \(\Pi\) 是嵌套程序，\(S\) 是协调的文字集。若 \(\Pi((S), S) \in \mathcal{A}(\Pi)\)，则存在 \(\Pi' \subseteq \Pi(S)\)，使得 \((\Pi', S) \in \mathcal{A}(\Pi')\) 且 \(\Pi'\) 是在 \(S\) 上紧凑的。

证明 不失一般性，设 SM(\(\{ C | C \leftarrow B, F \in \Pi | B = \emptyset \}\)) \(\neq \emptyset\)。否则，\(\mathcal{A}(\Pi) = \{ \emptyset \}\)，令 \(\Pi' = \emptyset\) 即可。设 \(S = \cup_{0 \le n} S_n\), \(\Pi(S) = \cup_{0 \le n} \Pi(S_n)\)，其中，

```
\(S_0 \in SM(\{ C | C \leftarrow B, F \in \Pi | B = \emptyset \})\)
\(S_n \in SM(\{ C \leftarrow B, F \in \Pi | B \subseteq S_{n-1} \})\)
\(\Pi(S_n) = \{ C \leftarrow B \in \Pi | B \subseteq S_{n-1} \}\)
```

定义 \(\Pi'\) 如下：

```
对 \(n > 0\), \(\Pi'_0 = \{ C \leftarrow B, F \in \Pi(S_0) | B = \emptyset \}\)
\(\Pi'_n = \{ C \leftarrow B, F \in \Pi(S_n) | \mathbf{G}Lit_C \cap S_{n-1} = \emptyset \}\)
\(\Pi' = \cup_{0 \le n} \Pi'_n\)
```

显然, \(\Pi'_n \subseteq \Pi(S_n)\), \(\Pi' \subseteq \Pi(S)\)。

施归纳法容易证明：对 \(n \ge 0\), \(S_n \in SM(\{ C | C \leftarrow B, F \in \Pi'_n \})\)，因此 \((\Pi', S) \in \mathcal{A}(\Pi')\)。

基始 由 \(\Pi'_0 = \Pi(S_0)\)，显然，\(S_0 \in SM(\{ C | C \leftarrow B, F \in \Pi'_0 \})\)。