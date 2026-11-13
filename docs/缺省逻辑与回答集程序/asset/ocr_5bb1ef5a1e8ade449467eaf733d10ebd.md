$r_k\rangle \in F_j\}$。

由 $D'$ 的联合相容性及 $\Lambda(D')=D'$，易知 $F_i(i \ge 0)$ 是合式定义的，因此 $E$ 是合式定义的。

施归纳法易证：

+   (1) $E=\text{Th}_S(W \cup \{<C: \{r_0, \cdots, r_k, B, C\} | A: MB/C \in E D', <A: \{r_0, \cdots, r_k\} > \in E\})$；
+   (2) $\text{Form}(E)=\text{Th}_S(W \cup \text{Con}(D'))$；
+   (3) $\text{Supp}(E)=\text{Supp}(W) \cup \text{Cons}(D') \cup \text{Con}(D')$；
+   (4) $\text{Form}(E) \cup \text{Supp}(E)$ 协调。

以 (1) 为例，记此式等号右边的式子为 $Q$。

首先，易知，对任意 $i \ge 0$，$F_i \subseteq Q$，从而 $E \subseteq Q$。

其次，由 $W \subseteq E$，对任意 $A: MB/C \in E D'$，若 $<A: \{r_0, \cdots, r_k\} > \in E$，则存在某个 $i \ge 0$，使得 $<A: \{r_0, \cdots, r_k\} > \in F_i$。因为 $D' = \Lambda(D')$，故有 $j \ge 0$，使得 $A: MB/C \in E D'_j(\Delta)$。令 $k=\max(i, j)$，根据 $F_i$ 的单调性，$<C: \{r_0, \cdots, r_k\} > \in F_{k+1}$，因此 $Q \subseteq E$。故 $Q=E$，即 (1) 为真。

为证 $E$ 是 $\Delta$ 的 CDL 扩张，根据定理 7.2.1，只需证 $\Theta(E, \Delta)=E$。注意，$\Theta(E, \Delta) = \cup_{0 \le i} E_i$，其中，

+   $E_0=W$；
+   对 $i \ge 0, E_{i+1}=\text{Th}_S(E_i) \cup \{<C: \{r_0, \cdots, r_k, B, C\} | A: MB/C \in E D, <A: \{r_0, \cdots, r_k\} > \in E_i$ 且 $\{B, C\} \cup \text{Form}(E) \cup \text{Supp}(E)$ 协调}。

首先施归纳法证，对任意 $i \ge 0$，$F_i \subseteq E$，从而 $E \subseteq \Theta(E, \Delta)$。

基始 $F_0 = E_0 = W$。

归纳 设 $F_i \subseteq E_i$，则 $\text{Th}_S(F_i) \subseteq \text{Th}_S(E_i)$。对任意 $A: MB/C \in E' D(\Delta)$ 且 $<A: \{r_0, \cdots, r_k\} > \in F_i$，依归纳假设，我们有 $<A: \{r, \cdots, r_k\} > \in E_i$。由 (2) ~ (4) 知，$\{B, C\} \cup \text{Form}(E) \cup \text{Supp}(E)$ 协调，故 $<C: \{r_0, \cdots, r_k, B, C\} > \in E_{i+1}$。

所以，$F_{i+1} \subseteq E_{i+1}$。

其次，施归纳法证：对一切 $i \ge 0$，$E_i \subseteq E$，从而 $\Theta(E, \Delta) \subseteq E$。

基始 $E_0 = W \subseteq E$。

归纳 设 $E_i \subseteq E$，则 $\text{Th}_S(E_i) \subseteq \text{Th}_S(E) = E$。对任意 $A: MB/C \in D$，若 $<A: \{r_0, \cdots, r_k\} > \in F_i$ 且 $\{B, C\} \cup \text{Form}(E) \cup \text{Supp}(E)$ 协调，则依归纳假设 $<A: \{r_0, \cdots, r_k\} > \in E$。因此，$<C: \{r_0, \cdots, r_k, B, C\} > \in E$ [依前面 (1) 式]，此时，$E_{i+1} \subseteq E$。

综上所述，引理获证。

由引理 7.2.4 与引理 7.2.5 可得 CDL 扩张的如下有穷特征。