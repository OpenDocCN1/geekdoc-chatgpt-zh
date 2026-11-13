### 6.2.3 遗忘算子的公理化刻画

在信念修正与更新研究中，公理化方法极为常见。本节沿用这一思路，从遗忘算子应具备的基本性质出发，构建遗忘理论的公设框架。

设 $C, D$ 为语言 $L$ 的子类，$V \subseteq A$。针对回答集程序的（不完全）遗忘算子 $f: C \times 2^A \to D$，我们提出以下核心公设（注：此处算子 $f$ 为偏函数，不保证处处有定义；且相关文献中尚存在其他公设）：

*   **存在性 (E):** 结果 $f(\phi, V)$ 必须落在目标类 $D$ 内，即 $f(\phi, V) \in D$。
*   **无关性 (IR):** 遗忘结果不应包含被遗忘集合 $V$ 中的任何命题符号。
*   **弱化 (W):** 原理论 $\phi$ 应逻辑蕴含遗忘结果，即 $\phi \models_{ht} f(\phi, V)$。
*   **正保留 (PP):** 若 $\phi \models_{ht} \zeta$ 且 $\zeta$ 与 $V$ 无关（$IR_{ht}(\zeta, V)$），则 $f(\phi, V) \models_{ht} \zeta$。
*   **负保留 (NP):** 若 $\phi \not\models_{ht} \zeta$ 且 $\zeta$ 与 $V$ 无关，则 $f(\phi, V) \not\models_{ht} \zeta$。
*   **强等价 (SE):** 若 $\phi \equiv_{ht} \zeta$，则遗忘后结果强等价：$f(\phi, V) \equiv_{ht} f(\zeta, V)$。
*   **结论保留 (CP):** 遗忘后程序的回答集恰为原程序回答集删去 $V$ 中原子后的集合，即 $SM(f(\phi, V)) = \{M \setminus V \mid M \in SM(\phi)\}$。

其中 $\phi, \zeta$ 分别属于 $C, D$ 类（如 Horn、正规、析取程序等），逻辑蕴含关系依具体语言语义定义。

---

#### 定理 6.2.6 (表示定理)
设 $\psi, \phi$ 为公式，$V \subseteq A$。以下三命题等价：

1.  $\psi \models_{ht} F_{ht}(\phi, V)$。
2.  $\psi \models_{ht} \{\xi \mid \phi \models_{ht} \zeta \land IR_{ht}(\zeta, V)\}$。
3.  算子 $F_{ht}$ 满足公设 (W)、(PP)、(NP) 与 (IR)，即存在满足这四条公设的算子 $f$ 使得 $\psi \models_{ht} f(\phi, V)$。

**证明梗概：** 由命题 6.2.2 可知 (1) 与 (2) 等价；推论 6.2.2 与命题 6.2.1 保证 (2) 蕴含 (1)。核心在于证 (3) $\Rightarrow$ (2)，即满足前述四公设的任意算子 $f$ 其结果强等价于该公式集。
*   (PP) 给出 $f(\phi, V) \models_{ht} \{\xi \mid \phi \models_{ht} \zeta \land IR_{ht}(\zeta, V)\}$。
*   由 (W) 与 (IR) 得 $\phi \models_{ht} f(\phi, V)$ 且 $IR_{ht}(f(\phi, V), V)$。若反向蕴含不成立，则存在 $\xi$ 满足 $IR_{ht}(\xi, V)$ 且 $f(\phi, V) \models_{ht} \xi$，但公式集 $\not\models_{ht} \xi$。结合 (W) 推出 $\phi \models_{ht} \xi$，与公式集定义矛盾。证毕。

---

#### 例 6.2.2：$F_{ht}$ 不满足结论保留 (CP)

考察程序 $\Pi = \{\neg p \to q,\ \neg q \to p,\ p \to r,\ q \to r\}$。
验证可得 $F_{ht}(\Pi, \{p\}) \equiv_{ht} \{\neg p \to r,\ p \to r\}$。
$\Pi$ 拥有两个回答集 $\{p,r\}$ 与 $\{q,r\}$；但 $F_{ht}(\Pi, \{q\})$ 唯一回答集为 $\{r\}$。显然，$SM$ 结果不符合 $\{M \setminus V\}$ 的要求，故 (CP) 失效。

---

#### 例 6.2.3：七大公设无法同时满足

假设全集 $A=\{p,q\}$，令 $\phi = (p \lor \neg p \lor q \lor \neg q) \land (p \to q \lor \neg q) \land (q \to p \lor \neg p)$。
$\phi$ 有两回答集：$\{p,q\}$ 与 $\varnothing$。
假设存在算子 $f$ 满足 (CP)，则 $f(\phi, \{p\})$ 应有回答集 $\{q\}$ 与 $\varnothing$。这要求 $\langle \varnothing, \{q\} \rangle \not\models_{ht} f(\phi, \{p\})$（否则 $\{q\}$ 不成其为回答集）。
然而 $\langle \varnothing, \{q\} \rangle \models_{ht} \phi$。若 $f$ 满足 (W)，则 $\phi \models_{ht} f(\phi, \{p\})$，迫使 $\langle \varnothing, \{q\} \rangle \models_{ht} f(\phi, \{p\})$。矛盾产生，故无算子能兼顾全部七公设。

---

#### 定义 6.2.3：SM-遗忘
公式 $\psi$ 称为 $\phi$ 关于 $V \subseteq A$ 的 **SM-遗忘结果**，当且仅当：
1.  $Mod_{ht}(\psi)$ 是 $Mod_{ht}(F_{ht}(\phi, V))$ 的极大子集；
2.  $SM(\psi) = \{M \setminus V \mid M \in SM(\phi)\}$。

**示例回顾 (例 6.2.3 续)：**
$Mod_{ht}(\phi) = A^{ht} \setminus \{\langle \varnothing, \{p,q\}\rangle, \langle \{q\}, \{p,q\}\rangle, \langle \{p\}, \{p,q\}\rangle\}$。
$Mod_{ht}(F_{ht}(\phi, \{p\})) = A^{ht}$（因 $\langle \varnothing, \{q\}\rangle, \langle \{q\}, \{q\}\rangle \in Mod_{ht}(\phi)$）。
为使 $SM(\psi)=\{q, \varnothing\}$，需取 $Mod_{ht}(\psi)$ 为满足条件的极大 $\{q\}$-子集，即 $\{\langle \varnothing, \varnothing\rangle, \langle \{q\}, \{q\}\rangle\}$，对应公式 $\psi \equiv_{ht} (q \lor \neg q)$。($A^{ht}$ 为 $A$ 上全体 HT-解释集)。

---

#### 定理 6.2.7：SM-遗忘的存在性与唯一性
对任意公式 $\phi$ 与 $V \subseteq A$，SM-遗忘结果 $\psi$ 存在；且若 $\psi'$ 也是结果，则 $\psi \equiv_{int} \psi'$（这里强等价蕴含下的等价）。

**证明构造：**
定义三个 HT-解释集合：
*   $G = \{\langle X, Y\rangle \mid X \subseteq Y,\ Y \in \{M\setminus V \mid M \in SM(\phi)\}\}$；
*   $H = \{\langle Y, Y\rangle \mid Y \in SM(F_{ht}(\phi, V)) \setminus \{M\setminus V \mid M \in SM(\phi)\}\}$；
*   $N = Mod_{ht}(F_{ht}(\phi, V)) \setminus (G \cup H)$。

**关键引理：** 对任意 $X \subset Y$，若 $\langle X, Y\rangle \in N$，则 $\langle Y, Y\rangle \in N$。
*推理链：* $\langle X, Y\rangle \in N \Rightarrow \langle X, Y\rangle \models_{ht} F_{ht}(\phi, V)$ 且 $\langle X, Y\rangle \notin G \cup H$。因 $X \subset Y$，自动 $\notin G$。又因 $X \subset Y$ 且满足 $F_{ht}$，得 $\langle Y, Y\rangle \models_{ht} F_{ht}(\phi, V)$ 且 $Y$ 非 $F_{ht}$ 的回答集，故 $\langle Y, Y\rangle \notin H$；亦非 $G$ 元素（因 $Y$ 非原回答集投影）。故 $\langle Y, Y\rangle \in N$。

令 $\psi$ 满足 $Mod_{ht}(\psi)=N$。易见 $SM(\psi)=\{M\setminus V \mid M \in SM(\phi)\}$，符合定义。

**唯一性证明：** 假设存在 $\psi'$ 满足条件且 $Mod_{ht}(\psi')=S \supsetneq N$。取 $\langle X, Y\rangle \in S \setminus N = G \cup H$ 分情况讨论：
*   若 $\in G$：则 $X=Y \in \{M\setminus V\}$。但 $X=Y$ 意味 $Y \notin SM(\psi')$（因 $\langle Y, Y\rangle$ 非严格子模型），与 $SM(\psi')$ 定义矛盾。
*   若 $\in H$：则 $X=Y$，且 $Y \in SM(F_{ht}(\phi, V))$ 但 $Y \notin \{M\setminus V\}$。因 $Y$ 是 $F_{ht}$ 的回答集，对任意 $Y'\subset Y$ 有 $\langle Y', Y\rangle \not\models_{ht} F_{ht}(\phi, V)$，故 $\langle Y', Y\rangle \notin S$。这导致 $Y \in SM(\psi')$。但 $Y \notin \{M\setminus V\}$，再次矛盾。

综上，$\psi$ 唯一（模强等价）。证毕。

---

#### 推论 6.2.3 (SM-遗忘的表示定理)
算子 $F_{sm}$ 满足公设 (E)、(IR)、(SE)、(CP) 与 (PP)。

**例 6.2.2 续析：** 对 $\Pi=\{\neg p \to q, \neg q \to p, p \to r, q \to r\}$，验证得 $F_{sm}(\Pi, \{q\}) \equiv_{ht} \{p \lor \neg p;\ r\}$。该程序超出析取逻辑程序表达能力，说明正规程序的 SM-遗忘结果不一定能在析取/正规类内封闭。

---

#### 命题 6.2.3：Horn 程序的特殊性
若 $\Pi$ 为 Horn 逻辑程序且 $V \subseteq A$，则 $F_{sm}(\Pi, V) \equiv_{ht} F_{ht}(\Pi, V)$。

**证明：** 沿用定理 6.2.7 构造的 $G, H, N$。
1.  **证 $H = \varnothing$：** 假设 $\langle Y, Y\rangle \in H$。则 $Y$ 是 $F_{ht}(\Pi, V)$ 的最小模型，但 $Y \neq M\setminus V$ ($M$ 为 $\Pi$ 最小模型)。存在 $\langle Z, Z\rangle \models_{ht} \Pi$ 使 $Z \to_r Y$（此处 $\to_r$ 表达某种模型关系），$V \cap Y = \varnothing$。推得 $Z\setminus V = Y$ 且存在 $M \subset Z$，故 $M\setminus V \subset Y$。但 $M\setminus V$ 是 $F_{ht}(\Pi, V)$ 模型，与 $Y$ 为最小模型矛盾。
2.  **证 $G \cap N = \varnothing$：** 假设 $\langle X, Y\rangle \in G \cap N$。则 $X \subset Y$ 且 $Y \in \{M\setminus V\}$。这意味着 $Y$ 是 $F_{ht}(\Pi, V)$ 的极小模型（回答集），但 $\langle X, Y\rangle \models_{ht} F_{ht}(\Pi, V)$ 且 $X \subset Y$，直接违背极小模型定义。矛盾。

因此对于 Horn 程序，$F_{sm}$ 与 $F_{ht}$ 重合。证毕。