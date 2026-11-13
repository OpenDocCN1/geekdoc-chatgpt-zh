```
17 |     elif c3 is None:
18 |         if c1 is not None and c2 is not None:    # 存在 2 个交点的情况
19 |             if c1.distance(c2) ! = 0.0:
20 |                 return Segment(c1, c2)
21 |         elif c1 is not None and c2 is not None and c3 is not None:   # 存在 3 个交点的情况
22 |             return Segment(c1, c3) if c1.isIdentical(c2) else Segment(c1, c2)
23 |     return None    # 如果都不满足，返回 None
```

以上函数是空间三角形和空间平面求交的通用函数，输入分别为 Triangle 和 Plane 对象，对交点个数，我们根据以上讨论枚举了各种可能存在的情况。必须注意的是，在设计算法时切勿抱任何侥幸心理，特别是涉及算法原理的部分，否则算法在后期测试或今后实际运行中终会碰到问题。

考虑到在三维打印中，一般以 Z 轴作为模型生长方向，即截平面总是垂直于 Z 轴，我们可以再构建一个特化函数，专门用于求取空间三角形和 Z 平面的交线，代码如下：

| GeomAlgo.py | intersectTriangleZPlane:空间三角形和 z 平面求交函数 | 4 - 4 |
|---|---|---|
| 1 | ...... | # 略过前面的代码 |
| 2 | def intersectTriangleZPlane( triangle, z ): | # 输入为 Triangle 对象和 z 高度 |
| 3 | if triangle.zMinPnt( ).z > z: | # 预处理，如果三角形最低点高于 |
| 4 | return None | # 平面或最高点低于平面，返回 |
| 5 | if triangle.zMaxPnt( ).z < z: | # None，即无交线 |
| 6 | return None | |
| 7 | return intersectTrianglePlane( triangle, Plane.zPlane( z ) ) | # 否则返回求交结果 |

在 intersectTriangleZPlane 函数中，我们只需输入一个 Triangle 对象和 Z 平面高度（实数）即可。为了尽量减少计算量，在真正求交之前，我们要先对三角形和平面的相对位置进行判断，如果三角形最低点高于 Z 平面，那么返回 None；同理如果三角形最高点低于 Z 平面，同样返回 None。在排除这两种情况后，再调用空间三角形和平面求交的一般函数。

在建立任意空间三角形和任意 Z 平面求交函数之后，下一节我们将在不考虑算法效率的情况下进一步讨论如何使用该函数对 STL 模型进行截交计算。

# 4.3 STL 模型截交计算

当我们用一组平行平面去截 STL 模型的时候，在每张平面上，我们都将得到一系列截交线段。为了保存这些线段，需要建立一种数据结构，且这种结构能区分不同平面上的截交线段，我们把这种数据结构叫作 Layer(层)。显然，Layer 应该包含当前层高属性(z)，以及用来保存截交线段的线段列表(segments)。我们把 Layer 写在 Layer.py 文件中，具体代码如下：