基始 设 \(n=1\)，则由 \(A \in T_{\Pi} \uparrow 1\) 知 \(A\) 是程序 \(\Pi\) 的一个单元子句的其中一个基例式。显然， \(\Pi \cup \{ \leftarrow A \}\) 有一个 SLD 反驳，故 \(A \in S\)。

归纳 设对任一 \(B \in T_{\Pi} \uparrow (n-1)\)， \(\Pi \cup \{ \leftarrow B \}\) 有一 SLD 的反驳。令 \(A \in T_{\Pi} \uparrow n\)，依 \(T_{\Pi}\) 的定义，存在 \(\Pi\) 中规则 \(B \leftarrow B_1, \cdots, B_k\) 的一个基例式使得对某一置换 \(\theta\)， \(A = B\theta\) 且 \(\{ B_1 \theta, \cdots, B_k \theta \} \subseteq T_{\Pi} \uparrow n-1\)。依归纳假设，对 \(i=1, \cdots, k\)， \(\Pi \cup \{ (\leftarrow B_i) \theta \}\) 有一个 SLD 的反驳。因每个 \(B_i \theta\) 是基原子，这些反驳可以组合为 \(\Pi \cup \{ \leftarrow (B_1, \cdots, B_k) \theta \}\) 的一个反驳。依定义 2.3.6， \(\Pi \cup \{ \leftarrow A \}\) 有一个 SLD 反驳，故 \(A \in S\)。

例 2.3.2 对确定程序 \(\Pi = \{ B(x) \leftarrow \neg A(x), C(x) \leftarrow \neg A(x), A(a) \leftarrow, D(a) \leftarrow \}\) 和确定约束 \(G \leftarrow \neg D(x), C(x)\)。有从 \(\Pi \cup \{ G \}\) 到 \(\perp\) 的 SLD 反驳：

+   (1) \(C_0 = G\);
+   (2) \(C_1 = D(a) \leftarrow\);
+   (3) \(C_2 = \leftarrow \neg C(a)\)，它是 \(G\) 和 \(C_1\) 用 mgu \(\theta_1 = \{ [a/x] \}\) 得到的 SLD 消解式；
+   (4) \(C_3 = C(x) \leftarrow A(x)\);
+   (5) \(C_4 = A(a) \leftarrow\);
+   (6) \(C_5 = C(a) \leftarrow\)，它是 \(C_3\) 和 \(C_4\) 用 mgu \(\theta = \{ [a/x] \}\) 得到的消解式；
+   (7) \(C_6 = \perp\)，它是 \(C_2\) 和 \(C_5\) 的 SLD 消解式。

例 2.3.3 程序 \(P_1\) 由下述三子句组成：

+   (1) \(p(x,z) \leftarrow q(x,y), p(y,z)\);
+   (2) \(p(x,x)\);
+   (3) \(q(a,b)\)。

约束是 \(\leftarrow p(x,b)\)。

在图 2-4 所示的 SLD 消解过程中，带下划线的原子是被选择的，数字 1,2,3 分别指出被选择原子消解时所用到的子句编号。消解产生一棵有三类分支的 SLD 消解树，即无穷分支；以空子句终止的分支（成功分支）和因为无可用规则而得到以死循环终止的分支（有穷失败分支）。

在本例中，总用出现在约束中的最后一个原子做消解。若总选择约束中第一个原子做消解，可以得到（至少在此例中）一有穷树。通常，给定确定程序 \(P\)，有一个用于选择 SLD 消解中被消解的原子的选择规则 \(R\)，它是由确定约束集到 Atom 的函数，使得对一个确定约束 \(G\)， \(R\) 取 \(G\) 中的一个原子（称为为被选择原子）为值。可以证明选择函数的独立性，即对于 \(R\) 的任意选择，如果 \(P \cup G\) 是不可满足的，则总可以找到用选择函数 \(R\) 的一个消解。