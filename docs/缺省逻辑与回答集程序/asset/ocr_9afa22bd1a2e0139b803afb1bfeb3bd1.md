易知 $F_{i+1} \subseteq F'_{i+1}$。

反之，若 $A:MB/C \in D, <A:\{r_1, \cdots, r_n\}> \in F'_i$ 且 $\{B,C\} \cup Form(F) \cup Supp(F)$ 协调，则依归纳假设 $<A:\{r_1, \cdots, r_n\}> \in F_i \subseteq F$。因此，$A \in Form(E)$。由定义 7.3.3, $A:MB/C \in D'$, 故 $<C:\{r_1, \cdots, r_n, B,C\}> \in F_{i+1}$。这表明，$F'_{i+1} \subseteq F_{i+1}$。因此 $F'_{i+1} = F_{i+1}$。
综上所述，$D'$ 是 $\Delta$ 的 CDL 扩张 $F$ 的生成缺省集。

若存在 $A:MB/C \in D \setminus D'$，使得

$Form(W) \cup Con(D') \vdash A$，且 $\{B\} \cup Form(W) \cup Supp(W) \cup Con(D')$ 协调

则依定理 7.3.5，有 $A \in Form(E)$。故 $A:MB/C \in D'$，矛盾。

“当”类似于“仅当”的证明，设 $E$ 是 $\Delta$ 的 CDL 扩张，$D' = GD(E, \Delta)$。依定理 7.2.1，$E = \cup_{0 \le i} E_i$，其中

对于 $i \ge 0, E_0 = W$  
$E_{i+1} = Th_S(E_i) \cup \{<C:\{r_1, \cdots, r_n, B,C\}> | A:MB/C \in D', <A:\{r_1, \cdots, r_n\}> \in E_i\}$

定义 $F = \cup_{0 \le i} F_i$，其中

对于 $i \ge 0, F_0 = W$  
$F_{i+1} = Th_S(F_i) \cup \{<C:\{r_1, \cdots, r_n, B\}> | A:MB/C \in D', <A:\{r_1, \cdots, r_n\}> \in F_i\}$

容易归纳地证明：$Form(F) = Form(E)$ 且 $Supp(F) = Supp(E) \cup Ccs(D')$。

定义 $F' = \cup_{0 \le i} F'_i$，其中

$F'_0 = W$  
对于 $i \ge 0, F'_{i+1} = Th_S(F'_i) \cup \{<C:\{r_1, \cdots, r_n, B\}> | A:MB/C \in D', <A:\{r_1, \cdots, r_n\}> \in F'_i$ 且 $\{B\} \cup Form(F) \cup Supp(F)$ 协调}

依定理的条件 (2)，类似地可证，对一切 $i \ge 0, F_i = F'_i$。故 $F$ 是 $\Delta$ 的 CADL 扩张，且 $D' = GD_{CA}(F, \Delta)$。

# 引理 7.3.1 令 $\Delta=(D,W)$ 是 ADT。

(1) $\Delta$ 不是良基的，当且仅当 $\Delta$ 有唯一 CDL 扩张 $E$，且 $Form(E) \cup Supp(E)$ 不协调。

(2) $\Delta$ 不是良基的，当且仅当 $\Delta$ 有唯一 CADL 扩张且 $Form(E) \cup Supp(E)$ 不协调。

证明 (1) 若 $\Delta$ 不是良基的，则易知 $Th_S(W)$ 是 $\Delta$ 的唯一 CDL 扩张，且 $Form(E) \cup Supp(E) \cup Th_S(W)$ 不协调。反之，若 $\Delta$ 有唯一 CDL 扩张，使得 $Form(E) \cup Supp(E)$ 协调，则由引理 7.2.1 知，$\Delta$ 不是良基的。

(2) 对于 CADL 扩张，类似于引理 7.2.1 的命题也为真。因此，类似于 (1) 容易证明 (2)。