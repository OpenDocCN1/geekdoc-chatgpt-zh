下面给出 $\le_D$ 的一些性质，它们比较容易被证明。

**引理 3.6.1** 设 $D_1$ 和 $D_2$ 是缺省集 $D$ 的子集且 $D_1 \subseteq D_2$。对任意语义框架 $(\Psi_1, \tilde{\Psi}_1)$ 和 $(\Omega, \tilde{\Omega})$，若 $(\Psi, \tilde{\Psi}) \le_{d_1} (\Omega, \tilde{\Omega})$，则 $(\Psi, \tilde{\Psi}) \le_{d_2} (\Omega, \tilde{\Omega})$。

**引理 3.6.2** 令 $d$ 是缺省，$\le_d$ 是反对称且传递的。特别地，若 $(\Psi_1, \tilde{\Psi}_1) \le_d (\Psi_2, \tilde{\Psi}_2) \le_d (\Psi_3, \tilde{\Psi}_3)$，则 $(\Psi_2, \tilde{\Psi}_2) = (\Psi_3, \tilde{\Psi}_3)$。设 $D$ 是缺省集，关系 $\le_D$ 是自反、反对称和传递的，即 $\le_D$ 是偏序。

**定义 3.6.5** 令 $D$ 是缺省集，称语义框架 $(\Psi, \tilde{\Psi})$ 是 $\le_D$ -极大的，当且仅当不存在框架 $(\Omega, \tilde{\Omega})$，使得 $(\Psi, \tilde{\Psi}) <_D (\Omega, \tilde{\Omega})$，$(\Psi, \tilde{\Psi})$ 是极大分布相容的当且仅当 $(\Psi, \tilde{\Psi})$ 是分布相容的且不存在分布相容的语义框架 $(\Omega, \tilde{\Omega})$，使得 $(\Psi, \tilde{\Psi}) <_D (\Omega, \tilde{\Omega})$。

**定义 3.6.6** 缺省集 $D$ 是协调的当且仅当 $\text{Pre}(D) \cup \text{Con}(D)$ 是协调的。

**定义 3.6.7** 语义框架 $(\Psi, \tilde{\Psi})$ 是协调缺省集 $D$ 的模型，记作 $(\Psi, \tilde{\Psi}) \vDash D$，当且仅当 $\Psi=\text{MOD}(\text{Pre}(D) \cup \text{Con}(D))$ 且 $\tilde{\Psi}=\{\text{MOD}(B)|B \in \text{Ccs}(D)\}$，其中，$\text{Ccs}(D) \neq \emptyset$；若 $\text{Ccs}(D)=\emptyset$，则 $\tilde{\Psi}=\{\Phi\}$。

语义框架 $(\Psi, \tilde{\Psi})$ 是缺省序列 $<d_i>_{i<\alpha}$ 的模型当且仅当 $(\Psi, \tilde{\Psi})$ 是由缺省序列 $<d_i>_{i<\alpha}$ 的所有成员构成的缺省集的模型。

易见，不协调的缺省集 $D$ 没有模型。

根据定义 3.6.3 和定义 3.6.7，下述结果是显然的。

**推论 3.6.1** 若 $(\Phi, \{\Phi\}) \leqslant_{<d_i>_{i<\alpha}} (\Psi, \tilde{\Psi})$，则 $(\Psi, \tilde{\Psi})$ 是缺省序列 $<d_i>_{i<\alpha}$ 的模型。

以下约定，对缺省理论 $\Delta=(D,W)$，$W$ 协调，$T^*=\{t:C \in C \in W\}$。

**引理 3.6.3** 对任意缺省集 $D$ 和任一 $D' \subseteq D$，$\Lambda(D', \Delta)=D'$，当且仅当 $D' \cup T^*$ 有模型 $(\Psi, \tilde{\Psi})$，使得 $(\Phi, \{\Phi\}) \leqslant_{<d_i>_{i<\alpha}} (\Psi, \tilde{\Psi})$，其中 $<d_i>_{i<\alpha}$ 是 $D' \cup T^*$ 中所有缺省构成的系列。

**证明** 不失一般性，设 $D' \neq \emptyset$。事实上，若 $D'=\emptyset$，则由定义 3.6.4 和定义 3.6.7 可知引理成立。

证明条件“仅当”用 $D' \cup T^*$ 的良序 $d_1, \cdots, d_k$ 定义框架 $(\Psi_a, \tilde{\Psi}_a)$ 和缺省序列 $<\delta_i>_{i<\alpha}$ 如下：

+   (1) $(\Psi_0, \tilde{\Psi}_0) = (\Phi, \{\Phi\})$。

+   (2) 对后继序数 $i$，若有 $d_k=A:MB_1, \cdots, MB_n/C \in (D' \cup T^*) \backslash \{\delta_j | j \leqslant i-1\}$，使得对任一 $\gamma \in \Psi_{i-1}$，$\gamma \not\vdash A$，则 $\Psi_i = \{\gamma \in \Psi_{i-1} | \gamma \vdash C\}$，$\tilde{\Psi}_i = \tilde{\Psi}_{i-1} \cup \cup_{j \leqslant n} \text{MOD}(\{B_j\})$。

定义 $\delta_{i-1}$ 是上述 $d_k$ 中下标中最小的一个 $d_m$，即

$m=\min\{k|d_k \in (D' \cup T^*) \backslash \{\delta_j | j \leqslant i-1 \text{ 且 } \Psi_{i-1} \vdash A\}\}$。