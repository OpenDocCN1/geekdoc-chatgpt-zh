
| GenDpPath.py | 对 GenDpPath 类进行扩展和升级, 添加 generateEx 函数 | 10 - 9 |
|---|---|---|
| 1 ...... | | # 导入相关库 |
| 2 class GenDpPath: | | |
| 3 ...... | | # 忽略类中其他代码 |
| 4 def generateEx( self, ys = None, center = None ): | | # 定义 generateEx 函数 |
| 5 rotPolys = GeomAlgo.rotatePolygons( self.polygons, -self.angle, center ) | | # 旋转多边形 |
| 6 if ys is None: | | # 如果参数 ys 为 None |
| 7 ys = self.genScanYs( rotPolys ) | | # 则根据多边形生成独立的 ys |
| 8 self.splitPolys = splitRegion( rotPolys ) | | # 分区生成单连通区域 |
| 9 paths = [ ] | | # 定义路径序列 |
| 10 for poly in self.splitPolys: | | # 对每个单连通区域生成路径 |
| 11 segs = genHatches( [ poly ], ys ) | | # 生成平行填充线段 |
| 12 if len( segs ) > 0: | | |
| 13 path = self.linkLocalHatches( segs ) | | # 连接单连通区域内填充线段 |
| 14 paths.append( path ) | | |
| 15 return GeomAlgo.rotatePolygons( paths, self.angle, center ) | | # 反向旋转并返回路径 |
| 16 def genDpPathEx( polygons, interval, angle, ys = None, center = None ): # generateEx 接口函数 | | |
| 17 return GenDpPath( polygons, interval, angle ).generateEx( ys, center ) | | |

我们可以结合 generate 函数的实现代码来看以上 generateEx 函数的实现代码。generateEx 函数有两个输入参数, 分别为 ys 和 center。其中 ys 就是我们之前说的用来控制当前切片层上平行线位置和间距的实数序列, 该参数由外部生成和输入, 可确保对所有切片来说, ys 是唯一的。第二个参数 center 表示旋转中心点, 下面我们着重分析一下在 generateEx 函数中设置旋转中心点的原因。

我们知道, 在 generate 函数中, 为了适应平行路径不同填充角度的需求, 我们根据相对运动的原理需要对表示打印区域的多边形轮廓进行旋转, 在完成填充路径计算后再将路径旋转相反的角度, 如图 9.7 所示。在 generate 函数中, 因为 ys 根据旋转后的多边形轮廓产生, 因此理论上 ys 对应的平行线总能覆盖旋转后的多边形区域, 且旋转中心点是可以任意选择的(简单起见, 在 generate 函数中, 我们选择了平面坐标系原点为旋转中心点)。但是对 generateEx 函数来说, 因为 ys 在多边形轮廓旋转之前已经生成, 理论上, ys 对应的平行线和旋转后的多边形并无关系, 为了达到填充的目的, 我们有必要通过外部措施来保证 ys 对应的平行线能够覆盖旋转后的多边形区域, 而且是所有切片层上的旋转多边形区域。我们采取的外部措施包括两点: 一是根据模型沿 Z 方向的外接圆柱来生成 ys(类似原理图可参见图 9.9), 二是以外接圆柱轴线沿 Z 轴投影点为旋转中心点。通过这两个措施, 在任意切片层上, 无论多边形轮廓如何旋转, 我们总能保证 ys 对应的平行线能够覆盖多边形区域。

我们可以看到, generateEx 函数中的 ys 和 center 参数都有默认值, 而且它们的默认值都为 None, 这样做完全是为了“向下兼容”, 即如果不对 generateEx 输入任何参数, 那么 generateEx 函数就将退化为 generate 函数, 即此时两者功能完全一致。

类似于 generate 函数, 在 GenDpPath 模块中, 我们也为 generateEx 写了一个对应的全局接口函数 genDpPathEx。该函数包含 5 个输入参数，其中 polygons 表示打印区域的多边形轮廓序列，interval 表示平行路径间距，angle 表示填充角度，ys 表示平行线位置和间距的序列，center 表示旋转中心点，ys 和 center 的默认值同样都为 None。这 5 个参数中的 interval 和 ys 两个参数是有点重复的，这其实是无奈之举，因为 genDpPathEx 函数需要和 genDpPath 函数兼容。在调用 genDpPathEx 函数时，如果指定了 ys 参数，那么 interval 参数将不起作用，此时可对 interval 设置任意值（比如 0）。

### 10.3.2 支撑路径生成实现

在完成 GenDpPath 模块的扩展和升级之后，基于该模块，下面我们直接给出针对支撑区域的填充路径生成代码。我们把这部分代码写在 GenSptPath 模块中，包括一个全局函数 genSptPath 和一个枚举类 SptPathType，具体实现如下：

```python
# 导入相关库
class SptFillType( Enum ):
    # 填充路径形式枚举类
    line = 1    # 平行同向填充
    cross = 2   # 平行交叉填充
# 定义支撑路径生成函数
def genSptPath(stlModel, layers, pathInvl, gridSize, crAngle, fillType, fillAngle = 0, xyGap = 1):
    findSptRegion(stlModel, layers, gridSize, crAngle, xyGap)    # 生成支撑区域
    xMin, xMax, yMin, yMax, zMin, zMax = stlModel.getBounds()    # 计算模型在各个方向极限
    center = Point3D((xMin + xMax)/2, (yMin + yMax)/2)    # 模型在平面上中心点
    corner = Point3D(xMax, yMax)    # 模型包络在平面上角点
    radius = center.distance(corner)    # 计算模型外接圆柱半径
    ys, y = [], center.y - radius
    while y <= center.y + radius:    # 生成实数序列 ys
        y += pathInvl
        ys.append(y)
    for i, layer in enumerate(layers):    # 遍历每层切片
        angle = fillAngle    # 填充角度
        if fillType == SptFillType.cross and i % 2 == 1:    # 如果是平行交叉填充，
            angle = fillAngle + math.pi/2    # 计算交叉填充角度
        layer.sptDpPaths = genDpPathEx(layer.sptContours, 0, angle, ys, center)    # 平行填充路径
        layer.sptCpPaths = layer.sptContours    # 外周轮廓路径
```

我们首先来看一下 SptFillType 这个枚举类。它枚举了目前我们能够实现的两种支撑路径形式，一种为 line，表示上下切片层的平行线同向填充，如图 10.5（c）；另一种为 cross，表示上下切片层的平行线交叉填充，如图 10.5（a）。

下面我们再来看下 genSptPath 函数，该函数共有 8 个输入参数，其中 stlModel 表示三维模型，layers 为对应的切片数据，pathInvl 为相邻平行路径之间的间距，gridSize 为 XY 平面上的预设网格尺寸，crAngle 为危险面片临界角，fillType 为 SptFillType 枚举类型的支撑路径填充形式，fillAngle 表示填充角度（默认值为 0 度），xyGap 为支撑到模型的横向间隙（默认值为 1 毫米）。在这些参数中，其中 stlModel，layers，gridSize，crAngle，xyGap 这 5 个参数我们比较熟悉，它们和支撑区域生成相关，将直接被 findSptRegion 函数所用。

在以上最新 GenDpPath 模块的基础上，genSptPath 函数的实现变得简单，在流程上主要可以分为 3 个步骤。

- （1）调用 findSptRegion 函数生成了各层切片上的支撑区域轮廓，这些支撑轮廓被保存在 layers 序列中。
- （2）计算模型在 XY 平面上的中心点 center 和全局唯一的用于指示平行线位置和间距的实数序列 ys。
- （3）在前面两步获得的数据的基础上，调用 GenDpPath 模块中的 genDpPathEx 函数对每层切片上的支撑区域生成平行填充路径。

在 genSptPath 函数中，我们将得到的平行填充路径保存到了每层切片（layer）的 sptDpPaths 序列中，并且我们直接以平行填充路径外围的支撑区域轮廓（sptContours）为轮廓路径，保存到每层切片的 sptCpPaths 序列中。

注意，以上 sptDpPaths 和 sptCpPaths 两个序列（其实也包括 sptContours 序列）都是 Python 语言特有的动态属性，如果使用其他编程语言实现，应考虑提前在 Layer 类中定义相应属性。我们看到，genSptPath 函数的最终输出就是 sptDpPaths 和 sptCpPaths 两个路径序列，其中对 sptDpPaths 中的平行填充路径来说，它们已经具备了分区特性，且各个分区内的路径都已经被连接。

### 10.3.3 支撑路径生成测试

这一节我们对以上得到的支撑路径生成函数 genSptPath 做一个简单的测试，看它是否能够生成符合要求的支撑填充路径，包括同向填充和交叉填充。方便起见，我们直接将测试代码写在 GenSptPath 模块的 main 函数中，具体如下：

| GenSptPath.py | 在模块 main 函数中对 genSptPath 函数进行测试 | 10 - 11 |
| :--- | :--- | :--- |
| 1 | ...... | # 导入相关库，忽略其他代码 |
| 2 | if __name__ == '__main__': | |
| 3 | src, stlModel = vtk.vtkSTLReader(), StlModel() | |
| 4 | src.SetFileName("E:\\Models\\STL\\monk.stl") | # 读取 STL 模型文件 |
| 5 | stlModel.extractFromVtkStlReader(src) | # 提取 VTK 数据源 |
| 6 | layers = readSclFile("D:\\monk at 2.0mm.slc") | # 读取相应切片文件 |
| 7 | genSptPath(stlModel, layers, 2, 2, degToRad(60), SptFillType.line) | |
| 8 | va = VtkAdaptor() | # 以下调用 VTK 显示计算结果 |
| 9 | va.drawPdSrc(src).GetProperty().SetOpacity(0.1) | # 显示三维模型 |
| 10 | for layer in layers: | # 逐层显示填充路径 |
| 11 | for poly in layer.sptDpPaths: | # 显示平行填充路径 |
| 12 | if poly is not None: | |
| 13 | va.drawPolyline(poly).GetProperty().SetColor(1, 0, 0) | # 平行路径红色 |

```python
14    for poly in layer.sptCpPaths:                 # 显示轮廓路径
15    if poly is not None:
16        va.drawPolyline(poly).GetProperty().SetColor(0, 0, 1)  # 轮廓路径蓝色
17    va.display()
```

这里我们选择 monk 和 bunny 两个模型（参见图 5.8）作为支撑路径生成的测试模型，与之对应的切片文件切片厚度均为 2.0 毫米。对 monk 模型，在 genSptPath 函数中，我们设置的路径间距（pathInvl）为 2 毫米，网格尺寸（gridSize）为 2 毫米，危险面片临界角（crAngle）为 60 度，支撑路径填充形式（fillType）为同向填充（line），其他参数保持默认值不变。而对 bunny 模型，考虑到该模型在空间尺寸上较 monk 模型小些，因此我们为其设置的路径间距为 1 毫米；为了区别，设置的路径填充形式为交叉填充（cross）。当然，这些参数可以根据实际测试需求进行灵活调整。

在测试代码后半部分，我们对模型和支撑填充路径进行了显示，包括平行填充路径和外围的轮廓路径。

针对两个模型实际生成的支撑路径如图 10.21 所示。从图 10.21（a）和图 10.21（c）中我们可以看出，genSptPath 函数能够根据设置的参数在模型上生成完备的支撑路径，这其实主要得益于我们对 FindSptRegion 类的良好封装；从俯视图 10.21（b）和 10.21（c）中我们可以看出，无论是同向填充路径还是交叉填充路径，相邻上下切片层之间的平行路径都已严格对齐，符合我们对支撑路径的要求，当然这其实得益于我们对 GenDpPath 模块的扩展和改进。

最后我们也对 genSptPath 函数的运行效率做了简单的测试。这里我们使用的模型依然为 monk 模型，但为了贴合实际应用，选择的切片文件切片厚度为 0.2 毫米，对应的切片层数为 877 层（参见表 8.1）。

对 genSptPath 函数，我们设置的路径间距为 2 毫米，网格尺寸为 2 毫米，危险面片临界角为 60 度，支撑路径填充形式为同向填充。根据这些参数，在测试平台（CPU 为 Intel Core i7-8700@ 3.20GHz）上，genSptPath 函数的执行时间约为 30 秒钟。相对切片及模型内部路径的生成时间（参见表 7.1、表 8.1 和表 9.1），这样的执行时间是令人满意的，表明 genSptPath 具有较高的运行效率。需要强调的是，在本书中，所有路径生成算法都是用 Python 语言实现的，如果使用效率更加高的编程语言，在相同的测试条件及测试平台上，程序执行时间必然更短。

## 10.4 支撑量优化

针对一个给定的三维模型，我们已经能够为其识别支撑区域、生成支撑路径。支撑路径的总长度直接反映了打印过程中支撑材料的用量大小，而在打印完成后，这些支撑材料是需要被去除并舍弃的。因此，在保证支撑有效且强度足够的情况下，我们希望支撑材料用量（即支撑量）越少越好。在相同的支撑参数设置下（包括路径间距 pathInvl、危险面片临界角 crAngle、填充形式 fillType、填充角度 fillAngle、横向间距 xyGap 等），对同一模型同

![](img/c3ab49ac4eb80e7a0a66462a29869ca2_278_0.png)

![](img/c3ab49ac4eb80e7a0a66462a29869ca2_278_1.png)

![](img/c3ab49ac4eb80e7a0a66462a29869ca2_278_2.png)

![](img/c3ab49ac4eb80e7a0a66462a29869ca2_278_3.png)

图 10.21 monk 和 bunny 模型支撑路径生成测试

种工艺，显而易见，支撑量只和模型在工作台上的摆放角度有关。为了降低支撑量，本节我们将考虑使用一种智能优化算法对模型的摆放角度进行优化。

目前常用的智能优化算法有遗传算法、粒子群算法、免疫算法、模拟退火算法等，这些算法大都受自然界生物群体行为或物理现象的启发，依赖“随机—迭代—进化”过程，在解空间中搜索最优解。在这些算法中，粒子群算法原理相对简单易懂，代码实现相对容易，因此，在这一节，我们将采用粒子群算法对模型摆放角度进行优化。首先我们来介绍下粒子群算法的基本原理。

### 10.4.1 粒子群算法原理

粒子群算法( particle swarm optimization ,PSO) 最早由美国两位科学家 Eberhart 和 Kennedy 在 1995 年提出。它源于对鸟类集群捕食行为的研究，利用群体中个体对信息的共享使整个群体的运动在问题求解空间中产生从无序到有序的演化过程。和遗传算法类似，粒子群算法也是用适应度( fitness )来评价解的品质，从一群随机粒子( 随机解 )出发，通过迭代寻找最优解，如图 10.22 所示。在每次迭代中，每个粒子通过跟踪两个“极值”更新自己，一个是该粒子本身所找到的最优解，叫作个体极值；另一个是从整个群体中当前找到的最优解，叫作全局极值。相比于遗传算法，粒子群算法更为简单，易实现且仅有少量参数需要调整。粒子群算法通常被用于求解组合优化问题的最优解。

在粒子群算法中，每个粒子都有一个随机产生的初始位置和速度，并且有一个通过求解适应度函数得到的适应度来评价该粒子在当前位置上的品质。粒子群算法实现的基本步骤如下：

![](img/c3ab49ac4eb80e7a0a66462a29869ca2_279_0.png)

图 10.22 粒子群算法粒子演化收敛过程示例

- (1) 随机初始化 N 个粒子，包括每个粒子的位置 P_i 和速度 V_i，其中 i ∈ [1, N]；
- (2) 根据指定适应度函数计算每个粒子适应度；
- (3) 比较粒子群中每个粒子 P_i 和与其对应的个体极值 LP_i 的适应度，如果 P_i 优于 LP_i，则用 P_i 替换 LP_i；
- (4) 比较粒子群中每个粒子 P_i 和全局极值 GP 的适应度，如果 P_i 优于 GP，则用 P_i 替换 GP；
- (5) 根据下列进化公式对每个粒子速度进行操作并移动粒子，
  V_i(t + 1) = ω · V_i(t) + r_1(LP_i - P_i(t)) + r_2 · (GP - P_i(t)) （10-5）
  P_i(t + 1) = P_i(t) + V_i(t + 1) （10-6）
- (6) 如果停止条件满足，则跳出循环，否则跳至步骤（2）。

在以上步骤中，对每个粒子，位置、速度及适应度函数（fitness function）的定义是整个粒子群算法中最为基础的环节，特别是适应度函数。粒子的适应度是从某个角度衡量粒子品质的一种指标，而适应度函数就是计算粒子适应度的一种方法。正如我们在第 9 章处理最少跳刀填充方向的优化问题一样，对大多优化问题，我们都会去建立相应的目标函数。在粒子群算法中，多数情况下，我们会按照被求解问题本身的目标函数去定义粒子的适应度函数。
