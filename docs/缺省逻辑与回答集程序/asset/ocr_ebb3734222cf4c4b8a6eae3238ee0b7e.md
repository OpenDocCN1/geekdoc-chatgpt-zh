的一个解的成员。
用集合 \( rm(\Pi, S)=\{Head(r)\leftarrow Pos(r)\backslash S, \ not(Neg(r)\backslash S) \mid r \in \Pi \} \) 表示由 \(\Pi\) 中每一规则删除其正体（Pos）和负体（Neg）中属于 \(S\) 的所有原子后得到的规则。
定义 4.3.12 令 \( U=<U_\alpha >_{\alpha<\mu} \) 是正规程序 \(\Pi\) 的一个分裂序列，称 \( b_{v_\emptyset}(\Pi) \ , \ rm(b_{v_{\alpha+1}}(\Pi)\backslash b_{v_\alpha}(\Pi), U_\alpha) \quad (\alpha+1<\mu) \) 为 \(\Pi\) 的 \(U\)-组件。

显然，对任意原子集 \(S\), \( e_{v_\emptyset}(b_{v_{\alpha+1}}(\Pi)\backslash b_{v_\alpha}(\Pi), S) \) 是 \( rm(b_{v_{\alpha+1}}(\Pi)\backslash b_{v_\alpha}(\Pi), U_\alpha) \) 的子集。因此，式（4-11）中的每一程序是 \(\Pi\) 的一个 \(U\)-组件的一个子集。
应用 \(U\)-组件可以推出一些特殊类型的正规程序的简单特征。

定理 4.3.10 正规程序 \(\Pi\) 是局部分层的，当且仅当 \(\Pi\) 的一个分裂序列 \(U\)，使得 \(\Pi\) 的所有 \(U\)-组件都是确定程序。

证明 “当” 设 \( U=<U_\alpha >(\alpha<\mu) \) 是 \(\Pi\) 的一个使得所有 \(U\)-组件都是确定程序的分裂序列，定义层次映射 \(\lambda\): 对任意 \( A \in \mathbf{GAtom}_\Pi, \ \lambda(A)=min\{\alpha \mid A \in U_\alpha\} \)，容易验证 \(\Pi\) 是局部分层的。

“仅当” 设 \(\Pi\) 是局部分层的，\(\lambda\) 是相应的层次映射，\(\mu=min\{\beta \mid \lambda(A)<\beta, \ \text{对任意 } A \in \mathbf{GAtom}_\Pi\} \)。对每一 \(\alpha<\mu\), 定义 \( U_\alpha=\{A \in \mathbf{GAtom}_\Pi \mid \lambda(A)<\alpha\} \)。容易验证, \( U=<U_\alpha >_{\alpha<\mu} \) 是 \(\Pi\) 的一个分裂序列且 \(\Pi\) 的所有 \(U\)-组件都是确定程序。

定理 4.3.11 正规程序 \(\Pi\) 是序协调的当且仅当 \(\Pi\) 有一分裂序列 \(U\)，使得所有的 \(U\)-组件都是正当之分的。

证明 “当” 设 \(\Pi\) 有一分裂序列 \( U=<U_\alpha >_{\alpha<\mu} \)，使得所有的 \(U\)-组件都有正负之分，定义层次映射 \(\lambda\)，使得对任意 \( A \in \mathbf{GAtom}_\Pi, \ \lambda(A)=min\{\alpha \mid A \in U_\alpha\} \)，则易见 \(\Pi\) 是序协调的。

“仅当” 设 \(\Pi\) 是序协调的，\(\lambda\) 是相应的层次映射。将 \(\mathbf{GAtom}_\Pi\) 中的原子安排为序列 \( <A_\alpha >_{\alpha<\mu} \)，使得若 \(\alpha<\beta\), 则 \(\lambda(A_\alpha)<\lambda(A_\beta)\)。定义 \(\Pi\) 的分裂序列为 \( U_\alpha=\cup_{\alpha<\mu}(\Pi_\alpha^+ \cup \Pi_\alpha^-) \)。容易证明：对此时序列 \(U\), 所有的 \(U\)-组件都有正负之分。

如定理 2.3.10 所示，确定程序 \(\Pi\) 的结论集是单调算子 \(T_\Pi\) 的最小不动点且可以通过在空集上迭代算子 \(T_\Pi\) 从下逼近。对正规程序可以定义类似的构造，然而，类似于 \(T_\Pi\) 的单调函数的定义更为复杂，而且此单调函数的最小不动点可能是结论集的一个真子集，以致不能由逼近达到。尽管如此，这个单调函数及其不动点在理论和计算方面仍然是很有意义的。为此，引入程序的良基语义。

对正规程序 \(\Pi\), 定义由 \(\mathbf{GAtom}_\Pi\) 到 \(\mathbf{GAtom}_\Pi\) 的函数 \(\gamma_\Pi\), 使得对任意 \( X \subseteq \mathbf{GAtom}_\Pi, \ \gamma_\Pi(X)=C_\Pi(\Pi^X) \)。