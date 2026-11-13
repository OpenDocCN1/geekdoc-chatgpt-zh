显然，闭公式 $A$ 的 Herbrand 域是由 $A$ 中出现的个体和函数符号生成的所有基项的集合。公式 $A$ 的 Herbrand 基 $B_A$ 是由 $A$ 中出现的谓词符号和 $H_A$ 中的基项生成的所有基原子的集合。

+   为定义公式 $A$ 的 Herbrand 赋值 $v$，$v$ 应该满足：
+   (1) 若 $a$ 是 $A$ 中出现的个体，则 $a^v=a \in H_A$;
+   (2) 若 $u$ 是 $A$ 中出现的自由变元，则 $u^v=u \in H_A$;
+   (3) 若 $f$ 是 $A$ 中出现的 n 元函数且 $t_1^v, \cdots, t_n^v \in H_A$，则 $f^v(t_1^v, \cdots, t_n^v)=f(t_1, \cdots, t_n) \in H_A$。类似地，定义任意公式集的 Herbrand 解释。

一阶公式集 S 的一个 Herbrand 模型是在相应的 Herbrand 赋值下，S 的 Herbrand 基 $B_S$ 中取值为真的所有基原子的集合。如前述，将 S 的 Herbrand 赋值等同于它的 Herbrand 模型。

给定形如 $\forall x_1 \cdots \forall x_n A(x_1, \cdots, x_n)$ 的无 $\exists$ 前束范式 A，称用 $t_1, \cdots, t_n \in H_A$ 分别代入母式 $A(x_1, \cdots, x_n)$ 中 $x_1, \cdots, x_n$ 而得到的 $A(t_1, \cdots, t_n)$ 为母式的例式。特别地，没有变元出现的例式是母式的基例式。如果 A 中没有自由出现的变元，则其母式的例式是基例式。

称形如 $\forall x_1 \cdots \forall x_n A$ 的公式为全称语句，其中，$x_1, \cdots, x_n$ 是出现在 A 中的所有变元且 A 中无量词出现。

引理 1.3.1 设 A 是形如 $\forall x_1 \cdots \forall x_n B$ 的全称语句，若 A 在赋值 v 下可满足，则有一 Herbrand 赋值 $v_1$，使得 A 在赋值 $v_1$ 下是可满足的。

证明 设赋值 v 的论域为非空集合 D，公式 A 的 Herbrand 域为 $H_A$。构造 A 的 Herbrand 赋值 $v_1$，满足对 A 中出现的任意 n 元谓词符号 “p” 和等词符号 “=”，以及任何 $t_1^v, \cdots, t' \in H_A$：
\(< t_1^v, \cdots, t'_n > \in p^v\) 当且仅当 \(< t_1^v, \cdots, t_n^v > \in p^{v_1}\)，即
\(p^{v_1}(t'_1, \cdots, t'_n) = p^{v_1}(t'_1, \cdots, t'_n)\)
\(< t'_1, t'_2 > \in =^{v_1}\) 当且仅当 \(< t'_1, t'_2 > \in =^{v_1}\)，即
\((t'_1, t'_2)^{v_1} = (t_1, t_2)^v\)

以上过程归纳于公式 B 的结构，容易证明 $A^v = 1$。

**定理 1.3.5** 全称语句 A 是不可满足的，当且仅当 A 在所有 Herbrand 赋值下都取假值。等价地，全称语句集 T 是协调的，当且仅当 T 有一 Herbrand 模型。

**定理 1.3.6 (Herbrand 定理)** 无 $\exists$ 前束范式 $\forall x_1 \cdots \forall x_n A(x_1, \cdots, x_n)$ 是不可满足的，当且仅当存在它的母式 $A(x_1, \cdots, x_n)$ 的一个有穷基例式集是不可满足的。