SptRegion 类的实现代码（参见代码 10-1），在该类中，我们同样也设置了一个名为 xyGap 属性，它们的意义是相同的。

根据以上分析，支撑轮廓的修正流程如下：

+   (1) 对模型轮廓向外偏置，偏置距离为 xyGap，得到一条（或多条）偏置轮廓，如图 10. 17(b) 所示；

+   (2) 对支撑轮廓和以上得到的偏置轮廓执行布尔减操作，生成的差轮廓即为所求的新支撑轮廓，如图 10. 17(c) 所示。

以上我们使用先偏置、后布尔减的方法对支撑轮廓进行了修正，有效避免了模型轮廓和支撑轮廓相交情况的发生，彻底消除了切片支撑点影响范围矩形假设潜在的副作用。下面我们给出 genRawSptRegions 函数的升级版本——genSptRegions，在这个函数命名时，我们去掉了“raw”标识，它的具体实现如下：

| FindSptRegion.py | 寻找支撑区域类：实现初始支撑区域修正函数，实现类的核心函数 | 10 - 7 |
|---|---|---|
| 1 | ...... | # 导入相关库 |
| 2 | class FindSptRegion: | |
| 3 | ...... | # 忽略类中其他代码 |
| 4 | def genSptRegions(self, pts, layer): | # 定义支撑区域生成函数 |
| 5 | rawRegions = self.genRawSptRegions(pts) | # 根据支撑点生成初始支撑区域 |
| 6 | clipper, ca = Pyclipper(), ClipperAdaptor() | # 定义裁剪及适配器对象 |
| 7 | contours = ca.offset(layer.contours, self.xyGap) | # 生成模型轮廓的偏置轮廓 |
| 8 | clipper.AddPaths(ca.toPaths(rawRegions), PT_SUBJECT) | # 裁剪对象，初始支撑区域 |
| 9 | clipper.AddPaths(ca.toPaths(contours), PT_CLIP) | # 裁剪对象，偏置轮廓 |
| 10 | sln = clipper.Execute(CT_DIFFERENCE) | # 执行布尔减操作 |
| 11 | return ca.toPolys(sln, layer.z) | # 返回修正后的支撑区域 |
| 12 | def execute(self): | # 实现类的核心流程控制函数 |
| 13 | gridDic = self.calcModelSptPoints() | # 生成模型支撑点 |
| 14 | for layer in self.layers: | # 遍历模型切片数据 |
| 15 | layer.sptContours = [] | # 在每个切片上定义支撑轮廓序列 |
| 16 | pts = self.calcLayerSptPoints(gridDic, layer.z) | # 计算切片支撑点 |
| 17 | if len(pts) > 0: | # 如果支撑点数不为 0, |
| 18 | layer.sptContours = self.genSptRegions(pts, layer) | # 则生成支撑轮廓 |
| 19 | def findSptRegion(stlModel, layers, gridSize, crAngle, xyGap = 1): | # 定义支撑生成全局函数 |
| 20 | FindSptRegion(stlModel, layers, gridSize, crAngle, xyGap).execute() | |

从以上代码中，我们可以看到，genSptRegions 和 genRawSptRegions 两个函数都有一个名为 pts 的参数，表示切片支撑点，但前者比后者多了一个名为 layer 的参数，这个参数表示三维模型在某一层上的切片轮廓数据（参见 SliceAlgo 模块中的 slice_topo 函数）。根据之前的分析，layer 参数的作用是显而易见的，即用于修正 genRawSptRegions 函数得到初始支撑区域。和 genRawSptRegions 一样，genSptRegions 函数的实现同样非常简单，它们的主体都是布尔运算。在 genSptRegions 函数中，我们首先调用了 genRawSptRegions 函数生成了截平面上的初始支撑区域，然后对 layer 中的模型轮廓做偏置得到偏置轮廓，最后使用