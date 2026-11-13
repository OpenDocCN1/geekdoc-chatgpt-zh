引理 3.6.5 可以等价地陈述如下：

# 引理 3.6.6 框架 \((\Psi, \tilde{\Psi})\) 是 \(\le_D\) 极大分布相容的且 \((\text{MOD}(W), \{\Phi_i\}) \le_D (\Psi, \tilde{\Psi})\)，当且仅当有 \(D\) 的极大强相容子集 \(D'\)，使得 \((\text{MOD}(W), \{\Phi_i\}) \le_{<d_i>_{i<\alpha}} (\Psi, \tilde{\Psi})\)，其中，序列 \(<d_i>_{i<\alpha}\) 由 \(D'\) 的所有元素构成。

证明 以“仅当”为例，设 \(<d_i>_{i<\alpha}\) 是 \(D\) 中缺省构成的极大序列，使得 \((\Psi, \tilde{\Psi})\) 是分布相容的且 \((\text{MOD}(W), \{\Phi_i\}) \le_{<d_i>_{i<\alpha}} (\Psi, \tilde{\Psi})\)，即不存在 \(d \in D \\ D'\)，使得 \((\Psi, \tilde{\Psi})\) 是分布相容的，且 \((\text{MOD}(W), \{\Phi_i\}) \le_{<d_i>_{i<\alpha}+1} (\Psi, \tilde{\Psi})\)，其中，\(D'\) 由 \(<d_i>_{i<\alpha}\) 中的所有元素组成，\(<d'_i>_{i<\alpha+1} = <d_0 \cdots d_i \cdots d'>\)，\(i < \alpha\)。

显然，\((\Psi, \tilde{\Psi})\) 是 \(\le_D\)-极大的。由引理 3.6.5，\(\Lambda(D', \Delta) = D'\) 且 \(D'\) 相容，从而 \(D'\) 是强相容的。

若存在 \(d=A:MB_1, \cdots, MB_n/C \in D\)，使得 \(D' \cup \{d\}\) 是相容的，则 \(W \cup \text{Con}(D') = A\)。

因此

+   (1) 对任一 \(\psi \in \Psi\)，\(\psi \vDash A\);
+   (2) \((\Psi, \tilde{\Psi}) \le_d \{ \psi \in \Psi | \psi \vDash C \}\)，\(\tilde{\Psi} \cup \{ \text{MOD}(B_i) | 1 \le i \le n \}\);
+   (3) \((\{ \psi \in \Psi | \psi \vDash C \}\)，\(\tilde{\Psi} \cup \{ \text{MOD}(B_i) | 1 \le i \le n \})\) 是分布相容的。

依 \((\Psi, \tilde{\Psi})\) 的 \(\le_D\) 极大性，可得到

\(\{ \psi \in \Psi | \psi \vDash C \} = \Psi\)，\(\tilde{\Psi} \cup \{ \text{MOD}(B_i) | 1 \le i \le n \} = \tilde{\Psi}\)

因此，由 \(<d_i>_{i<\alpha}\) 的极大性，我们有 \(d \in <d_i>_{i<\alpha}\)，这表明 \(D'\) 是极大强相容集。

# 定义 3.6.8 分布相容的语义框架 \((\Psi, \tilde{\Psi})\) 关于缺省 \(A:MB_1, \cdots, MB_n/C\) 是稳定的，当且仅当 \(\text{Mod}(A) \nsubseteq \Psi\)，或者对某一 \(i: 1 \le i \le n\)，\(\text{MOD}(B_i) \notin \tilde{\Psi}\)。

利用缺省理论的语义框架和缺省理论论扩张的特征，可推导出缺省理论扩张的语义特征。

# 定理 3.6.1 缺省理论 \(\Delta=(D,W)\) 有扩张，当且仅当有 \(D' \subseteq D\) 和语义框架 \((\Psi, \tilde{\Psi})\) 满足

+   (1) \((\Psi, \tilde{\Psi})\) 是分布相容的;
+   (2) \((\text{MOD}(W), \{\Phi_i\}) \le_{<d_i>_{i<\alpha}} (\Psi, \tilde{\Psi})\)，其中，\(<d_i>_{i<\alpha}\) 是 \(D'\) 中所有缺省组成的序列;
+   (3) 对任一 \(d \in D \\ D'\)，\((\Psi, \tilde{\Psi})\) 关于 \(d\) 是稳定的。

证明 证明条件“仅当” 设 \(\Delta\) 有扩张，则有 \(D' \subseteq D\) 满足扩张的特征定理 3.2.1。

于是，由引理 3.6.4 易见 \(D' \cup T^*\) 的模型 \((\Psi, \tilde{\Psi})\) 是分布相容的，且 \((\text{MOD}(W), \{\Phi_i\}) \le_{<d_i>_{i<\alpha}} (\Psi, \tilde{\Psi})\)，其中 \(<d_i>_{i<\alpha}\) 是 \(D'\) 中所有缺省组成的序列。