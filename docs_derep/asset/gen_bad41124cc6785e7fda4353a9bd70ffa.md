# 嵌套逻辑程序的语义基础与等价变换

在形式化嵌套逻辑程序时，我们首先约定几种特殊规则的简写形式：形如 \(F \leftarrow \top\) 的规则简记为 \(F \leftarrow\)，称为（嵌套）事实，直接等同于公式 \(F\)；而形如 \(\bot \leftarrow G\) 的规则称为约束，记作 \(\leftarrow G\)。

为便于后续讨论，引入符号表示符号集合：\(\text{GLit}_F\)、\(\text{GLit}_r\)、\(\text{GLit}_\Pi\) 分别代表公式 \(F\)、规则 \(r\)、程序 \(\Pi\) 中出现的所有文字集合；\(\text{GAtom}_F\)、\(\text{GAtom}_r\)、\(\text{GAtom}_\Pi\) 则对应其中出现的所有基原子集。若嵌套公式、规则或程序中不含缺省否定 `not`，则分别称为基本公式、基本嵌套规则、基本嵌套程序。这些基本构造分别对应经典逻辑中的公式（将连接词 “,” 与 “;” 视作 “\(\land\)” 与 “\(\lor\)”）、基本程序规则，以及由带合取头的基本规则组成的程序。

### 定义 5.2.1：归约操作

为赋予嵌套程序语义，需推广归约概念以定义回答集。首先递归定义协调文字集 \(S\) 与嵌套公式的非单调可满足关系 \(\vDash_n\)（下标 \(n\) 区别于经典可满足关系）：

*   **文字**：若 \(L \in S\)，则 \(S \vDash_n L\)。
*   **真/假**：\(S \vDash_n \top\)；\(S \not\vDash_n \bot\)。
*   **合取**：若 \(S \vDash_n F\) 且 \(S \vDash_n G\)，则 \(S \vDash_n (F,G)\)。
*   **析取**：若 \(S \vDash_n F\) 或 \(S \vDash_n G\)，则 \(S \vDash_n (F;G)\)。
*   **缺省否定**：若 \(S \not\vDash_n F\)，则 \(S \vDash_n \text{not } F\)。

若存在协调文字集 \(S\) 使 \(S \vDash_n F\)，称 \(F\) 可满足，\(S\) 为 \(F\) 的模型。\(F\) 的所有极小模型集记为 \(SM(F)\)。推论关系定义为：\(F \vDash_n G\) 当且仅当任意满足 \(F\) 的协调文字集 \(S\) 均满足 \(G\)。显然，对经典公式 \(F\)，\(S \vDash_n F \iff S \vDash F\)（经典逻辑结论）。

规则 \(r = \text{Head} \leftarrow \text{Body}\) 关于 \(S\) 的满足定义为：\(S \vDash_n \text{Body} \implies S \vDash_n \text{Head}\)。程序 \(\Pi\) 关于 \(S \subseteq \text{GLit}_\Pi\) 的满足要求 \(S\) 满足 \(\Pi\) 中每条规则。

**定义 5.2.1（公式归约）**
令 \(S\) 为协调文字集，\(F\) 为公式。\(F\) 关于 \(S\) 的归约 \(F^S\) 递归定义如下：
*   初等公式：\(F^S = F\)；
*   合取：\((F,G)^S = F^S, G^S\)；
*   析取：\((F;G)^S = F^S; G^S\)；
*   否定：若 \(S \vDash_n F\) 则 \((\text{not } F)^S = \bot\)；否则 \((\text{not } F)^S = \top\)。

规则 \(r = \text{Head} \leftarrow \text{Body}\) 的归约为 \(\text{Head}^S \leftarrow \text{Body}^S\)，记作 \(r^S\)。程序 \(\Pi\) 的归约为集合 \(\{r^S \mid r \in \Pi\}\)，记作 \(\Pi^S\)。

**推论 5.2.1**
对任意公式 \(F\) 和协调文字集 \(S\)，\(S \vDash_n F \iff S \vDash F^S\)（右侧为经典满足）。

**等价定义**
公式 \(F, G\) 等价（\(F \Leftrightarrow G\)）当且仅当 \(F \vDash_n G\) 且 \(G \vDash_n F\)。等价地，对任意协调文字集 \(X, Y\)，\(X \vDash_n F^Y \iff X \vDash_n G^Y\)。

### 定义 5.2.2：回答集

嵌套回答集程序 \(\Pi\) 的协调文字集 \(S\) 若是满足 \(\Pi^S\) 的**极小**协调文字集，则称 \(S\) 为 \(\Pi\) 的回答集。存在回答集的程序（或单规则）称为协调的。

### 例 5.2.1：回答集非极小性演示

1.  **程序 \(\Pi_1 = \{A; \text{not } A \leftarrow \top\}\)**  
    *   \(A \in S\) 时：\(\Pi_1^S = \{A \leftarrow \top\}\)，极小模型 \(\{A\}\)。
    *   \(A \notin S\) 时：\(\Pi_1^S = \{\top \leftarrow \top\}\)（等价于 \(\bot \leftarrow \bot\) 等），极小模型 \(\emptyset\)。
    *   结果：\(\Pi_1\) 有两个回答集 \(\{A\}, \emptyset\)。

2.  **程序 \(\Pi_2 = \{A \leftarrow \text{not not } A\}\)**  
    *   \(A \in S\)：\(\Pi_2^S = \{A \leftarrow \top\}\)，模型 \(\{A\}\)。
    *   \(A \notin S\)：\(\Pi_2^S = \{A \leftarrow \bot\}\)，模型 \(\emptyset\)。
    *   结果：回答集为 \(\emptyset, \{A\}\)。

对比 \(\{A \leftarrow A\}\)（唯一回答集 \(\emptyset\)）与 \(\{A \leftarrow \text{not } A\}\)（无回答集）。这表明嵌套程序的回答集**不满足极小性**——一个回答集可以是另一个的真子集。

### 例 5.2.2：强否定与双重否定的交互

1.  **\(\Pi_3 = \{\neg A \leftarrow \text{not not } A\}\)**  
    *   \(A \in S\)：归约为 \(\{\neg A \leftarrow \top\}\)，最小模型 \(\emptyset\)。含 \(A\) 的 \(S\) 均非回答集。
    *   \(A \notin S\)：归约为 \(\{\neg A \leftarrow \bot\}\)，\(\emptyset\) 为最小模型，故为回答集。
    *   唯一回答集：\(\emptyset\)。

2.  **\(\Pi_4 = \{A \leftarrow \text{not not } B\}\)**  
    *   \(B \in S\)：归约为 \(\{A \leftarrow \top\}\)，极小模型 \(\{A\} \not\subseteq S\)（因 \(B \in S\) 但 \(A \notin S\)），不满足极小性要求。
    *   \(B \notin S\)：归约为 \(\{A \leftarrow \bot\}\)，极小模型 \(\emptyset\)。
    *   唯一回答集：\(\emptyset\)。

### 定义 5.2.3：程序等价

嵌套程序 \(\Pi_1, \Pi_2\) 等价，指对任意协调文字集 \(X, Y\)：\(X\) 封闭于 \(\Pi_1^Y \iff X\) 封闭于 \(\Pi_2^Y\)。  
推论：等价公式归约后的回答集相同 \(\Rightarrow\) 等价程序回答集相同 \(\Rightarrow\) 等价程序在并入任意程序 \(\Pi\) 后保持等价。

### 引理 5.2.1：等价替换性（公式层面）

设 \(F, G, H\) 为公式，\(H'\) 由 \(H\) 中 \(F\) 的**正则出现**替换为 \(G\) 得到。若 \(F \Leftrightarrow G\)，则 \(H \Leftrightarrow H'\)。  
*证明*：对公式结构归纳。基步 \(H=F\) 显然。归纳步分合取、析取、否定三种构造，利用归约定义与归纳假设逐步推导，核心在于 \((\text{not } F)^Y\) 仅依赖 \(Y \vDash_n F\) 的真值，而等价性保证了该真值不变。

### 引理 5.2.2：等价替换性（程序层面）

嵌套程序 \(\Pi\) 中，用等价公式 \(G\) 替换 \(F\) 的正则出现得到 \(\Pi'\)，则 \(\Pi \Leftrightarrow \Pi'\)。  
*证明*：利用引理 5.2.1 及规则满足的定义，对任意 \(X, Y\) 证明 \(X\) 封闭于 \(\Pi^Y \iff X\) 封闭于 \((\Pi')^Y\)。

### 引理 5.2.3：基础等价律

任意公式 \(F, G, H\) 满足：
1.  **交换律**：\(F,G \Leftrightarrow G,F\)；\(F;G \Leftrightarrow G;F\)。
2.  **结合律**：\((F,G),H \Leftrightarrow F,(G,H)\)；\((F;G);H \Leftrightarrow F;(G;H)\)。
3.  **分配律**：\(F,(G;H) \Leftrightarrow (F,G);(F,H)\)；\(F;(G,H) \Leftrightarrow (F;G);(F,H)\) —— *注意第二式应为 \((F;G);(F;H)\)，原文可能有误*。
4.  **德摩根律**：\(\text{not }(F,G) \Leftrightarrow \text{not } F; \text{not } G\)；\(\text{not }(F;G) \Leftrightarrow \text{not } F, \text{not } G\)。
5.  **三重否定消解**：\(\text{not not not } F \Leftrightarrow \text{not } F\)。
6.  **真元吸收**：\(F,\top \Leftrightarrow F\)；\(F;\top \Leftrightarrow \top\)。
7.  **假元吸收**：\(F,\bot \Leftrightarrow \bot\)；\(F;\bot \Leftrightarrow F\)。
8.  **原子互补**：若 \(A\) 为原子，\(A,\neg A \Leftrightarrow \bot\)；\(\text{not } A; \text{not }\neg A \Leftrightarrow \top\)。
9.  **常量否定**：\(\text{not }\top \Leftrightarrow \bot\)；\(\text{not }\bot \Leftrightarrow \top\)。

*证明提要*：(4) 利用归约定义：\(\text{not }(F,G)^Y = \top \iff Y \not\vDash_n F^Y, G^Y \iff (\text{not } F)^Y = \top \lor (\text{not } G)^Y = \top\)。(5) 通过归约链式推导：\(\text{not not not } F\) 归约为 \(\top\) 当且仅当 \(Y \not\vDash_n \text{not not } F \iff \dots \iff Y \not\vDash_n F \iff \text{not } F\) 归约为 \(\top\)。

### 定义 5.2.4：简单合取式与析取式

*   **简单合取式**：\(L_1, \dots, L_k, \text{not } L_{k+1}, \dots, \text{not } L_m, \text{not not } L_{m+1}, \dots, \text{not not } L_n\) （\(0 \le k \le m \le n\)）。
*   **简单析取式**：\(L_1; \dots; L_k; \text{not } L_{k+1}; \dots; \text{not } L_m; \text{not not } L_{m+1}; \dots; \text{not not } L_n\)。

约定：空合取为 \(\top\)，空析取为 \(\bot\)。

由引理 5.2.2、推论 5.2.2 及引理 5.2.3 导出：

**推论 5.2.2**：\(\text{not}\) 将简单合取式与简单析取式互换。

**引理 5.2.4：范式存在性**  
任一公式等价于：
1.  **析取范式 (DNF)**：\(F_1; \dots; F_n\) (\(n \ge 1\))，每 \(F_i\) 为简单合取式。
2.  **合取范式 (CNF)**：\(F_1, \dots, F_n\) (\(n \ge 1\))，每 \(F_i\) 为简单析取式。
*证明*：对公式结构归纳，利用分配律与德摩根律逐层推入。

### 引理 5.2.5：规则结构等价变换

设 \(F, G, H\) 为公式：
1.  **头部分解**：\(F, G \leftarrow H \Leftrightarrow \{F \leftarrow H,\; G \leftarrow H\}\)。
2.  **身体析取分解**：\(F \leftarrow G; H \Leftrightarrow \{F \leftarrow G,\; F \leftarrow H\}\)。
3.  **否定上移（身体变头）**：\(F \leftarrow G, \text{not } H \Leftrightarrow F; \text{not } H \leftarrow G\)。
4.  **否定下移（头变身体）**：\(F; \text{not } G \leftarrow H \Leftrightarrow F \leftarrow H, \text{not } G\)。

*证明*：均通过考察归约 \((\text{not } H)^Y\) 取值为 \(\top/\bot\) 时的满足条件等价性直接验证。

### 定理 5.2.1：嵌套规则的标准正规形式

综合上述引理，任一嵌套程序规则可等价转换为以下两种标准形式之一的集合：

**形式一（头部析取，身体合取）：**
\[
L_1; \dots; L_i \leftarrow L_{i+1}, \dots, L_k, \text{not } L_{k+1}, \dots, \text{not } L_m, \text{not not } L_{m+1}, \dots, \text{not not } L_n
\quad (0 \le i \le k \le m \le n)
\]

**形式二（头部合取含否定，身体合取含否定）：**
\[
L_1; \dots; L_i; \text{not } L_{i+1}; \dots; \text{not } L_k \leftarrow L_{k+1}, \dots, L_m, \text{not } L_{m+1}, \dots, \text{not } L_n
\quad (0 \le i \le k \le m \le n)
\]

其中 \(L_j\) 均为文字。

**约定记法**：将合取 \(L_1 \land \dots \land L_k\) 视为集合 \(\{L_1, \dots, L_k\}\)。记 \(\text{not } F = \{\text{not } L_1, \dots, \text{not } L_m\}\)，\(\text{not not } F = \{\text{not not } L_1, \dots, \text{not not } L_m\}\)。于是任一嵌套规则统一表示为：
\[
C \leftarrow B,\; \text{not } F_1,\; \text{not not } F_2
\]
其中 \(B, F_1, F_2\) 为文字集合，且不全为空。