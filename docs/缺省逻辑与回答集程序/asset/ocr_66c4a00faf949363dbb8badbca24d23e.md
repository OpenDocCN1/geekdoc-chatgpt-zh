当且仅当 \( S \) 是 \( \Pi_4^S \) 的回答集（因为\((\Pi_4 \cup \Pi)^S=\{p(a);p(b), p(a)\leftarrow\} \cup \Pi^S\);
当且仅当 \( S \) 是 \( \Pi_4 \) 的回答集。
因此, \( \Pi_3 \) 和 \( \Pi_4 \) 强等价。

类似于 2.3.2 节将基本程序规则和约束转换为确定程序规则和约束回答集程序的方式，可以将回答集程序转换为等价的不含经典否定的程序。为此，对回答集程序语言中 \( L \) 的每一 \( n \) 元 (\( 0 \leqslant n \)) 谓词符 \( p \)，令 \( p^+ \) 是不在 \( L \) 中出现的新的 \( n \) 元谓词符， \(\mathbf{GAtom}^+=\{p^+(t)|p(t)\in\mathbf{GAtom}\}\)，其中， \(\mathbf{GAtom}\) 是 \( L \) 的全体基原子组成的集合，\( p \) 是 \( L \) 的 \( n \) 元 (\( 0 \leqslant n \)) 谓词符，\( t \) 是基项。

映射 DEF 定义：对任一 \( L \in \mathbf{GLit} \) (\( L \) 的全体基文字组成的集合)，
若 \( L=A \)，则 \(\text{DEF}(L)=L\)；
若 \( L=\neg A \)，则 \(\text{DEF}(L)=L^+\)。
其中， \( A \in \mathbf{GAtom} \)。注意， \(\mathbf{GLit}=\mathbf{GAtom} \cup \{\neg A | A \in \mathbf{GAtom}\}\)，则有
\(\mathbf{GAtom}^+=\{A^+ | \neg A \in \mathbf{GLit}, A \in \mathbf{GAtom}\}\)

显然， DEF 是 \(\mathbf{GIt}\) 到 \(\mathbf{GAtom} \cup \mathbf{GAtom}^+\) 上的 1-1 映射。

拓广 DEF 到文字集，回答集程序规则和约束及回答集：

+   • \(\text{DEF}(S)=\{\text{DEF}(L)|L \in S\}\)，其中 \( S \subseteq \mathbf{GLit}\);
+   • \(\text{DEF}(\text{Head} \leftarrow \text{Pos, not Neg})=\text{DEF}(\text{Head} \leftarrow \text{DEF}(\text{Pos}), \text{not DEF}(\text{Neg}))\);
+   • \(\text{DEF}(\Pi)=\{r^+ | r \in \Pi\}\)，其中 \(\Pi\) 是回答集程序，\( r \) 是回答集程序规则;
+   • \(\text{DEF}(\leftarrow \text{Pos,not Neg}) \leftarrow \text{DEF}(\text{Pos}), \text{not DEF}(\text{Neg})\)。

从以上可以看出， DEF 是基本程序集到正规程序集上的 1-1 映射，这里，正规程序集的每一成员是在扩展的基原子子集 \(\mathbf{GAtom} \cup \mathbf{GAtom}^+\) 定义的。

因为程序 \(\text{DEF}(\Pi)\) 的回答集可能包含一对原子 \( A \) 和 \( A^+\)，类似于基本程序，引入 Contr 作为特殊原子，用于标识矛盾规则 \( A \leftarrow B, B^+\) 和 \( A^+ \leftarrow \neg B, B^+\) 的集合，其中 \( A \) 和 \( B \) 是任意一对不同的基原子。于是增加 Contr，如果 \(\text{DEF}(\Pi) \cup \text{Contr}\) 有回答集 \( S \subseteq \mathbf{GAtom} \cup \mathbf{GAtom}^+\)，则 \(\text{DEF}(S)\) 是 \(\text{DEF}(\Pi)\) 的不包含任何原子对 \( A \) 和 \( A^+\) 的回答集，因此文字集 \(\text{DEF}^{-1}(S)\) 是协调的，其中， \(\text{DEF}^{-1}\) 是 DEF 的逆映射。

对任一文字集 \( S \subseteq \mathbf{GLit}\)，称 \(\text{DEF}(S)\) 是合式的，当且仅当 \(\text{DEF}(S)\) 不包含任何形如 \( A \) 和 \( A^+\) 的基原子对。

令 \(\Pi^+=\text{DEF}(\Pi) \cup \text{Contr}\)。称 \( S \subseteq \mathbf{GAtom} \cup \mathbf{GAtom}^+\) 是 \(\Pi^+\) 的回答集，当且仅当 \( S \) 是合式的且 \( S \) 是 \(\text{DEF}(\Pi)\) 的回答集。

# 定理 4.1.3 协调的基文字集 \( S \) 是回答集程序 \( \Pi \) 的回答集，当且仅当 \(\text{DEF}(S)\) 是 \(\Pi^+\) 的回答集。