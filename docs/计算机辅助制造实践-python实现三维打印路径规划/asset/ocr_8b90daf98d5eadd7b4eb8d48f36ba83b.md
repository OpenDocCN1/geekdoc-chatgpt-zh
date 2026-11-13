# 7.5 算法测试比较

这一节我们将对之前封装的基于拓扑模型的切片轮廓计算模块，即对 TopoSlicer 模块进行实例测试和比较。在此之前，我们先来看一下它的比较对象。在前面 3 个章节中，我们封装了 3 种模型截交算法和 3 种截交线段拼接算法，它们分别是暴力截交法、扫描平面截交法、层高匹配截交法，以及暴力拼接法、字典序排序拼接法、字典查询拼接法。

根据我们之前的测试，其中扫描平面截交法和字典查询拼接法的效率最高，因此，我们在这里将使用这两种算法的组合和本章的 TopoSlicer 模块进行对比，只有这样的比较才是有意义的。

为了方便起见，我们先对这两种算法的组合进行封装，在 SliceAlgo 模块中新建一个类似 slice_topo 的全局函数，函数名为 slice_combine，它的具体实现代码如下：

| SliceAlgo.py | slice_combine:添加扫描平面截交、字典查询拼接组合切片算法 | 7-9 |
|---|---|---|
| 1 | from TopoSlicer import * | # 导入 TopoSlicer 模块 |
| 2 | ...... | # 忽略 SliceAlgo 其他导入模块及代码 |
| 3 | def slice_combine( stlModel, layerThk ): | # 定义组合切片函数 |
| 4 | layers = intersectStl_sweep( stlModel, layerThk ) | # 首先扫描平面截交 |
| 5 | for layer in layers : | |
| 6 | layer.contours = linkSegs_dlook( layer.segments ) | # 然后字典查询拼接 |
| 7 | layer.segments.clear( ) | # 清除 layer 中的线段，重要 |
| 8 | adjustPolygonDirs( layer.contours ) | # 调整轮廓方向，“外逆内顺” |
| 9 | return layers | |

在以上的 slice_combine 函数中，我们首先调用了 intersectStl_sweep 函数生成截交线段，并把这些线段数据存放在 layers 中，然后我们通过 linkSegs_dlook 函数对每层中的线段进行拼接，最后得到每层的轮廓数据。

在这个过程中，我们进行了一项非常重要的操作，即在每层的线段被拼接后，清空该层上的所有截交线段。这样做是为了节省计算机内存空间，因为线段被拼接后，再在内存中保留这些线段已经没有意义了。如果不删除这些线段，对于一些大型模型，在切片层数较大时，运行 slice_combine 函数很有可能导致内存溢出。同时，我们也对得到的切片轮廓方向进行了修正，使其满足“外逆内顺”的规定。

接下来我们回到正题，对 slice_topo(拓扑切片) 和 slice_combine(组合切片) 两个函数进行比较测试，测试主机 CPU 为 Intel Core i7-8700@ 3.20GHz。测试模型依然从如图 5.8 所示的四个 STL 模型中选择，这里我们挑选了其中两个模型，分别为 bunny 和 vase 模型，它们包含的面片个数分别约为 27 万和 119 万。

针对这两个模型，我们分别设置了 1.0 毫米、0.5 毫米、0.2 毫米、0.1 毫米以及 0.05 毫米这 5 种切片厚度。我们将测试代码写在一个临时 Test_topoSlicer.py 文件中，具体如下：