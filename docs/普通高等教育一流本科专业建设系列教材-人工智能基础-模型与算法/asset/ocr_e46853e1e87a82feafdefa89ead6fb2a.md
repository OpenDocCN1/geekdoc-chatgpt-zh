\(\mu_O(u)=\left\{\begin{array}{ll} 0, & 0 \leqslant u \leqslant 50 \\ \left[1+\left(\frac{5}{u-50}\right)^2\right]^{-1}, & 50<u \leqslant 200 \end{array}\right.\)
\(\mu_Y(u)=\left\{\begin{array}{ll} 1, & 0 \leqslant u \leqslant 25 \\ \left[1+\left(\frac{u-25}{5}\right)^2\right]^{-1}, & 25<u \leqslant 200 \end{array}\right.\)

采用 Zadeh 表示法，“年老” \( O \) 与 “年轻” \( Y \) 两个模糊集合可以表示为

\(O=\frac{\int_{50 \leqslant \mu \leqslant 200}\left[1+\left(\frac{5}{u-50}\right)^2\right]^{-1}}{u}\)
\(Y=\int_{0<\mu \leqslant 25}\frac{1}{u}+\frac{\int_{25<u \leqslant 200}\left[1+\left(\frac{u-25}{5}\right)^2\right]^{-1}}{u}\)

常见的隶属函数有正态分布、三角分布、梯形分布等。

# 2. 模糊集合的运算

因为模糊集合是经典集合的推广，所以经典集合的运算可以推广到模糊集合。但由于模糊集合有隶属函数这一概念，因此其具体运算规则需要重新定义。

+   (1) 模糊集合的包含关系

若 \(\mu_A(x) \geqslant \mu_B(x)\)，则称 \( A \) 包含 \( B \)，记作 \( A \supseteq B \)。

+   (2) 模糊集合的相等关系

若 \(\mu_A(x)=\mu_B(x)\)，则称 \( A \) 与 \( B \) 相等，记作 \( A=B \)。

+   (3) 模糊集合的交并补运算

设 \( A \)、\( B \) 为论域中的两个模糊集合，\(\mu_A(x)\)、\(\mu_B(x)\) 为对应的隶属函数。

+   1) 交运算（intersection）记作 \( A \cap B \)：

\(\mu_{A \cap B}(x)=\min\{\mu_A(x),\mu_B(x)\}=\mu_A(x) \wedge \mu_B(x)\) (5.72)

+   2) 并运算（union）记作 \( A \cup B \)：

\(\mu_{A \cup B}(x)=\max\{\mu_A(x),\mu_B(x)\}=\mu_A(x) \vee \mu_B(x)\) (5.73)

+   3) 补运算（complement）记作 \(\overline{A}\)：

\(\mu_{\overline{A}}(x)=1-\mu_A(x)\) (5.74)

【例 5.9】 设论域 \(U=\{x_1,x_2,x_3,x_4\}\)，\( A \) 和 \( B \) 是论域上的两个模糊集合，已知：

\(A=\frac{0.3}{x_1}+\frac{0.5}{x_2}+\frac{0.7}{x_3}+\frac{0.4}{x_4}\)
\(B=\frac{0.5}{x_1}+\frac{1}{x_2}+\frac{0.8}{x_3}\)

求 \(\overline{A}\)、\(\overline{B}\)、\(A \cap B\)、\(A \cup B\)。

解

\(\overline{A}=\frac{0.7}{x_1}+\frac{0.5}{x_2}+\frac{0.3}{x_3}+\frac{0.6}{x_4}\)