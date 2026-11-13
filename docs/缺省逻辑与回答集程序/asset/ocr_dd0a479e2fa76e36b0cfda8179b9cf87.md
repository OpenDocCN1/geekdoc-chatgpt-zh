穷特征，我们引入下述语义概念以确立扩张的语义特征。

令 \(\Phi\) 是一阶语言 \(L\) 的所有赋值组成的类。给定任意一阶闭公式集 \(F\)，令 \(F\) 的全体模型的集合为 \(MOD(F)=\{\psi \in \Phi, |\psi \vDash F\}\)。若 \(F=\varnothing\)，则令 \(MOD(F)=\Phi\)；令 \(MOD(\perp)=\varnothing\)，\(MOD(\top)=\Phi\)。

如前所述，给定一个集合 \(S\)，\(P(S)\) 表示 \(S\) 的幂集。

**定义 3.6.1** 称对子 \((\Psi, \tilde{\Psi})\) 是语义框架，其中 \(\Phi \neq \varnothing, \Psi \subseteq \Phi, \tilde{\Psi} \in P(\Phi)\)。若语义框架 \((\Psi, \tilde{\Psi})\) 满足：对任意 \(\Omega \in \tilde{\Psi}\)，\(\Psi \cap \Omega \neq \varnothing\)，则称它是分布式相容的。对任意语义框架 \((\Psi, \tilde{\Psi})\) 和 \((\Omega, \tilde{\Omega})\)，\((\Psi, \tilde{\Psi}) \neq (\Omega, \tilde{\Omega})\)，当且仅当 \(\Psi \neq \Omega\) 或 \(\tilde{\Psi} \neq \tilde{\Omega}\)。

**定义 3.6.2** 令 \(d=A:MB_1, \cdots, MB_n/C\) 是一缺省，定义语义框架上的关系 \(\le_d\) 如下：对任意两个语义框架 \((\Psi_1, \tilde{\Psi}_1)\) 和 \((\Psi_2, \tilde{\Psi}_2)\)，\((\Psi_1, \tilde{\Psi}_1) \le_d (\Psi_2, \tilde{\Psi}_2)\)，当且仅当 (1) 对任意 \(\psi \in \Psi_1, \psi \vDash A\)； (2) \(\Psi_2 = \{\psi \in \Psi_1 | \psi \vDash C\}\)； (3) \(\tilde{\Psi}_2 = \tilde{\Psi}_1 \cup \cup_{i \le i \le n} \{MOD(\{B_i\})\}\)。显然，若 \((\Psi_1, \tilde{\Psi}_1) \le_d (\Psi_2, \tilde{\Psi}_2)\)，则 \(\Psi_2 \subseteq \Psi_1, \tilde{\Psi}_1 \subseteq \tilde{\Psi}_2\)。

注意: 若 \(A\) 或 \(C\) 是永假公式，则对任意 \((\Psi_1, \tilde{\Psi}_1)\) 和 \((\Psi_2, \tilde{\Psi}_2)\)，\((\Psi_1, \tilde{\Psi}_1) \le_d (\Psi_2, \tilde{\Psi}_2)\) 不成立。

拓广上述关系到任意缺省序列和缺省集，具体如下：

**定义 3.6.3** 令 \((\Psi_1, \tilde{\Psi}_1)\) 和 \((\Psi_2, \tilde{\Psi}_2)\) 是任意两个语义框架，\(<d>_{i \ge \alpha}\) 是缺省序列 (\(\alpha\) 是一序数)。归纳地定义关系 \(\le_{<d>, i \ge \alpha}\) 如下： \((\Psi_1, \tilde{\Psi}_1) \le_{<d>, i \ge \alpha} (\Psi_2, \tilde{\Psi}_2)\) 当且仅当有语义框架序列 \((\Omega_i, \tilde{\Omega}_i)_{i < \alpha}\)，使得 (1) \((\Omega_0, \tilde{\Omega}_0) = (\Psi_1, \tilde{\Psi}_1)\)； (2) 对后继序数 \(i\): \((\Omega_i, \tilde{\Omega}_i) \le_{d_{i+}} (\Omega_{i-1}, \tilde{\Omega}_{i-1})\)； (3) 对极限序数 \(i\): \(\Omega_i = \cap_{j < i} \Omega_j, \tilde{\Omega}_i = \cup_{j < i} \tilde{\Omega}_j\)； (4) \((\Psi_2, \tilde{\Psi}_2) = (\Omega_\alpha, \tilde{\Omega}_\alpha)\)。

对空缺省序列 \(\Diamond\)，定义 \((\Psi_1, \tilde{\Psi}_1) \le_{\Diamond} (\Psi_2, \tilde{\Psi}_2)\)，当且仅当 \(\Psi_1 = \Psi_2\) 且 \(\tilde{\Psi}_1 = \tilde{\Psi}_2\)。对只由一个缺省 \(d\) 组成的序列 \(<d>\)，视 \((\Psi_1, \tilde{\Psi}_1) \le_{<d>} (\Psi_2, \tilde{\Psi}_2)\) 等同于 \((\Psi_1, \tilde{\Psi}_1) \le_d (\Psi_2, \tilde{\Psi}_2)\)。

**定义 3.6.4** 令 \(D\) 是缺省集，定义关系 \(\le_D\)，如下： \((\Psi_1, \tilde{\Psi}_1) \le_D (\Psi_2, \tilde{\Psi}_2)\)，当且仅当 \((\Psi_1, \tilde{\Psi}_1) = (\Psi_2, \tilde{\Psi}_2)\) 或存在 \(D\) 中某些缺省构成的非空序列 \(<d_i>_{i \ge \alpha}\)，使得 \((\Psi_1, \tilde{\Psi}_1) \le_{<d_i>_{i \ge \alpha}} (\Psi_2, \tilde{\Psi}_2)\)。 \((\Psi, \tilde{\Psi}) <_D (\Omega_\alpha, \tilde{\Omega}_\alpha)\)，当且仅当 \((\Psi, \tilde{\Psi}) \le_D (\Omega_\alpha, \tilde{\Omega}_\alpha)\) 且 \((\Psi, \tilde{\Psi}) \neq (\Omega_\alpha, \tilde{\Omega}_\alpha)\)。