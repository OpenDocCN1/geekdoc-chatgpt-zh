=> <Y, Y> \in \text{Mod}_{\text{ht}}(F_{\text{ht}}(\phi, V)), 且 Y \notin \text{SM}(F_{\text{ht}}(\phi, V)) (因为 <X, Y> \vDash_{ht} F_{\text{ht}}(\phi, V) 且 X \subset Y);
=> <Y, Y> \in \text{Mod}_{\text{ht}}(F_{\text{ht}}(\phi, V)) 且 <Y, Y> \notin (G \cup H);
=> <Y, Y> \in \text{Mod}_{\text{ht}}(F_{\text{ht}}(\phi, V)) \\ (G \cup H)。

令公式 \(\psi\) 满足 \(\text{Mod}_{\text{ht}}(\psi)=N\)，显然 \(\text{SM}(\psi)=\{M-V | M \in \text{SM}(\phi)\}\)。

假设存在可用公式 \(\psi'\) 表达的 ht-解释集 \(S\) (即 \(\text{Mod}_{\text{ht}}(\psi')=S\)) 满足 \(\text{SM}(\psi')=\{M-V | M \in \text{SM}(\phi)\}\)，且存在 \(<X, Y> \in SN\)，即 \(<X, Y> \in G \cup H\)。

考虑如下两种情形：

<X, Y> \in G, 则 X=Y, Y \in \{M-V | M \in \text{SM}(\phi)\}, 显然 Y \notin \text{SM}(\psi'), 即 \text{SM}(\psi') \neq \{M-V | M \notin \text{SM}(\phi)\}，矛盾。

<X, Y> \in H, 则 X=Y，即 <Y, Y> \in H，则有 Y \in \text{SM}(F_{\text{ht}}(\phi, V)) 且 Y \notin \{M-V | M \in \text{SM}(\phi)\}。

注意到

Y \in \text{SM}(F_{\text{ht}}(\phi, V))
=> Y \cap V = \emptyset, <Y, Y> \vDash_{ht} F_{\text{ht}}(\phi, V) 且对任何 Y' \subset Y, <Y', Y> \notin F_{\text{ht}}(\phi, V);
=> 任何 Y' \subset Y, <Y', Y> \notin S;
=> Y \in \text{SM}(\psi')。

同样地，因为 Y \notin \{M-V | M \in \text{SM}(\phi)\}，有 \text{SM}(\psi') \neq \{M-V | M \in \text{SM}(\phi)\}。

下面的推论由上面的定理和 \(F_{\text{ht}}\) 的表示定理（定理 6.2.6）可得。

# 推论 6.2.3 (表示定理) \(F_{sm}\) 满足 (E)、(IR)、(SE)、(CP) 和 (PP)。

对于例 6.2.2，令 \(\Pi=\{\neg p \rightarrow q, \neg q \rightarrow p, p \rightarrow r, q \rightarrow r\}\)。不难验证 \(F_{sm}(\Pi, \{q\}) \equiv_{ht} \{p \vee \neg p; r\}\)。该程序显然不能用析取逻辑程序表示，故正规逻辑程序的 sm-遗忘也不能保证用析取（正规）逻辑程序来表示。

# 命题 6.2.3 若 \(\Pi\) 是 Horn 逻辑程序且 \(V \subseteq A\)，则 \(F_{sm}(\Pi, V) \equiv_{ht} F_{ht}(\Pi, V)\)。

证明 按照定理 6.2.7 的证明构造 \(G, H, N\)。

首先证明 \(H = \emptyset\)。假设存在 \(<Y, Y> \in H\)。则

=> Y \in \text{SM}(F_{\text{ht}}(\Pi, V)) \\ \{M-V | M \in \text{SM}(\Pi)\};
=> Y 是 \(F_{\text{ht}}(\Pi, V)\) 的最小模型, \(Y \neq M-V\), 其中 \(M\) 是 \(\Pi\) 的最小模型;
=> 存在 \(<Z, Z> \vDash_{ht} \Pi\) 且 \(Z \rightarrow r Y\), \(V \cap Y = \emptyset\), \(Y \neq M-V\);
=> Z - V = Y, \(M \subset Z\), \(Y \neq M-V\);
=> M - V \subset Y, 且 \(M - V\) 是 \(F_{\text{ht}}(\Pi, V)\) 的模型，矛盾。

其次证明 \(G \cap N = \emptyset\)。假设存在 \(<X, Y> \in G \cap N\)。