```
M(\boldsymbol{x}, \boldsymbol{y})=<M(x_{1}, y_{1}), M(x_{2}, y_{2})>
C(\boldsymbol{x}, \boldsymbol{y})=<C(x_{1}, y_{1}), C(x_{2}, y_{2})>
```

其中，\(\boldsymbol{x}=<x_{1}, x_{2}>; \boldsymbol{y}=<y_{1}, y_{2}>; N(\boldsymbol{x}) 、 T(\boldsymbol{x}, \boldsymbol{y}) 、 S(\boldsymbol{x}, \boldsymbol{y}) 、 I(\boldsymbol{x}, \boldsymbol{y}) 、 Q(\boldsymbol{x}, \boldsymbol{y}) 、 M(\boldsymbol{x}, \boldsymbol{y})\) 和 \(C(\boldsymbol{x}, \boldsymbol{y})\) 是线序逻辑学中的逻辑运算模型(零级或一级)。上述思想可以推广到任意 \(n\) 级， \(n \geqslant 2\)。

# 定义 5.6.5 \(n\) 级不确定性。真值的不确定性是 \(n\) 级的，iff 命题的真值 \(\boldsymbol{x}=<x_{1}, x_{2}, \cdots, x_{n}>, n \geqslant 2, x_{i} \in[0,1]\)。

例如，对于一个柔性命题 \(p\) ，如果我们无法准确获得它的逻辑真值，但可以得到 \(p\) 为真的期望真值 \(x_{2}\)，且 \(x_{1}\) 是 \(x_{2}\) 的下限，\(x_{3}\) 是 \(x_{2}\) 的上限，则 \(p\) 的真值状态需要用三元数组 \(\boldsymbol{x}=<x_{1}, x_{2}, x_{3}>\) 表示。反之，\(\neg p\) 的真值状态需要用三元数组 \(N(\boldsymbol{x})=<N(x_{3}), N(x_{2}), N(x_{1})>\) 表示。

在 \(n\) 级不确定性推理中，命题的真值需要用一个 \(n\) 元数组来表示，但命题的真值在本质上仍然是一维的，它的每个分量都只是从不同的角度来描述这个一维命题，所以它的多维性只是形式上的虚假现象。我们称这种虚假多维真值空间的逻辑学为伪偏序逻辑学或伪多维逻辑学。

在 \(n\) 级不确定性推理中，由于 \(n\) 个分量是相互关联的，所以它的逻辑运算模型(零级或一级)服从以下拓扑规则：

```
N(\boldsymbol{x})=<N(x_{n}), N(x_{n-1}), \cdots, N(x_{1})>
T(\boldsymbol{x}, \boldsymbol{y})=<T(x_{1}, y_{1}), T(x_{2}, y_{2}), \cdots, T(x_{n}, y_{n})>
S(\boldsymbol{x}, \boldsymbol{y})=<S(x_{1}, y_{1}), S(x_{2}, y_{2}), \cdots, S(x_{n}, y_{n})>
I(\boldsymbol{x}, \boldsymbol{y})=<I(x_{1}, y_{1}), I(x_{2}, y_{2}), \cdots, I(x_{n}, y_{n})>
Q(\boldsymbol{x}, \boldsymbol{y})=<Q(x_{1}, y_{1}), Q(x_{2}, y_{2}), \cdots, Q(x_{n}, y_{n})>
M(\boldsymbol{x}, \boldsymbol{y})=<M(x_{1}, y_{1}), M(x_{2}, y_{2}), \cdots, M(x_{n}, y_{n})>
C(\boldsymbol{x}, \boldsymbol{y})=<C(x_{1}, y_{1}), C(x_{2}, y_{2}), \cdots, C(x_{n}, y_{n})>
```

其中，\(\boldsymbol{x}=<x_{1}, x_{2}, \cdots, x_{n}>, \boldsymbol{y}=<y_{1}, y_{2}, \cdots, y_{n}>; N(\boldsymbol{x}) 、 T(\boldsymbol{x}, \boldsymbol{y}) 、 S(\boldsymbol{x}, \boldsymbol{y}) 、 I(\boldsymbol{x}, \boldsymbol{y}) 、 Q(\boldsymbol{x}, \boldsymbol{y}) 、 M(\boldsymbol{x}, \boldsymbol{y})\) 和 \(C(\boldsymbol{x}, \boldsymbol{y})\) 是线序逻辑学中的逻辑运算模型(零级或一级)。

泛逻辑学还研究另外一类逻辑学，它的真值域不仅涉及一个有序空间 \([0,1]^n\)，还涉及另外一些与 \([0,1]^n\) 不连通的特殊空间，如无定义状态 \(\perp\) 形成的空间，用 \(\{\perp\}\) 表示；还有某种真值附加特性 \(\alpha\), 用 \(x<\alpha\) 表示。本书特称由有序空间 \([0,1]^n\) 和另一些不连通的特殊空间组成的空间为超序空间，以超序空间为真值域的逻辑学为超序逻辑学，下面举一些简单的实例来说明超序逻辑学中的拓扑规则。

# 5.6.3 包含无定义状态的泛逻辑运算模型

在有些情况下，命题的真值需要考虑无定义状态 \(\perp\) ，\(\perp\) 的物理意义是在正常的