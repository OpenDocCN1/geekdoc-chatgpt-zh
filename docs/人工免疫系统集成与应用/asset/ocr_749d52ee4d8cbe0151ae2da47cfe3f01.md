协同检测。模型的层次结构表示如式(11-31)所示。

\(\Omega_{RIDS} = \{L_P, L_L, L_H\}\) (11-31)

式中，$L_P$ 为规则检测层，识别了大部分已知的人侵类型；$L_L$ 为适应性检测层，能发现许多变型攻击、组合攻击和未知攻击；$L_H$ 为协同检测层，对适应性检测进行协同确认，从而对 $L_L$ 的误检进行纠正。

# 定理 11-1 $L_P$ 和 $L_L$ 的双层检测比只采取单层检测的检测率高。

证明 $L_P$ 的检测率:$R_{TD}(L_P)=P(Y_{LP}\cdot Flag=0|X_{RIDS} \in Type_x \wedge Type_x\cdot Flag=0)$。

$L_L$ 的检测率:$R_{TD}(L_L)=P(Y_{LL}\cdot Flag=0|X_{RIDS} \in Type_x \wedge Type_x\cdot Flag=0)$。

按照抗原识别过程，P 检测器和 L 检测器的检测结果是或的关系，系统检测率为

$R_{TD}(SYS) = P(Y_{LL}\cdot Flag = 0 \vee Y_{LP}\cdot Flag = 0 | X_{RIDS} \in Type_x \wedge Type_x\cdot Flag = 0)$
$= P(\{Y_{LL}\cdot Flag = 0\} \cup \{Y_{LP}\cdot Flag = 0\} | X_{RIDS} \in Type_x \wedge Type_x\cdot Flag = 0)$

因为

\{Y_{LL}\cdot Flag = 0\} \subsetneq \{Y_{LL}\cdot Flag = 0\} \cup \{Y_{LP}\cdot Flag = 0\}
\{Y_{LP}\cdot Flag = 0\} \subsetneq \{Y_{LL}\cdot Flag = 0\} \cup \{Y_{LP}\cdot Flag = 0\}

所以

$R_{TD}(SYS) > R_{TD}(L_P), \quad R_{TD}(SYS) > R_{TD}(L_L)$

证毕。

根据漏检率的定义，定理 11-1 等价于如下命题：

$L_P$ 和 $L_L$ 的双层检测比只采取单层检测的漏检率低。

# 定理 11-2 $L_L$ 和 $L_H$ 的协同检测比仅采用 $L_L$ 层检测的误检率低。

证明 只采用 $L_L$ 的误检率:$R_{FD}(L_L) = P(Y_{LL}\cdot Flag = 0 | X_{RIDS} \in Type_x \wedge Type_x\cdot Flag = 1)$，采用 $L_L$ 与 $L_H$ 协同的误检率。

$R_{FD}(L_L \& L_H) = P(Y_{LL}\cdot Flag = 0 \wedge Y_{LH} = Y_{LL} | X_{RIDS} \in Type_x \wedge Type_x\cdot Flag = 1)$
$= P(\{Y_{LL}\cdot Flag = 0\} \cap \{Y_{LH} = Y_{LL}\} | X_{RIDS} \in Type_x \wedge Type_x\cdot Flag = 1)$

因为

\{Y_{LL}\cdot Flag = 0\} \cap \{Y_{LH} = Y_{LL}\} \subsetneq \{Y_{LL}\cdot Flag = 0\}

所以

$R_{FD}(L_L \& L_H) < R_{FD}(L_L)$

证毕。