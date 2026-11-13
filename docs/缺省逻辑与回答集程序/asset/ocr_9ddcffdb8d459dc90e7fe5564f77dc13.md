```
F:=Form(W_1) \cup Con(D_2);
IF F=A THEN RETIURN (true) ELSE RETURN (false)
```

此程序计算一个布尔函数以处理成员（MEMBER）问题：判断断言<A:R>是否属于由生成缺省集 \( D'\subseteq D \) 生成的 CDL 扩张。

注意：上述程序的正确性直接由它们的定义与定理 7.2.5 得到。这些程序中的每一个除要求运行多项式函数的推理测试（\(\vdash\)）外，还需要运行输入大小的多项式时间。

基于利用上述三个程序作为模块，可以处理命题 ADT 的所有扩张计算与主要推理问题。

```
ALL-CDL-EXTENSION (D,W)
result:=\emptyset;
REPEAT
  new:=\emptyset;
  FOR EACH D' \in 2^D-result DO
    IF GDSET(D,W,D') THEN new:=new \cup {D'};
  result:=result \cup new;
UNTIL new = \emptyset
RETURN (result)
```

此程序返回 \(\Delta=(D,W)\) 的所有 CDL 扩张(ALL-CDL-EXTENSION)的生成缺省集。

```
BOOLEAN FUNCTION SR (A,R,D,W)
FOR EACH D' \subseteq D DO
  IF GDSET(D,W,D') AND NOT MEMBER (A,R,D,W,D')
  THEN RETURN (false)
RETURN (true)
```

此程序处理审慎推理（SR）问题，回答<A:R>是否在 \(\Delta=(D,W)\) 的所有 CDL 扩张中出现。类似地，下面三个程序分别处理 SR^*, CR 与 CR^* 问题。

程序 1:

```
BOOLEAN FUNCTION SR* (A,D,W)
FOR EACH D' \subseteq D DO
  IF GDSET(D,W,D') AND NOT Form(W) \cup Con(D') \vdash A
  THEN RETURN (false)
RETURN (true)
```