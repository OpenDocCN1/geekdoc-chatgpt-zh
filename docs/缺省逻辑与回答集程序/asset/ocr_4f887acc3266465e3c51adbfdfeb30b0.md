式 Hd(App(P, I)) = Hd(App(Q, I)) 成立。

证明 令 $r^* = gr(r, r')$ 且 $Q=P \cup \{r^*\}$。已知 $P \subseteq Q$ 且 $App(P, I) \subseteq App(Q, I)$。假设 $App(Q, I)=App(P, I) \cup \{r^*\}$。显然， $I = Bd(r^*)$ 成立。下面讨论两种情况：

(1) $I = l$: 有 $I = Bd(r^*) \cup \{l\}$
\(\Rightarrow\) 由于 $Bd(r^*) = Bd(r) - \{l\}$， $I = Bd(r)$
\(\Rightarrow r \in App(P, I)\)
\(\Rightarrow Hd(r) = Hd(App(P, I))\)
\(\Rightarrow\) 由 $Hd(r) = Hd(r^*)$， $Hd(r^*) \in Hd(App(P, I))\)
\(\Rightarrow Hd(App(P, I)) = Hd(App(Q, I))\)
\(\Rightarrow Hd(App(P, I)) = Hd(App(Q, I))$。

(2) $I = \bar{l}$: 有 $I = Bd(r^*) \cup \{\bar{l}\}$
\(\Rightarrow I = Bd(r')$，因为 $Bd(r') - \{l\} \subseteq Bd(r^*)\)
\(\Rightarrow Hd(r) \in Hd(App(P, I)), Hd(r) \in Hd(App(Q, I))\)
\(\Rightarrow Hd(App(P, I)) = Hd(App(Q, I))\) 因为 $Hd(r') \subseteq Hd(r^*)$。

引理 6.1.1 令 $r, r'$ 为两条规则，使得 $Bd^+(r) \cap Bd^-(r) = Bd^+(r') \cap Bd^-(r') = \emptyset$ 及 $l$ 是一个文字。如果 $r$ 关于 $r'$ 在 $l$ 上是可析取基消解的，则 $(Bd^+(r) \cap Bd^-(r')) \cup (Bd^-(r) \cap Bd^+(r')) = \{l\}$。

证明 根据定义 6.1.3 的条件 (1)，有 $l \in Bd(r)$ 且 $\bar{l} \in Bd(r')$。这意味着 $\{l\} \subseteq (Bd^+(r) \cap Bd^-(r')) \cup (Bd^-(r) \cap Bd^+(r'))$。假设有另一个文字 $l'$ 是 $(Bd^+(r) \cap Bd^-(r')) \cup (Bd^-(r) \cap Bd^+(r'))$ 的成员。如果 $l'$ 是 $Bd^+(r) \cap Bd^-(r')$ 的成员，由于 $Bd^+(r) \cap Bd^-(r) = \emptyset$，考虑以下两种情形：

(1) $l' \notin Bd^-(r)$，即 $\overline{l'} \notin Bd(r)$ （因为 $l' \in Bd^+(r)$）；

(2) $l' \in Bd^-(r)$，即 $\overline{l'} \in Bd(r)$。

因为 $l \neq l'$，所以 $Bd(r') = \{\bar{l}\} \nsubseteq Bd(r)$。这与定义 6.1.3 条件 (2) 矛盾。另一种情况可类似证明。

以下定理表明，删除被包含规则对析取基消解没有影响。

定理 6.1.3 令 $r, r', r''$ 为析取规则，使得 $r < r'$ 且 $r'$ 关于 $r''$ 在文字 $l$ 上可析取基消解，则当 $r$ 关于 $r''$ 在文字 $l$ 上可析取基消解时有 $r \leq gr(r', r'')$ 或 $gr(r, r'') \leq gr(r', r'')$ 成立。

证明 根据 $r < r'$，有 $Hd(r) \subseteq Hd(r')$ 和 $Bd(r) \subseteq Bd(r')$，并且 $Hd(r) \subset Hd(r')$ 和 $Bd(r) \subset Bd(r')$ 中的至少一个成立。消解式 $gr(r', r'')$ 是规则 $Hd(r') \leftarrow Bd(r') - \{l\}$。如果 $r$ 关于 $r''$ 在 $l$ 上是可析取基消解的，则消解式 $gr(r, r'')$ 是规则 $Hd(r) \leftarrow$