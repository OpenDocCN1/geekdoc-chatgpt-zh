![](bbox=[0.22, 0.11, 0.78, 0.29])

(b) $\phi^{-1}(x,k)$ and $1-\phi(x,k)$

图 A.3 上正常形 N 范数完整簇 N(x, k) 和它的 N 性生成元完整簇 $\phi(x, k)$ 及其对偶

+   2) $\Phi_2(x, k)=\text{ite}\{0|x=0; \Gamma[(x-2k+1)/(2-2k)]\}$
+   $1-\Phi_2(x, k)=\text{ite}\{1|x=0; \Gamma[(1-x)/(2-2k)]\}$
+   $\Phi_2^{-1}(x, k)=\text{ite}\{0|x=0; \Gamma[(2-2k)x+2k-1]\}$

+   3) $\Phi_3(x, k)=\text{ite}\{0|x=0; 1|x=1; \Gamma[x-k+0.5]\}$
+   $1-\Phi_3(x, k)=\text{ite}\{1|x=0; 0|x=1; \Gamma[0.5+k-x]\}$
+   $\Phi_3^{-1}(x, k)=\text{ite}\{0|x=0; 1|x=1; \Gamma[x+k-0.5]\}$

它们都能生成同一个上正常形 N 范数

$N(x, k)=\text{ite}\{1|x=0; 0|x=1; \Gamma[2k-x]\}$

由于 $\phi(x, k)$ 簇中的复合运算和逆运算仍然在 $\phi(x, k)$ 簇内

$\Phi_1(\Phi_1(x, k), k_2) = \text{ite}\{1|x=1; \Gamma[x/(2k)/(2k_2)]\}$
$= \text{ite}\{1|x=1; \Gamma[x/(2k_2)]\} = \Phi_1(x, k_2)$
$\Phi_1^{-1}(x, k) = \text{ite}\{1|x=1; \Gamma[2kx]\} = \Phi_1(x, k)$

所以 $N(x, k)$ 是 N 范数完整簇。

# 4. 指数型 N 范数完整簇

图 A.4 给出了指数型 N 范数完整簇 $N(x, k)$ 和它的 N 性生成元完整簇 $\phi(x, k)$ 及其对偶 $N(x, 1-k)$ 和 $\phi(x, 1-k)$。它比上正常形 N 范数要瘦，但比 Sugeno 型 N 范数更胖。其中：

$N(x, k)=(1-x^n)^{1/n}, n=-1/\log_2 k$
$\phi(x, k)=x^n, 1-\phi(x, k)=1-x^n, \phi^{-1}(x, k)=x^{1/n}$

从前面的讨论我们已经知道，$\phi(x, k)$ 簇中的复合运算和逆运算仍然在 $\phi(x, k)$ 簇内，所以 $N(x, k)$ 是 N 范数完整簇。