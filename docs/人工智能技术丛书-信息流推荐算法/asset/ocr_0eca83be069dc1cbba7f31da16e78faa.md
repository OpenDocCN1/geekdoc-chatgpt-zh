![](bbox=[0.13, 0.08, 0.87, 0.36])

图4-17 列式计算与行式计算的差异

```
linear_{log(x)} = \begin{cases} -log(-x) - 1 & x < -1 \\ x & -1 \leqslant x \leqslant 1 \\ log(x) + 1 & x > 1 \end{cases} (4-17)
```

linear_{log(x)} 函数可以将Float32的数值转换到一个合适的范围，保持在Float16的边界之内，因此COLD将它作为特征输入后的第一层处理逻辑来约束模型参数的范围。在落地实践时，对比保持参数精度为Float32的情况，发现linear_{log(x)} 函数对模型的训练效果并没有负向影响，而CUDA Kernel（核）的运行性能却因为使用了Float16有了显著提升。同时为了优化Kernel的启动，在介绍COLD的论文中又提到了配合使用MPS（Multi-Process Service）来解决Kernel启动的系统开销，在这两项工程优化措施的叠加助力下，最终的每秒查询率（Queries Per Second, QPS）有了接近两倍的提升。

# 3.COLD 的业务效果

如图4-18所示，从离线效果的对比来看，无论是在GAUC上还是Recall的指标上，COLD都明显优于传统双塔模型，同时可以达到接近复杂的精排模型DIEN的效果。

| 算法 | GAUC | Recall |
|---|---|---|
| Vector-Product based DNN Model | 0.6232 | 88% |
| COLD | 0.6391 | 96% |
| DIEN | 0.6511 | 100% |

图4-18 COLD算法离线效果

综上所述，COLD并没有使用独创性的模型结构以及很复杂的工程优化方法，但它的创新点在于提供了一种非常灵活的建模方法，并且同时从算法和工程的双重视角来实现模型表达能力和算力消耗的平衡，因此也为业界提供了一种更加宽阔的技术视野。