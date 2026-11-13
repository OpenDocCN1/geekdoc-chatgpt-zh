例 4.2.2 正规程序\(\Pi=\{A\leftarrow\text{not }B,\ B\leftarrow\text{not }A\}\)不是相容的而是弱自相容的，它有两个回答集\(\{A\}\)和\(\{B\}\)。

不失一般性，我们考虑由可数条规则组成的弱自相容正规程序的回答集算法。

定义 4.2.5 (向前链接算法) 令\(\Pi\)是弱自相容正规程序，\(<\)是自然数集上通常的大小关系，\(\Pi^<=\{r_1^{\cdot},\cdots,r_n^{\cdot},\cdots\}\)。令

+   (1) \(S^{\cdot}_0=\emptyset\)；

+   (2) 对\(i\geqslant 0\)，若有满足下述条件的最小的 \(k\)：\(\text{Pos}(r^{\cdot}_k)\subseteq S^{\cdot}_i\)且\(S^{\cdot}_i\cap\text{Neg}(r^{\cdot}_k)=\emptyset\)，则 \(S^{\cdot}_{i+1}=S^{\cdot}_i\cup\text{Head}(r^{\cdot}_k)\)；否则，\(S^{\cdot}_{i+1}=S^{\cdot}_i\)。

其中，令 \(S^{\cdot}=\cup\{S^{\cdot}_i|i\geqslant 0\}\)。

定义 4.2.5 (向前链接算法的可靠性和完备性) 令 \(S\) 是基原子集，\(\Pi\)是弱自相容正规程序。\(S\) 是\(\Pi\)的回答集，当且仅当 \(S\) 是由向前链接算法得到的。

值得指出的是，一方面，定义 4.1.1 和定义 4.1.2 通过引入部分赋值和归约将稳定模型概念拓广到回答集程序，从而得到回答集的概念（定义 4.1.3）；另一方面，根据定理 4.2.1，正规回答集程序是一类特殊的缺省逻辑，依缺省逻辑语义框架的可靠性与完备性定理 3.6.2 和定理 3.6.3，其回答集可以用相应的语义框架描述。这会产生如下问题：在这两种观点下正规程序的回答集概念是一致的吗？更确切地说，对任一正规程序\(\Pi\)和与之相应的缺省理论\(\Delta=\langle D_{\Pi},\emptyset \rangle\)，其中

\(D_{\Pi}=\{B_1\wedge\cdots\wedge B_m:M-C_1,\cdots,M-C_n/A|A\leftarrow B_1,\cdots,B_m,\text{not }C_1,\cdots,\text{not }C_n\in\Pi\}\)

下述两个结论是否等价：

结论 I：基原子集 \(S\) 是\(\Pi\)的一个回答集，当且仅当 \(S\) 是\(\Pi^S\)的稳定模型（定义 4.1.3）；

结论 II：基原子集 \(S\) 是\(\Pi\)的一个回答集，当且仅当有 \(D'\subseteq D_{\Pi}\) 和语义框架 \(\langle \Phi,\tilde{\Psi} \rangle\) 满足：

+   (1) \((\Phi,\{\Phi_i\})\leqslant_{(d_i)}(\Phi,\tilde{\Psi})\) 且\((d_i)\)恰好由 \(D'\) 中所有元素构成；

+   (2) \((\Phi,\tilde{\Psi})\) 是分布相容的；

+   (3) 对任一 \(d\in D_{\Pi}\backslash D'\)， \((\Phi,\tilde{\Psi})\) 关于 \(d\) 是稳定的。其中

\(D'=\{B_1\wedge\cdots\wedge B_m:M-C_1,\cdots,M-C_n/A|B_1,\cdots,B_m\subseteq S,\{C_1,\cdots,C_n\}\cap S=\emptyset\)
\(A\leftarrow B_1,\cdots,B_m,\text{not }C_1,\cdots,\text{not }C_n\in\Pi\}\subseteq D_{\Pi}\)
\(\tilde{\Psi}=\{\text{MOD}(B)|B\in\text{Ccs}(D')\}\cup\{\Phi\}\)

依定理 3.6.2 和定理 3.6.3，因为 \(\text{MOD}(\emptyset)=\Phi\)，故 \(\Psi=\Phi\)。

如果结论 I 和结论 II 是等价的，则依据结论 I 和结论 II 分别给出的正规程序的回答集概念是等价的。在此意义下，定义 4.1.3 是合适的。