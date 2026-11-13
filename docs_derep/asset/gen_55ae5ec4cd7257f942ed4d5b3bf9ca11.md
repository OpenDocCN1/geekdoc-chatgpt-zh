## 定理 7.2.5 判断断言归属的充要条件

设 $\Delta=(D,W)$ 为有穷良基的断言缺省理论（ADT），$<A:R>$ 为任一断言。

**(1) 不协调即排除**  
若 $R \cup \text{Form}(W) \cup \text{Supp}(W)$ 出现冲突，则 $<A:R>$ 肯定不属于 $\Delta$ 的任何 CDL 扩张。

**(2) 协调则唯一对应**  
若上述集合协调一致，则：
- 约简理论 $\Delta_R=(D[R],W[R])$ 拥有唯一的 CDL 扩张 $F$；
- 该扩张的生成缺省集满足 $\text{GD}(F,\Delta_R)=\mathcal{A}(D[R],\Delta_R)$；
- $<A:R>$ 属于 $\Delta$ 的某个 CDL 扩张 **当且仅当** $<A:R> \in F$。

**证明梳理**

**第 (1) 部分**：反证法。假设 $<A:R>$ 落入某扩张 $E$，依性质必有 $R \subseteq \text{Supp}(E)$。推论 4.2.1 随即推出 $R \cup \text{Form}(W) \cup \text{Supp}(W)$ 协调，与前提矛盾。

**第 (2) 部分**：核心在于证③。  
**引理 7.2.1** 直接给出 $\Delta_R$ 唯一扩张 $F$ 及生成缺省集等式。

*③ 充分性（“当”)*：  
假设 $<A:R> \in F$。构造 $\Delta'_R=(D[R],W)$，因 $\text{Form}(W) \cup \text{Supp}(W) \cup \text{Ccs}(D[R]) \cup \text{Con}(D[R])$ 协调，**引理 7.2.7** 保证 $\Delta'_R$ 有唯一扩张 $F'$，且 $\text{GD}(F',\Delta'_R)=\mathcal{A}(D[R],\Delta'_R)$。  
归纳可得：$\forall i\ge 0,\ D[R]_i(\Delta_R) \subseteq D[R]_i(\Delta'_R)$ 且 $F_i \subseteq F'_i$。故 $\mathcal{A}(D[R],\Delta_R) \subseteq \mathcal{A}(D[R],\Delta'_R)$ 与 $F \subseteq F'$。  
又 $D[R] \subseteq D$，依 CDL 扩张**半单调性**，存在 $\Delta$ 的扩张 $E$ 使 $F' \subseteq E$，从而 $<A:R> \in E$。

*③ 必要性（“仅当”)*：  
设 $E$ 为含 $<A:R>$ 的 $\Delta$ 扩张，令 $D'=\text{GD}(E,\Delta)$。**引理 7.2.7** 给出 $\Delta'_R=(D[R],W[R])$ 唯一扩张 $F'$，满足 $\text{GD}(F',\Delta'_R)=\mathcal{A}(D'[R],\Delta'_R)=\mathcal{A}(D'[R],\Delta_R)$。  
**引理 7.2.9** 推出：  
$\text{Form}(W[R]) \cup \text{Con}(\mathcal{A}(D'[R],\Delta_R)) \vdash A$  
$\text{Supp}(W[R]) \cup \text{Ccs}(\mathcal{A}(D'[R],\Delta_R)) \cup \text{Con}(\mathcal{A}(D'[R],\Delta_R)) = R$  
**推论 7.2.2** 致 $A \in \text{Form}(F')$。因 $\mathcal{A}(D'[R],\Delta_R) \subseteq D[R]$，半单调性使 $\Delta_R$ 存在含 $F$ 的扩张；结合唯一性得 $A \in \text{Form}(F)$ 且 $R \subseteq \text{Supp}(F)$。定义给 $R=\text{Supp}(F)$，**引理 7.2.8** 终确认 $<A:\text{Supp}(F)> \in F$。

> **推论**：该定理为 CDL 冒险推理算法提供了理论支撑。

---

## 定理 7.2.6 CDL 的累积性

设 $\Delta=(D,W)$ 为 ADT。若存在 CDL 扩张 $F$ 包含 $<A:R>$，则：  
$E$ 是含 $<A:R>$ 的 $\Delta$ 的 CDL 扩张 **$\Leftrightarrow$** $E$ 是 $\Delta'=(D, W \cup \{<A:R>\})$ 的 CDL 扩张。

**证明要点**  
*非良基情形*：唯一扩张为 $\text{Th}_S(W)$ 与 $\text{Th}_S(W \cup <A:R>)$，因 $<A:R> \in \text{Th}_S(W)$，两者扩张完全一致。  
*良基且 $<A:R> \notin W$* 时分两向证：

*必要性（“仅当”)*：  
令 $D'=\text{GD}(E,\Delta)$。因 $<A:R> \in E$，$D'$ 对 $\Delta'$ 联合相容。归纳得 $\forall i,\ D'_i(\Delta) \subseteq D'_i(\Delta')$，故 $D' = \Lambda(D',\Delta')$。  
任取 $D^* \supseteq D'$ 且 $\Lambda(D^*,\Delta')=D^*$ 且对 $\Delta'$ 联合相容，则 $D^*$ 对 $\Delta$ 亦联合相容。利用定理 1.3.1 与归纳比较生成序列，证 $D^* \subseteq D'$，即 $D^*=D'$。依定理 7.2.2，$D'$ 是 $\Delta'$ 扩张的生成缺省集，$E$ 即为该扩张。

*充分性（“当”)*：  
设 $E$ 为 $\Delta'$ 扩张。**定理 7.2.5** 给 $\Delta_R=(D[R],W[R])$ 唯一扩张 $F$ 含 $<A:R>$。构造 $\Delta'_R=(D'[R],W'[R])$ ($W'=W \cup \{<A:R>\}$)，同理证 $F$ 为 $\Delta'_R$ 扩张，$D^*=\text{GD}(F,\Delta_R)$ 为其生成缺省集。协调性保证 $F$ 是 $\Delta'_R$ **唯一**扩张。  
再证 $E$ 为 $\Delta$ 扩张：只需验证任意 $D' \subseteq D$ 满足 (1) 对 $\Delta'$ 联合相容 $\Rightarrow$ 对 $\Delta$ 联合相容（显然）；(2) $\Lambda(D',\Delta')=D' \Rightarrow \Lambda(D',\Delta)=D'$。后者借助 $D^* \subseteq D'$ 及生成序列嵌套关系得证。最终 $D'$ 为 $\Delta$ 扩张 $E$ 的生成缺省集，$F \subseteq E$ 推出 $<A:R> \in E$，故 $E'=E$。

---

### 例 7.0.4 与 7.0.5 续析：累积性对比

- **L 扩张**不具备累积性。  
- **CDL** 下均满足：  
  *例 7.0.3* 增加 $<p\lor q:\{p\}>$ 后，缺省 $p\lor q:M\lnot p/\lnot p$ 因 $\{\{p\}\} \cup \{\lnot p\}$ 不协调而失效，无新扩张产生。  
  *例 7.0.4* 增加 $<\text{Pet}:\{\text{Bird}\}>$ 后，缺省 $\text{Pet}:M\text{Dog}/\text{Dog}$ 因 $\{\text{Bird}\} \cup \{\text{Dog}\} \cup \{\text{Dog}\to\text{Bird}\}$ 不协调而阻断，累积性成立。

---

## 7.2.3 CDL 扩张计算算法

Reiter 默认逻辑利用有穷特征计算扩张，CDL 同理。但 CDL 处理的是**断言（公式+支承对）**，支承组合可能引发指数爆炸，推理空间复杂度上升。定理 7.2.5 揭示的**局部性质**可大幅简化计算。另因推理对象分“纯公式”与“断言”，需区分两类审慎/冒险推理。

### 核心推理问题定义

**定义 7.2.11（显式支承管理）**  
设 $\Delta=(D,W)$ 为任意 ADT，$P=<A:\{r_0,\dots,r_k\}>$。
- **审慎推理 (SR)**：判定 $P$ 是否在**所有** CDL 扩张中。是则称 $P$ 为 S-结论，记 $\Delta \models_s P$。
- **冒险推理 (CR)**：判定 $P$ 是否在**某个** CDL 扩张中。是则称 $P$ 为 C-结论，记 $\Delta \models_c P$。

**定义 7.2.12（忽略具体支承，仅关公式）**  
设 $\Delta=(D,W)$ 为良基 ADT，$A$ 为一阶闭公式。
- **SR***：$\forall$ 扩张 $E,\ \exists R$ 使 $<A:R> \in E$？记 $\Delta \models_{s^*} A$。
- **CR***：$\exists$ 扩张 $E,\ \exists R$ 使 $<A:R> \in E$？记 $A \leftarrow_c A$（原文符号）。

---

### 基础测试程序（针对命题 ADT）

设 $\Delta=(D,W)$ 为命题 ADT，$D' \subseteq D$，$<A:R>$ 为命题断言。

#### 程序 1：求生成缺省闭包
[PRE0]  
输入 $\Delta, D'$，输出 $\mathcal{A}(D',\Delta)$（即 $\text{LAMBDA}(D,W,D')$）。

#### 程序 2：判定生成缺省集
[PRE1]  
算法 **GDSET** 判定 $D'$ 是否为良基 $\Delta$ 的某 CDL 扩张的生成缺省集，返回布尔值。

#### 程序 3：断言成员检测
[PRE2]  
算法 **MEMBER** 判定 $<A:R>$ 是否属于由 $D'$ 生成的 CDL 扩张，返回布尔值。

> **正确性**：直接源于定义与定理 7.2.5。每程序除调用多项式时间经典推演 ($\vdash$) 外，自身运行时间为输入规模的多项式。

---

### 组合算法解决全局问题

以上三程序作为模块，支撑命题 ADT 的全扩张枚举与四大推理任务。

#### ALL-CDL-EXTENSION (D,W)
[PRE3]  
枚举 $\Delta=(D,W)$ 所有 CDL 扩张的生成缺省集。

#### 审慎/冒险推理求解器
[PRE4]  
程序 1 处理 **SR** 问题：判定 $<A:R>$ 是否现于 $\Delta$ 所有扩张。  
后续三程序分别对应 **SR***、**CR**、**CR*** 问题。

#### 程序 1：
[PRE4]  （注：原文此处标记为 Program 1，对应 SR 判定流程）