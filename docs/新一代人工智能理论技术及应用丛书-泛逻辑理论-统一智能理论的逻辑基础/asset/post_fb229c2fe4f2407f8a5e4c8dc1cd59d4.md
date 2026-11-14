
- 1) 对合律 ~k~k p<=>p
- 2) 泛非性 k->h, k p<=>~k p->h, k; p->h, k<=>k->h, k ~~k p
- 3) 对偶律 ~~k ~p<=>~1-k p

## 2. 阈元量词^k 的运算模型和性质

如果推理处在不同误差水平上，则需要借用阈元量词^k 来标志命题真值的误差水平 k，为此增加了许多性质：

阈元量词的运算模型为

^k x = \Phi^{-1}(x, k)=x^{1/n}, \hat{x}^{0.5}=x

阈元量词^k 的记号用^{\Theta^k} 表示, ^{\Theta^k} 的运算模型为

^{\Theta^k} x=\Phi(x, k)=x^{n}

利用阈元量词^k 可将泛非非命题连接词表示为

~k p<=>^{\Theta^k} ~ ^{\Theta^k} p

### (1) 阈元量词^k 的基本性质

- 1) 第一移位规则 ^k~p<=>~k ^k p
- 2) 第二移位规则 如果 ^k p<=>q, 则 p<=>^{\Theta^k} q
- 3) 第三移位规则 ^k1 ^k2 p<=> ^k2 ^k1 p
- 4) 第四移位规则 ~k p<=>^k ~^{\Theta^k} p 或 ~p<=>^{\Theta^k} ~k ^k p
- 5) 第五移位规则 ~k2 ^k1 p<=> ^k2 ~^{\Theta} k1 ^k2 p
- 6) 第六移位规则 ^k2 ~ ~^{\Theta} k2 ^k1 ~ ~^{\Theta} k1 p<=> ~^{\Theta} k2 ^k1 ~ ~^{\Theta} k1 ^k2 p

### (2) 阈元量词^k 的运算规则

- 1) 逆运算规则 \Theta k=(2^a | a=1/log2 k)。阈元逆满足还原律 \Theta \Theta k=k。
- 2) 和运算规则 k1 \oplus k2=(2^a | a=-log2 k1 log2 k2)。阈元和满足对偶律 k1 \oplus k2=\Theta(\Theta k1 \Theta k2)。
- 3) 差运算规则 k1 \Theta k2=(2^a | a=-log2 k1/log2 k2)。差运算满足吸收律 k \Theta k=0.5，即 ^k \Theta ^k x=x。

## 3. 不等 k 泛非公式

- 1) 合成律 ~k2 ~k1 p<=> ~k3 p, k3=k1 \Theta k2=k1(1-k2)/(k1+k2-2k1 k2)
- 2) 吸收律 ~k ~0 p<=> ~0 p; ~k ~1 p<=> ~1 p; ~0 ~k p<=> ~0 ~p; ~1 ~k p<=> ~1 ~p
- 3) 互补律 ~\Theta k ~k p<=> ~k p, 其中 k'=k^2/(1-2k+2k^2)
- 4) 交换律 ~k2 ~k1 p<=> ~k1 ~k2 p
- 5) 对偶律 ~k1 ~ ~k1 p<=> ~k p, k2=N(k, k1), ~ ~k ~p<=> ~1-k p

## 10.4.2 永真蕴含公式（除 h=0 和 k=1 外）

### 1. 化简式

- Im1 \( p \wedge_{h,k} q \Rightarrow p \)
- Im2 \( p \wedge_{h,k} q \Rightarrow q \)
- Im3 \( \sim_k (p \Rightarrow_{0.5,k} q) \Rightarrow p \)
- Im4 \( \sim_k (p \Rightarrow_{0.5,k} q) \Rightarrow \sim_k q \)

### 2. 附加式

- Im5 \( p \Rightarrow p \vee_{h,k} q \)
- Im6 \( q \Rightarrow p \vee_{h,k} q \)
- Im7 如果 \( p \Rightarrow q \) 或 \( h=0.5 \)，则 \( \sim_k p \Rightarrow p \Rightarrow_{h,k} q \)
- Im8 \( q \Rightarrow p \Rightarrow_{h,k} q \)
- Im9 \( p, q \Rightarrow p \wedge_{h,k} q \)
- Im10 \( p \Rightarrow_{h,k} q \Rightarrow (r \wedge_{h,k} p) \Rightarrow_{h,k} (r \wedge_{h,k} q) \)
- Im11 \( p \Rightarrow_{h,k} q \Rightarrow (r \vee_{h,k} p) \Rightarrow_{h,k} (r \vee_{h,k} q) \)

### 3. 推论式

- Im12 \( \sim_k p, p \vee_{h,k} q \Rightarrow q \) 析取三段论
- Im13 \( p, p \Rightarrow_{h,k} q \Rightarrow q \) 假言推论
- Im14 如果 \( p \Rightarrow q \) 或 \( h=0.5 \)，则 \( \sim_k q, p \Rightarrow_{h,k} q \Rightarrow \sim_k p \) 拒取式
- Im15 \( p \Rightarrow_{h,k} q, q \Rightarrow_{h,k} r \Rightarrow p \Rightarrow_{h,k} r \) 假言三段论
- Im16 \( p \vee_{h,k} q, p \Rightarrow_{h,k} r, q \Rightarrow_{h,k} r \Rightarrow r \) 二难推论

### 4. 其他

- Im17 \( (p \Rightarrow_{h,k} r) \wedge_{h,k} (q \Rightarrow_{h,k} r) \Rightarrow (p \wedge_{h,k} q) \Rightarrow_{h,k} r \) 合并律
- Im18 \( r \Rightarrow_{h,k} (p \wedge_{h,k} q) \Rightarrow (r \Rightarrow_{h,k} p) \wedge_{h,k} (r \Rightarrow_{h,k} q) \) 泛与性
- Im19 \( \sim_k p \wedge_{h,k} p \Rightarrow k \) 广义冗余律
- Im18 \( (p \vee_{h,k} q) \Rightarrow_{h,k} r \Rightarrow (p \Rightarrow_{h,k} r) \wedge_{h,k} (q \Rightarrow_{h,k} r) \) 泛或性
- Im19 \( k \Rightarrow \sim_k p \vee_{h,k} p \) 广义冗余律
- Im20 \( p \Rightarrow (p \wedge_{h,k} q) \vee_{h,k} p \)
- Im21 \( (p \vee_{h,k} q) \wedge_{h,k} p \Rightarrow p \)
- Im22 \( (r \Rightarrow_{h,k} p) \wedge_{h,k} (r \Rightarrow_{h,k} q) \Rightarrow r \Rightarrow_{h,k} (p \vee_{h,k} q) \) 合并律
- Im23 \( p \wedge_{h,k} q \Rightarrow p \vee_{h,k} q \) 与或律
- Im24 \( p \Rightarrow p \Rightarrow_{h,k} (p \wedge_{h,k} q) \) 附加律
- Im25 \( p \Rightarrow_{h,k} q \Rightarrow (r \Rightarrow_{h,k} p) \Rightarrow_{h,k} (r \Rightarrow_{h,k} q) \) 附加律
- Im26 \( p \Rightarrow p \Rightarrow_{h,k} q \) 附加律
- Im27 \( q \Rightarrow p \Rightarrow_{h,k} q \) 附加律
- Im28 \( (p \Rightarrow_{h,k} q) \wedge_{h,k} (q \Rightarrow_{h,k} r) \Rightarrow p \Rightarrow_{h,k} r \) 传递性
- Im29 \( p \wedge_{h,k} q \Rightarrow p \Rightarrow_{h,k} q \) 与等律

## 10.4.3 永真等价公式（除 h=0 和 k=1 外）

### 1. 交换律

- Eq1 \( p \wedge_{h,k} q \Leftrightarrow q \wedge_{h,k} p \)
- Eq2 \( p \vee_{h,k} q \Leftrightarrow q \vee_{h,k} p \)
- Eq3 \( p \Rightarrow_{h,k} q \Leftrightarrow q \Rightarrow_{h,k} p \)

### 2. 结合律

- Eq4 \( p \wedge_{h,k} (q \wedge_{h,k} r) \Leftrightarrow (p \wedge_{h,k} q) \wedge_{h,k} r \)
- Eq5 \( p \vee_{h,k} (q \vee_{h,k} r) \Leftrightarrow (p \vee_{h,k} q) \vee_{h,k} r \)
- Eq6 \( p \Leftrightarrow_1 (q \Leftrightarrow_1 r) \Leftrightarrow (p \Leftrightarrow_1 q) \Leftrightarrow_1 r \)

### 3. 分配律

- Eq7 \( p \wedge_1 (q \vee_1 r) \Leftrightarrow (p \wedge_1 q) \vee_1 (p \wedge_1 r) \)
- Eq8 \( p \vee_1 (q \wedge_1 r) \Leftrightarrow (p \vee_1 q) \wedge_1 (p \vee_1 r) \)
- Eq9 \( p \Rightarrow_1 (q \Rightarrow_1 r) \Leftrightarrow (p \Rightarrow_1 q) \Rightarrow_1 (p \Rightarrow_1 r) \)

### 4. 否定深入律

- Eq10 \( \sim k \sim_k p \Leftrightarrow p \) 对合律
- Eq11 \( \sim_k (p \wedge_{h,k} q) \Leftrightarrow \sim_k p \vee_{h,k} \sim_k q \) 对偶律
- Eq12 \( \sim_k (p \vee_{h,k} q) \Leftrightarrow \sim_k p \wedge_{h,k} \sim_k q \) 对偶律
- Eq13 \( \sim_k (p \Rightarrow_{0.5, k} q) \Leftrightarrow p \wedge_{0.5, k} \sim_k q \)
- Eq14 如果 \( p \Rightarrow q \) 或 h=0.5, 则 \( \sim_k p \Rightarrow_{h,k} \sim_k q \Leftrightarrow q \Rightarrow_{h,k} p \) 反非律
- Eq15 iff \( p, q \in \{0, 1\} \) 时, \( \sim (p \Leftrightarrow q) \Leftrightarrow \sim p \Leftrightarrow q \)
- Eq16 iff \( p, q \in \{0, 1\} \) 时, \( \sim (p \Leftrightarrow q) \Leftrightarrow p \Leftrightarrow \sim q \)
- Eq17 \( \sim_k p \Rightarrow_{0.5, k} \sim_k q \Leftrightarrow p \Leftrightarrow_{0.5, k} q \) 非等律

### 5. 吸收律

- Eq18 \( p \wedge_1 p \Leftrightarrow p \)
- Eq19 \( p \vee_1 p \Leftrightarrow p \)
- Eq20 \( (p \wedge_1 q) \vee_1 p \Leftrightarrow p \)
- Eq21 \( (p \vee_1 q) \wedge_1 p \Leftrightarrow p \)
- Eq22 iff \( p, q \in \{0, 1\} \) 时，p->~~p<=>~~p
- Eq23 iff \( p, q \in \{0, 1\} \) 时，~~p->p<=>p
- Eq24 如果 p->q 或 p, q ∈ {0, 1}，则 (p->_{h,k} q)->_{h,k} p<=>p
- Eq25 如果 p->q 或 p, q ∈ {0, 1}，则 p->_{h,k} (p->_{h,k} q)<=>p->_{h,k} q
- Eq26 iff \( p, q \in \{0, 1\} \) 时，(p<->q)<->p<->q

### 6. 极值律

- Eq27 \( p \wedge_{h,k} 1 \Leftrightarrow p \) 同一律
- Eq28 \( p \vee_{h,k} 0 \Leftrightarrow p \) 同一律
- Eq29 \( p \wedge_{h,k} 0 \Leftrightarrow 0 \) 两极律
- Eq30 \( p \vee_{h,k} 1 \Leftrightarrow 1 \) 两极律
- Eq31 当 h<0.5 时，\( p \wedge_{h,k} \sim_k p \Leftrightarrow 0 \) 补余律
- Eq32 当 h<0.5 时，\( p \vee_{h,k} \sim_k p \Leftrightarrow 1 \) 补余律
- Eq33 \( 1 \to_{h,k} p \Leftrightarrow p \) 同一律
- Eq34 \( p \to_{0.5,k} 0 \Leftrightarrow \sim_k p \)
- Eq35 \( 0 \to_{h,k} p \Leftrightarrow 1 \) 恒真律
- Eq36 \( p \to_{h,k} 1 \Leftrightarrow 1 \) 恒真律
- Eq37 \( p \to_{h,k} p \Leftrightarrow 1 \) 恒真律
- Eq38 \( q \to_{h,k} (p \to_{h,k} q) \Leftrightarrow 1 \) 恒真律
- Eq39 \( 1 \leftrightarrow_{h,k} p \Leftrightarrow p \) 同一律
- Eq40 \( p \leftrightarrow_{h,k} p \Leftrightarrow 1 \) 恒真律
- Eq41 \( \sim_k p \leftrightarrow 0 \leftrightarrow_{0.5,k} p \)
- Eq42 iff \( p, q \in \{0, 1\} \) 时，~~p<->p<->0 恒假律

### 7. 连接词关系律

- Eq34 \( \sim_k p \leftrightarrow p \to_{0.5,k} 0 \)
- Eq41 \( \sim_k p \leftrightarrow p \to_{0.5,k} 0 \)
- Eq43 \( p \wedge_{h,k} q \Leftrightarrow \sim(\sim_k p \vee_{h,k} \sim_k q) \) 德摩根律
- Eq44 \( p \vee_{h,k} q \Leftrightarrow \sim(\sim_k p \wedge_{h,k} \sim_k q) \) 德摩根律
- Eq45 \( p \to_{0.5,k} q \Leftrightarrow \sim_k p \vee_{0.5,k} q \)
- Eq46 \( p \leftrightarrow_{h,k} q \Leftrightarrow (p \to_{h,k} q) \wedge_{h,k} (q \to_{h,k} p) \)
- Eq47 当 \( p, q \in \{0, 1\} \) 时，\( p \leftrightarrow q \Leftrightarrow (p \wedge q) \vee (\sim p \wedge \sim q) \)

### 8. 其他

- Eq48 \( (p \land_{h,k} q) \to_{h,k} r \leftrightarrow p \to_{h,k} q (q \to_{h,k} r) \)
- Eq49 如果 \( p \to q \)，则 \( p \to_{h,k} q \leftrightarrow (r \land_{h,k} p) \to_{h,k} (r \land_{h,k} q) \)
- Eq50 如果 \( p \to q \)，则 \( p \to_{h,k} q \leftrightarrow (r \lor_{h,k} p) \to_{h,k} (r \lor_{h,k} q) \)

## 10.4.4 新增逻辑公式（除 h=0 和 k=1 外）

### 1. 永真蕴含公式

- Im30 \( p \land_{h,k} q \to p @_{h,k} q \) 与平律
- Im31 \( p @_{h,k} q \to p \lor_{h,k} q \) 平或律
- Im32 \( p \land_{h,k} q \to p \circ^e_{h,k} q \) 与组律
- Im33 \( p \circ^e_{h,k} q \to p \lor_{h,k} q \) 组或律

### 2. 永真等价公式

- Eq51 \( p @_{h,k} q \leftrightarrow q @_{h,k} p \) 交换律
- Eq51 \( p @_{h,k} p \leftrightarrow p \) 幂等性
- Eq52 \( p @_{h,k} q (q @_{h,k} r) \leftrightarrow (p @_{h,k} q) @_{h,k} q (p @_{h,k} r) \) 自分配律
- Eq53 \( p \circ^e_{h,k} q \leftrightarrow q \circ^e_{h,k} p \) 交换律
- Eq54 \( p \circ^e_{h,k} e \leftrightarrow p \) 幺元律
- Eq55 \( e \circ^e_{h,k} e \leftrightarrow e \) 弃权律

## 10.5 命题泛逻辑学的演绎推理规则

### 1. 合式字母表规则

\( p, q, r, \cdots; 0, 1; \sim_k, \land_{h,k}, \lor_{h,k}, \to_{h,k}, \leftrightarrow_{h,k}, @_{h,k}, \circ^e_{h,k}, \angle^k_{h,k}, (,) \) 是合式字母表。

### 2. 合式公式生成规则

- 1) 0, 1 和原子命题 p, q, r, ... 是合式公式;
- 2) 如果 p, q 是合式公式，则 \sim_k p, \( (p \land_{h,k} q) \), \( (p \lor_{h,k} q) \), \( (p \to_{h,k} q) \), \( (p \leftrightarrow_{h,k} q) \), \( (p @_{h,k} q) \), \( (p \circ^e_{h,k} q) \), \( \angle^k_{h,k} \) 是合式公式;
- 3) 当且仅当有限次使用 1)、2) 所得到的符号串才是合式公式。

由于同一个命题 p 不仅真值相等，而且最大相关，h=1, T(x, x, 1)=x。又由于串行推理是与运算，所以在推演的任何步骤上，都可以任意次地引用同一个前提或结论，而不影响推理的最后结果。
