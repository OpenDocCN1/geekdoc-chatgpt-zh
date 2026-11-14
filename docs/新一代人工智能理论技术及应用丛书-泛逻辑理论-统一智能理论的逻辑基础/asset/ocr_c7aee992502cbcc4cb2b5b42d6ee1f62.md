# 4. 泛蕴含命题连接词 $\rightarrow_{h,k}$ 的运算模型

$$I(x, y, h, k)=(\min(1+0^{nm}, 1-x^{nm}+y^{nm}))^{1/nm}$$
其中，$k=2^{-1/n}, n\in\mathbf{R}^{+}$ 或 $n=-1/\log_2 k, k\in[0, 1]$ ; $m=(3-4h)/(4h(1-h)), h\in[0, 1]$ 或 $h=((1+m)-((1+m)^2-3m)^{1/2})/(2m), m\in\mathbf{R}$。

+   它的四个特殊算子是：
+   * 最小蕴含 Zadeh 蕴含 \( I(x, y, 1, k)=\mathbf{I}_3=\text{ite}\{1|x\leqslant y; y\} \)
+   * 中极蕴含 概率蕴含 \( I(x, y, 0.75, k)=\mathbf{I}_2=\min(1, y/x) \)
+   * 中心蕴含 有界蕴含 \( I(x, y, 0.5, 0.5)=\mathbf{I}_1=\min(1, 1-x+y) \)
+   * 最大蕴含 突变蕴含 \( I(x, y, 0, k)=\mathbf{I}_0=\text{ite}\{y|x=1; 1\} \)

# 5. 泛等价命题连接词 $\leftrightarrow_{h,k}$ 的运算模型

$$Q(x, y, h, k)=(1\pm|x^{nm}-y^{nm}|)^{1/nm}$$
其中，\( h>0.75 \) 为 \(+\)，否则为 \(-\)。

+   它的四个特殊算子是：
+   * 最小等价 Zadeh 等价 \( Q(x, y, 1, k)=\mathbf{Q}_3=\text{ite}\{1|x=y; \min(x, y)\} \)
+   * 中极等价 概率等价 \( Q(x, y, 0.75, k)=\mathbf{Q}_2=\min(x/y, y/x) \)
+   * 中心等价 有界等价 \( Q(x, y, 0.5, 0.5)=\mathbf{Q}_1=1-|x-y| \)
+   * 最大等价 突变等价 \( Q(x, y, 0, k)=\mathbf{Q}_0=\text{ite}\{x|y=1; y|x=1; 1\} \)

其中，\( k=2^{-1/n}, n\in\mathbf{R}^{+}\) 或 \( n=-1/\log_2 k, k\in[0, 1]\) ; \( m=(3-4h)/(4h(1-h)), h\in[0, 1]\) 或 \( h=[(1+m)-((1+m)^2-3m)^{1/2}]/(2m), m\in\mathbf{R}\)。

# 6. 泛平均连接词 $\oplus_{h,k}$ 的运算模型

# 1) 二元模型

$$M(x,y,h,k)=(1-(((1-x)^n m+(1-y)^n m)/2)^{1/m})^{1/nm}$$

+   它的四个特殊算子是：
+   * 最大平均 Zadeh 平均 \( M(x, y, 1, k)=\mathbf{M}_3=\max(x, y)=\mathbf{S}_3 \)
+   * 中极平均 概率平均 \( M(x, y, 0.75, k)=\mathbf{M}\Phi_{2k}=(1-((1-x)^n(1-y)^n)^{1/2})^{1/n} \)
+   * 中心平均 有界平均 \( M(x, y, 0.5, 0.5)=\mathbf{M}_1=(x+y)/2 \)
+   * 最大平均 突变平均 \( M(x, y, 0, k)=\mathbf{M}_0=\min(x, y)=\mathbf{T}_3 \)

# 2) 多元模型

$$M(x_1, x_2, \cdots, x_l, h, k)=(1-(((1-x_1)^n m+(1-x_2)^n m+\cdots+(1-x_l)^n m)/l)^{1/m})^{1/nm}$$

+   它的四个特殊算子是：
+   * 最大平均 Zadeh 平均算子 \( M(x_1, x_2, \cdots, x_l, 1, k)=\mathbf{M}_3=\max(x_1, x_2, \cdots, x_l) \)
+   * 中极平均 概率平均算子 \( M(x_1, x_2, \cdots, x_l, 0.75, k)=\mathbf{M}\Phi_{2k}=(1-(((1-x_1)^n(1-x_2)^n)\cdots(1-x_l)^n)^{1/l})^{1/n} \) (内容部分截断)