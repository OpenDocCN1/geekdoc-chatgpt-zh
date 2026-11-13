若是，写作\( A \leftarrow_c A \)。

为计算一个命题 ADT (即出现在 ADT 的断言集 \( W \) 与缺省集 \( D \) 中的公式都是命题公式) 和解决相应的主要推理任务, 我们给出几个基本的运算推导测试程序, 它们是在经典逻辑推导 (\( \vdash \)) 上具外部信息源的测试程序。

设 \( A=(D,W) \) 是命题 ADT, \( D' \) 是 \( D \) 的子集, \( <A:R> \) 是一命题断言 (即 \( A \) 与 \( R \) 分别是命题公式和命题公式集)。

# 程序 1:

```
FUNCTION LAMBDA(D, W, D')
result:=Ø;
REPEAT
new:=Ø;
FOR EACH d=A:M B/CeD'\result DO
IF W U Con (result)U A THEN
new:= new U {d} ;
result:=result Unew;
UNTIL new:=Ø;
RETURN (result)
```

此程序对 \( A=(D,W) \) 与 \( D' \subseteq D \) 返回 \( A(D', \Delta) \), 即 LAMBDA(D, W, D')。

# 程序 2:

```
BOOLEAN FUNCTION GDSET (D, W, D')
IF Form (W) U Supp (W) U Ccs (D') U Con (D) |- false THEN RETURN (false)
FOR EACH d=A:MB/CeD\D' DO
F Form (W) USupp (W) UCcs (D') UCon (D) U {B,C}|-false AND Form (W) U
Con (D') |- A
THEN RETURN (false)
IF LAMBDA (D, W, D') ≠ D' THEN RETURN (false)
RETURN (true)
```

此程序计算一个布尔函数, 当 \( D' \) 是良基的 \( A=(D,W) \) 的一个 CDL 扩张的生成缺省集时, 算法 GDSET 输出布尔值 “true”。

```
BOOLEAN FUNCTION MEMBER (A, R, D, W, D')
W1:=W[R] ; D1:=D[R] ; D2:=LAMBDA (D, W, D') ;
S:=Supp (W) U Ccs (D2) U Con (D2) ;
IF S |- R THEN RETURN (false) ;
```