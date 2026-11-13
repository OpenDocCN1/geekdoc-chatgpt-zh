```
vtkAdaptor.drawPdSrc( vtkStlReader ).GetProperty( ).SetOpacity( 0.5 )     # 显示模型
stlModel = StlModel( )                                      # 建立 StlModel 对象
stlModel.extractFromVtkStlReader( vtkStlReader )            # 从 VTK 加载 STL 模型
layers = intersectStl_brutal( stlModel, 10.0 )              # 暴力切片，层高为 10.0 毫米
for layer in layers:                                        # 在 VTK 中显示所有截交线段
    for seg in layer.segments:
        segActor = vtkAdaptor.drawSegment( seg )
        segActor.GetProperty( ).SetLineWidth( 2 )
vtkAdaptor.display( )
```

以上代码运行结果如图 4.5 所示，图中显示的一系列黑线就是所要求的截交线段。这些线段以列表的形式组织，保存在每层的 Layer 对象中，在各层上首尾相接，围成一系列封闭轮廓线，理论上，这些轮廓线就是截平面和 STL 模型的交线。为了让截交线看得更清楚，我们在测试 main 函数中将模型的透明度设置成 0.5，截交线段的宽度设置为 2。

![](bbox=[0.18, 0.38, 0.84, 0.69])

# 图 4.5 截平面和 STL 模型截交线(暴力法)

在本例中，我们使用了较为简单的模型，这个模型包含 3928 个三角面片，Z 向高度约为 100 毫米，在层高设置为 10 毫米的情况下，切片层数约为 10 层。也就是说，在每一层上，截平面要和 3928 个三角形求交；若考虑所有层，那么平面和三角形求交总次数为 39280 次，这仅仅是层高为 10 毫米的情况下。如果考虑实际三维打印过程，将层高设置为 0.1 毫米，那么平面和三角形求交总次数将是 3928000 次，这将消耗大量的时间。对那些包含数十万、上百万三角面片的 STL 模型(很常见)，求交的计算量更为惊人！我们将在下一章详细讲解如何优化算法从而降低截交计算量。

如图 4.5 所示，对某一特定层，从表面上看，这些截交线段堆成一系列封闭的轮廓线，但实际上，这些轮廓线中的线段是无序排放的，这和三角面片在 STL 模型中的无序存储特性是对应的。我们将在下一节介绍如何将这些无序的线段整理成一系列封闭的轮廓线。