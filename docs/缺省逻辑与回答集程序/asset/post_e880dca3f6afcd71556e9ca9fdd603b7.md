
例如, $\Delta=(D, W)$, 其中 $D=\{d_1,d_2,d_3,d_4\}, W=\{C\to G, A\land B\to E, E\lor G, G\to F\}$。

$d_1=E\lor F:M(A\land F)/A\land F$
$d_2=A:MB/B$
$d_3=A\land E:MC/C$
$d_4=M\neg E/\neg E$

易知 $\{d_3\}, \{d_2,d_1\}, \{d_1\}, \{\}$ 是 $G$ 关于 $\Delta$ 的一个缺省证明。同时, $\{d_4\}, \{\}$ 也是 $G$ 关于 $\Delta$ 的一个缺省证明。但 $\{d_3,d_4\}, \{d_1,d_2\}, \{\}$ 不是 $\neg E$ 关于 $\Delta$ 的缺省证明, 因为尽管它满足定义 3.5.1 的条件 (1) ~ (3), 但不满足条件 (4)。

### 定理 3.5.2 (可靠性)
设 $\Delta=(D, W)$ 是协调的弱自相容缺省理论, $A$ 是公式。若 $A$ 存在关于 $\Delta$ 的缺省证明 $D_0, \cdots, D_k$, 则 $\Delta$ 存在扩张 $E$, 使得 $A \in E$。

证明 令 $D'=\cup_{0 \le i \le k} D_i, \Delta'=(D',W)$. 由于 $D_0, \cdots, D_k$ 满足定义 3.5.1 中条件 (2), 易知 $(D')(\Delta)=D_{k+1}$, 故 $\Delta'(D',\Delta')=D'$. 又由于 $D_0, \cdots, D_k$ 满足条件 (4), 故 $D'$ 相容 (关于 $\Delta'$). 依定理 3.3.1, $\Delta'$ 存在唯一扩张 $E'=Th(W \cup Con(D'))$. 因为 $\Delta$ 是弱自相容的, 依半单调性知, 存在 $\Delta$ 的扩张 $E$, 使得 $E'\subseteq E$, 故 $A \in E$。

### 定理 3.5.3 (完备性)
设 $E$ 是协调的弱自相容缺省理论 $\Delta=(D,W)$ 的扩张, 且 $A \in E$, 则 $A$ 有一个关于 $\Delta$ 的缺省证明。

证明 令 $D'=GD(E,\Delta)$, 因为 $A \in E$, 所以 $W \cup Con(D') \vdash A$. 因为 $D'=\cup_{0 \le i} (D')( \Delta)$, 依一阶逻辑的紧性, 有 $j \ge 0$, 使得 $W \cup \cup_{0 \le i \le j} Con(D_i)(\Delta) \vdash A$. 令 $D_i=(D')_{f_i}(\Delta), k=j+1$, $D_k=\emptyset$. 由生成缺省集的特性易知, $D_0, \cdots, D_k$ 满足定义 3.5.1 中的条件 (1) ~ (4)。

### 注
可以要求 $D_0, \cdots, D_k$ 两两互不相交。为此, 只需注意 $\Delta(D^*)=\cup_{0 \le i} (D')_i(\Delta)=\cup_{0 \le i} (D^*)_i(\Delta)$, 其中

$D_0^*(\Delta)=\{A:MB_1, \cdots, MB_n/C \in D' | W \vdash A \}, i \ge 0$
$D_{i+1}^*(\Delta)=\{A:MB_1, \cdots, MB_n/C \in D' \land \cup_{0 \le j \le i} (D^*)_j(\Delta) | W \cup Con(D^*_{j'}(\Delta)) \vdash A \}$

易知, $\Delta$ 算子的以上两种定义是等价的。
由定理 3.5.2 和定理 3.5.3 可得定理 3.5.4。

### 定理 3.5.4
对任一公式 $A$, 协调的弱自相容缺省理论 $\Delta=(D,W)$ 有扩张 $E$, 使得 $A \in E$, 当且仅当 $A$ 有关于 $\Delta$ 的缺省证明。

类似地, 我们可以证明“向前链接构造”(FC)的算法对于计算弱自相容缺省理论的扩张是可靠且完备的, 其中, $<$ 是 $D$ 上的一个良序。

不失一般性, 假定 $D$ 中每个缺省是具单一检验的缺省, 即形如 $A:MB/C$ 的缺省。

### 定义 3.5.2 (向前链接构造)
给定一个有穷缺省理论 $\Delta=(D,W)$, 设 $\{d_n | 0 \le n \}$ 是 $D$ 中所有缺省在良序 $<$ 下的列举。令 \( E_0^< = \text{Th}(W) \), 对任意 \( n \geqslant 0 \), 令 \( m \) 是满足下述条件的最小自然数, \( d_m = A: MB/C \), \( A \in E_n^< \) 且 \( \neg B, C \notin E_n^< \).

如果存在这样的 \( m \), 则定义 \( E_{n+1}^< = \text{Th}(E_n^< \cup \{C\}) \); 否则, 令 \( E_{n+1}^< = E_n^< \), 定义 \( E^< = \cup \{E_n^< \mid 0 \leqslant n\} \).

下面给出关于弱自相容的缺省理论扩张的向前链接构造（FC）算法：

```
FUNCTION one-extension (D,W,<)
BEGIN
E0^< := Th(W) ; GD0^< := ∅;
REPEAT
new := ∅;
IF THERE EXISTS THE LEAST m SUCH THAT d_m^< ∈ {d_n^< | n ≤ |D| } \ GD_n^<
WHERE Pre (d_m^<) ∈ E_n^< AND ¬B, C ∉ E_n^< FOR ANY B ∈ Cons (d_m^<) AND C ∈ Con (d_m^<)
THEN
BEGIN
new := new ∪ {d_m^<}
E^< = Th (E^< ∪ Con (d_m^<) )
GD^< = GD^< ∪ {d_m^<}
END
UNTIL new = ∅;
RETURN (E^<)
END
```

### 定理 3.5.5（可靠性和完备性）
给定弱自相容缺省理论 \(\Delta=(D,W)\), 公式集 \( E \) 是 \(\Delta\) 的扩张, 当且仅当 \( E \) 是由上述算法得到的。

定理 3.5.5 表明，一个弱自相容缺省理论的每一扩张都可由 \( D \) 上的一个良序通过上述算法得到，反之亦然。

## 3.6 缺省逻辑的语义

缺省逻辑的模型论语义本质上是具有完全可达关系的 Kripke 模型的扩充，它将缺省视为关于不完全规范世界的一阶知识，从经典一阶理论的模型族中选择两个受限制的子族，从而给出缺省理论的模型论特征。不同之处在于，Kripke 模型的论域要求非空，以保证与模型相应的理论的协调性。类似于缺省理论扩张的有穷特征，我们引入下述语义概念以确立扩张的语义特征。

令 \(\Phi\) 是一阶语言 \(L\) 的所有赋值组成的类。给定任意一阶闭公式集 \(F\)，令 \(F\) 的全体模型的集合为 \(MOD(F)=\{\psi \in \Phi, |\psi \vDash F\}\)。若 \(F=\varnothing\)，则令 \(MOD(F)=\Phi\)；令 \(MOD(\perp)=\varnothing\)，\(MOD(\top)=\Phi\)。

如前所述，给定一个集合 \(S\)，\(P(S)\) 表示 \(S\) 的幂集。

### 定义 3.6.1
称对子 \((\Psi, \tilde{\Psi})\) 是语义框架，其中 \(\Phi \neq \varnothing, \Psi \subseteq \Phi, \tilde{\Psi} \in P(\Phi)\)。若语义框架 \((\Psi, \tilde{\Psi})\) 满足：对任意 \(\Omega \in \tilde{\Psi}\)，\(\Psi \cap \Omega \neq \varnothing\)，则称它是分布式相容的。对任意语义框架 \((\Psi, \tilde{\Psi})\) 和 \((\Omega, \tilde{\Omega})\)，\((\Psi, \tilde{\Psi}) \neq (\Omega, \tilde{\Omega})\)，当且仅当 \(\Psi \neq \Omega\) 或 \(\tilde{\Psi} \neq \tilde{\Omega}\)。

### 定义 3.6.2
令 \(d=A:MB_1, \cdots, MB_n/C\) 是一缺省，定义语义框架上的关系 \(\le_d\) 如下：对任意两个语义框架 \((\Psi_1, \tilde{\Psi}_1)\) 和 \((\Psi_2, \tilde{\Psi}_2)\)，\((\Psi_1, \tilde{\Psi}_1) \le_d (\Psi_2, \tilde{\Psi}_2)\)，当且仅当 (1) 对任意 \(\psi \in \Psi_1, \psi \vDash A\)； (2) \(\Psi_2 = \{\psi \in \Psi_1 | \psi \vDash C\}\)； (3) \(\tilde{\Psi}_2 = \tilde{\Psi}_1 \cup \cup_{i \le i \le n} \{MOD(\{B_i\})\}\)。显然，若 \((\Psi_1, \tilde{\Psi}_1) \le_d (\Psi_2, \tilde{\Psi}_2)\)，则 \(\Psi_2 \subseteq \Psi_1, \tilde{\Psi}_1 \subseteq \tilde{\Psi}_2\)。

注意: 若 \(A\) 或 \(C\) 是永假公式，则对任意 \((\Psi_1, \tilde{\Psi}_1)\) 和 \((\Psi_2, \tilde{\Psi}_2)\)，\((\Psi_1, \tilde{\Psi}_1) \le_d (\Psi_2, \tilde{\Psi}_2)\) 不成立。

拓广上述关系到任意缺省序列和缺省集，具体如下：

### 定义 3.6.3
令 \((\Psi_1, \tilde{\Psi}_1)\) 和 \((\Psi_2, \tilde{\Psi}_2)\) 是任意两个语义框架，\(<d>_{i \ge \alpha}\) 是缺省序列 (\(\alpha\) 是一序数)。归纳地定义关系 \(\le_{<d>, i \ge \alpha}\) 如下： \((\Psi_1, \tilde{\Psi}_1) \le_{<d>, i \ge \alpha} (\Psi_2, \tilde{\Psi}_2)\) 当且仅当有语义框架序列 \((\Omega_i, \tilde{\Omega}_i)_{i < \alpha}\)，使得 (1) \((\Omega_0, \tilde{\Omega}_0) = (\Psi_1, \tilde{\Psi}_1)\)； (2) 对后继序数 \(i\): \((\Omega_i, \tilde{\Omega}_i) \le_{d_{i+1}} (\Omega_{i-1}, \tilde{\Omega}_{i-1})\)； (3) 对极限序数 \(i\): \(\Omega_i = \cap_{j < i} \Omega_j, \tilde{\Omega}_i = \cup_{j < i} \tilde{\Omega}_j\)； (4) \((\Psi_2, \tilde{\Psi}_2) = (\Omega_\alpha, \tilde{\Omega}_\alpha)\)。

对空缺省序列 \(\Diamond\)，定义 \((\Psi_1, \tilde{\Psi}_1) \le_{\Diamond} (\Psi_2, \tilde{\Psi}_2)\)，当且仅当 \(\Psi_1 = \Psi_2\) 且 \(\tilde{\Psi}_1 = \tilde{\Psi}_2\)。对只由一个缺省 \(d\) 组成的序列 \(<d>\)，视 \((\Psi_1, \tilde{\Psi}_1) \le_{<d>} (\Psi_2, \tilde{\Psi}_2)\) 等同于 \((\Psi_1, \tilde{\Psi}_1) \le_d (\Psi_2, \tilde{\Psi}_2)\)。

### 定义 3.6.4
令 $D$ 是缺省集，定义关系 $\le_D$，如下： $(\Psi_1, \tilde{\Psi}_1) \le_D (\Psi_2, \tilde{\Psi}_2)$，当且仅当 $(\Psi_1, \tilde{\Psi}_1) = (\Psi_2, \tilde{\Psi}_2)$ 或存在 $D$ 中某些缺省构成的非空序列 $<d_i>_{i \ge \alpha}$，使得 $(\Psi_1, \tilde{\Psi}_1) \le_{<d_i>_{i \ge \alpha}} (\Psi_2, \tilde{\Psi}_2)$。 $(\Psi, \tilde{\Psi}) <_D (\Omega_\alpha, \tilde{\Omega}_\alpha)$，当且仅当 $(\Psi, \tilde{\Psi}) \le_D (\Omega_\alpha, \tilde{\Omega}_\alpha)$ 且 $(\Psi, \tilde{\Psi}) \neq (\Omega_\alpha, \tilde{\Omega}_\alpha)$。

下面给出 $\le_D$ 的一些性质，它们比较容易被证明。

### 引理 3.6.1
设 $D_1$ 和 $D_2$ 是缺省集 $D$ 的子集且 $D_1 \subseteq D_2$。对任意语义框架 $(\Psi_1, \tilde{\Psi}_1)$ 和 $(\Omega, \tilde{\Omega})$，若 $(\Psi, \tilde{\Psi}) \le_{D_1} (\Omega, \tilde{\Omega})$，则 $(\Psi, \tilde{\Psi}) \le_{D_2} (\Omega, \tilde{\Omega})$。

### 引理 3.6.2
令 $d$ 是缺省，$\le_d$ 是反对称且传递的。特别地，若 $(\Psi_1, \tilde{\Psi}_1) \le_d (\Psi_2, \tilde{\Psi}_2) \le_d (\Psi_3, \tilde{\Psi}_3)$，则 $(\Psi_2, \tilde{\Psi}_2) = (\Psi_3, \tilde{\Psi}_3)$。设 $D$ 是缺省集，关系 $\le_D$ 是自反、反对称和传递的，即 $\le_D$ 是偏序。

### 定义 3.6.5
令 $D$ 是缺省集，称语义框架 $(\Psi, \tilde{\Psi})$ 是 $\le_D$ -极大的，当且仅当不存在框架 $(\Omega, \tilde{\Omega})$，使得 $(\Psi, \tilde{\Psi}) <_D (\Omega, \tilde{\Omega})$，$(\Psi, \tilde{\Psi})$ 是极大分布相容的当且仅当 $(\Psi, \tilde{\Psi})$ 是分布相容的且不存在分布相容的语义框架 $(\Omega, \tilde{\Omega})$，使得 $(\Psi, \tilde{\Psi}) <_D (\Omega, \tilde{\Omega})$。

### 定义 3.6.6
缺省集 $D$ 是协调的当且仅当 $\text{Pre}(D) \cup \text{Con}(D)$ 是协调的。

### 定义 3.6.7
语义框架 $(\Psi, \tilde{\Psi})$ 是协调缺省集 $D$ 的模型，记作 $(\Psi, \tilde{\Psi}) \vDash D$，当且仅当 $\Psi=\text{MOD}(\text{Pre}(D) \cup \text{Con}(D))$ 且 $\tilde{\Psi}=\{\text{MOD}(B)|B \in \text{Ccs}(D)\}$，其中，$\text{Ccs}(D) \neq \emptyset$；若 $\text{Ccs}(D)=\emptyset$，则 $\tilde{\Psi}=\{\Phi\}$。

语义框架 $(\Psi, \tilde{\Psi})$ 是缺省序列 $<d_i>_{i<\alpha}$ 的模型当且仅当 $(\Psi, \tilde{\Psi})$ 是由缺省序列 $<d_i>_{i<\alpha}$ 的所有成员构成的缺省集的模型。

易见，不协调的缺省集 $D$ 没有模型。

根据定义 3.6.3 和定义 3.6.7，下述结果是显然的。

### 推论 3.6.1
若 $(\Phi, \{\Phi\}) \leqslant_{<d_i>_{i<\alpha}} (\Psi, \tilde{\Psi})$，则 $(\Psi, \tilde{\Psi})$ 是缺省序列 $<d_i>_{i<\alpha}$ 的模型。

以下约定，对缺省理论 $\Delta=(D,W)$，$W$ 协调，$T^*=\{C:C \in W\}$。

### 引理 3.6.3
对任意缺省集 $D$ 和任一 $D' \subseteq D$，$\Lambda(D', \Delta)=D'$，当且仅当 $D' \cup T^*$ 有模型 $(\Psi, \tilde{\Psi})$，使得 $(\Phi, \{\Phi\}) \leqslant_{<d_i>_{i<\alpha}} (\Psi, \tilde{\Psi})$，其中 $<d_i>_{i<\alpha}$ 是 $D' \cup T^*$ 中所有缺省构成的系列。

**证明** 不失一般性，设 $D' \neq \emptyset$。事实上，若 $D'=\emptyset$，则由定义 3.6.4 和定义 3.6.7 可知引理成立。

证明条件“仅当”用 $D' \cup T^*$ 的良序 $d_1, \cdots, d_k$ 定义框架 $(\Psi_i, \tilde{\Psi}_i)$ 和缺省序列 $<\delta_i>_{i<\alpha}$ 如下：

- (1) $(\Psi_0, \tilde{\Psi}_0) = (\Phi, \{\Phi\})$。

- (2) 对后继序数 $i$，若有 $d_k=A:MB_1, \cdots, MB_n/C \in (D' \cup T^*) \backslash \{\delta_j | j \leqslant i-1\}$，使得对任一 $\gamma \in \Psi_{i-1}$，$\gamma \not\vdash A$，则 $\Psi_i = \{\gamma \in \Psi_{i-1} | \gamma \vdash C\}$，$\tilde{\Psi}_i = \tilde{\Psi}_{i-1} \cup \cup_{j \leqslant n} \text{MOD}(\{B_j\})$。

定义 $\delta_{i-1}$ 是上述 $d_k$ 中下标中最小的一个 $d_m$，即

$m=\min\{k|d_k \in (D' \cup T^*) \backslash \{\delta_j | j \leqslant i-1 \text{ 且 } \Psi_{i-1} \vdash A\}\}$。
