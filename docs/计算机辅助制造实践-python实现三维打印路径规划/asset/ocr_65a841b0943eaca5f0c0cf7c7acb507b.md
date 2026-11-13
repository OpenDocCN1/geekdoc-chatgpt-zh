系列表示打印区域边界的平面轮廓，这些轮廓之间可能存在包含关系，但是它们的方向是杂乱不定的。按照我们之前的约定，表示外边界的轮廓应是逆时针方向，表示内边界的轮廓是顺时针方向。如果边界轮廓方向不定，在使用 Clipper 库的偏置方法时可能发生难以预料的错误。所幸，在 slice_topo 函数的实现代码中，我们已经使用 adjustPolygonDir 函数对每层切片轮廓的方向进行了调整（参见代码 7-7 和 7-8）。

在 Test_saveSlc.py 中，最后我们通过调用 SliceAlgo 模块中的 writeSlcFile 函数将上述已经调整轮廓方向的切片列表写入文件，得到切片文件，切片文件的文件名包含原 STL 模型名及切片层厚信息，扩展名为“.slc”，如“monk at 2.0mm.slc”。通过调整以上代码中的 layerThk 变量值，我们可以得到 monk 模型的若干切片文件，这里我们设置的切片层厚分别是 2.0 毫米、1.0 毫米、0.5 毫米以及 0.2 毫米。

# 8.6.2 路径生成测试

在得到了切片文件之后，接下来我们考虑使用 GenCpPath 类针对这些切片文件生成轮廓平行填充路径。实现这部分功能的具体测试代码(写在 Test_genCpPath.py 文件中)如下：

| Test_genCpPath.py | 读取 SLC 文件，生成每层切片的轮廓填充路径 | 8-12 |
|---|---|---|
| 1 | from GenCpPath import * | # 导入 GenCpPath 库 |
| 2 | interval = 0.4 | # 轮廓路径间距 |
| 3 | shellThk = 2.0 | # 外壳厚度，即轮廓路径总宽度 |
| 4 | pathses = [] | # 存储各层已连接的路径 |
| 5 | layers = readSlcFile("D:\\monk at 1.0mm.slc") | # 读取 SLC 文件 |
| 6 | start = time.clock() | # 计时开始 |
| 7 | for i in range(len(layers)): | # 逐层生成轮廓填充路径 |
| 8 | print('cp, layer: %d / %d' % (i+1, len(layers))) | |
| 9 | paths = genCpPath(layer[i].contours, interval, shellThk) | |
| 10 | pathses.append(paths) | # 存储路径到 pathses |
| 11 | end = time.clock() | # 计时结束 |
| 12 | print("GenCpPath time : %f CPU seconds" % (end - start)) | # 打印路径生成时间 |
| 13 | va = VtkAdaptor() | # 显示，使用 VTK 库 |
| 14 | for layer in layers: | # 显示每层打印区域边界，黑色 |
| 15 | for contour in layer.contours: | |
| 16 | va.drawPolyline(contour).GetProperty().SetColor(0, 0, 0) | |
| 17 | for paths in pathses: | # 显示每层路径，红色 |
| 18 | for path in paths: | |
| 19 | va.drawPolyline(path).GetProperty().SetColor(1, 0, 0) | |
| 20 | va.display() | |

在以上代码中，变量 interval 表示轮廓路径间距，shellThk 表示外壳厚度，即轮廓填充路径总宽度。这里我们设置路径间距为 0.4 毫米，外壳厚度为 2.0 毫米，根据我们在上一节介绍的连续偏置原理，GenCpPath 类将对每层轮廓偏置 5 次。首先，我们使用 SliceAlgo 模块中的 readSlcFile 函数读取之前保存的 SLC 文件，得到切片列表 layers。然后根据