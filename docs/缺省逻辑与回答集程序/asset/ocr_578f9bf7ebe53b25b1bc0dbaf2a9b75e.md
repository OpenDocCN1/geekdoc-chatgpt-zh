则组成的逻辑程序称为一般逻辑程序。若 \(D=\emptyset\)，则是析取规则；若 \(C=D=\emptyset\)，则是正规则；若 \(D=\emptyset\) 且 \(|E| \leqslant 1\)，则是正规规则；若 \(C=D=\emptyset\) 且 \(|E| \leqslant 1\)，则是 Horn 规则。析取（正规、正、Horn）逻辑程序是仅由析取（正规、正、Horn）规则组成的逻辑程序。

# 定理 6.2.3 若 \(\phi\) 是正逻辑程序的，则 \(<X,Y>\vDash_{ht}\phi\)，当且仅当 \(X=\phi\) 且 \(Y=\phi\)。

证明 不失一般性，令 \(\phi = \wedge B \rightarrow \vee C\)。 \(X \vDash \wedge B \rightarrow \vee C\) 且 \(Y \vDash \wedge B \rightarrow \vee C\) 当且仅当 \(X \vDash \wedge B\) 蕴含 \(X \vDash \vee C\)，且 \(Y \vDash \wedge B \rightarrow \vee C\)； 当且仅当 \(<X,Y>\vDash_{ht}\wedge B\) 蕴含 \(<X,Y>\vDash_{ht}\vee C\)，且 \(Y \vDash \wedge B \rightarrow \vee C\)； 当且仅当 \(<X,Y>\vDash_{ht}\wedge B \rightarrow \vee C\)。

该定理表明，正逻辑程序之间的强等价与命题公式（在经典命题逻辑下）的等价一致，即

# 推论 6.2.1 正逻辑程序 \(\Pi_1\) 与 \(\Pi_2\) 强等价，当且仅当它们在命题逻辑中等价，即 \(\Pi_1\vDash_{ht}\Pi_2\) 当且仅当 \(\Pi_1 \equiv \Pi_2\)。

证明 “当” 由 \(\Pi_1 = \Pi_2\) \(\Rightarrow\) 对任何解释 \(I\)，\(I=\Pi_1\)，当且仅当 \(I=\Pi_2\)； \(\Rightarrow\) 对任何解释 \(X\)，\(Y(X \subseteq Y)\)，\(X=\Pi_1\) 且 \(Y=\Pi_1\)，当且仅当 \((X=\Pi_2\) 且 \(Y=\Pi_2)\)； \(\Rightarrow\) 对任何解释 \(X\)，\(Y(X \subseteq Y)\)，\(<X,Y>\vDash_{ht}\Pi_1\)，当且仅当 \(<X,Y>\vDash_{ht}\Pi_2\) 蕴含 \(\Pi_1\vDash_{ht}\Pi_2\)。 “仅当” 由 \(\Pi_1\vDash_{ht}\Pi_2\) \(\Rightarrow\) 对任何 ht-解释 \(<X,X>\)，\(<X,X>\vDash_{ht}\Pi_1\)，当且仅当 \(<X,X>\vDash_{ht}\Pi_2\)； \(\Rightarrow\) 对任何 ht-解释 \(<X,X>\)，\(X=\Pi_1\)，当且仅当 \(X=\Pi_2\)（定理 6.2.3）； \(\Rightarrow \Pi_1 \equiv \Pi_2\)。

# 定理 6.2.4 令 \(\Gamma\) 是 ht-解释的集，则存在逻辑程序 \(\phi\)，使得 (1) Mod\(_{ht}(\phi)=\Gamma\)，当且仅当 \(<X,Y> \in \Gamma\) 蕴含 \(<Y,Y> \in \Gamma\)； (2) Mod\(_{ht}(\phi)=\Gamma\) 且 \(\phi\) 是析取逻辑程序，当且仅当 \(<X,Y> \in \Gamma\)，\(Y \subseteq Z\)，且 \(<Z,Z> \in \Gamma\) 蕴含 \(<X,Z> \in \Gamma\)； (3) Mod\(_{ht}(\phi)=\Gamma\) 且 \(\phi\) 是正逻辑程序，当且仅当 \(<X,Y> \in \Gamma\)，当且仅当 \(<X,X> \in \Gamma\) 且 \(<Y,Y> \in \Gamma\)； (4) Mod\(_{ht}(\phi)=\Gamma\) 且 \(\phi\) 是正规逻辑程序，当且仅当 \(\phi\) 是析取逻辑程序，而且 \(<X,Y>\)，\(<Z,Y> \in \Gamma\) 蕴含 \(<X \cap Z, Y> \in \Gamma\)。

① 析取逻辑程序可以通过展开（unfolding）和转移（shifting）的方法翻译成等价的正规逻辑程序（有相同的回答集），但是展开通常导致指数空间增长。