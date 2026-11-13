件)，类名为 generate contour – parallel toolpath 的缩写，意思是生成轮廓平行路径。GenCpPath 类中连续偏置曲线生成部分的实现代码如下：

| GenCpPath.py | 偏置路径生成与连接:连续偏置路径生成 (offset) | 8-7 |
|---|---|---|
| 1 | ...... | # 导入相关库 |
| 2 | class GenCpPath: | # 定义 GenCpPath 类 |
| 3 | def __init__( self , boundaries , interval , shellThk ) : | # 输入：轮廓、偏置距离、填充带宽 |
| 4 | self.boundaries = boundaries | # 打印区域边界轮廓 |
| 5 | self.interval = interval | # 工艺参数：偏置距离(喷头直径) |
| 6 | self.shellThk = shellThk | # 工艺参数：填充带宽(外壳厚度) |
| 7 | self.arcTolerance = 0.005 | # 工艺参数：圆弧精度 |
| 8 | self.jointType = JT_SQUARE | # 工艺参数：衔接类型 |
| 9 | self.offsetPolys = [] | # 临时存储的中间偏置曲线列表 |
| 10 | self.paths = [] | # 最终输出的轮廓平行路径列表 |
| 11 | self.offset() | # 调用连续偏置路径生成函数 |
| 12 | self.linkLocalOffsets() | # 调用路径连接函数 |
| 13 | def offset( self ) : | # 定义连续偏置路径生成函数 |
| 14 | ca = ClipperAdaptor() | # 定义 Clipper 适配器对象 |
| 15 | ca.arcTolerance = self.arcTolerance | # 设置圆弧精度(使用圆弧衔接) |
| 16 | delta = self.interval / 2 | # 首次偏置距离 |
| 17 | polys = ca.offset( self.boundaries , -delta , self.jointType ) | |
| 18 | self.offsetPolys.append( polys ) | # 偏置曲线存放在 offsetPolys 中 |
| 19 | while math.fabs( delta ) < self.shellThk : | # 循环直至偏置距离大于填充带宽 |
| 20 | delta += self.interval | |
| 21 | polys = ca.offset( self.boundaries , -delta , self.jointType ) | |
| 22 | if polys is None or len( polys ) == 0: break | # 已到偏置区域中心，则退出 |
| 23 | self.offsetPolys.append( polys ) | |
| 24 | def linkLocalOffsets( self ) : pass | # 定义路径连接函数 |

我们来看一下以上代码。在 GenCpPath 类的初始化函数中，我们输入的是表示打印区域的边界轮廓 boundaries、偏置距离 interval 和填充带宽 shellThk 三个参数，其中偏置距离就是喷头直径，填充带宽类似于打印后模型的表皮或外壳厚度，这两个参数是和三维打印工艺直接相关的参数，即工艺参数。除此之外，在 GenCpPath 类中，我们还定义了圆弧精度和劣角顶点衔接类型两个工艺参数，默认情况下，圆弧精度为 0.005 毫米，劣角顶点采用矩形方式衔接。事实上，我们在 ClipperAdaptor 类中已经定义过两个同名参数( 属性 )，尽管它们的默认值一致，但在类中定义这两个参数单纯是出于几何上的考虑，而在 GenCpPath 类中定义这两个参数是出于工艺上的考虑，两者的层次不同。

我们在 GenCpPath 类中定义了两个函数，Offset 和 LinkLocalOffsets，分别用于连续偏置路径的生成和连接，但在这一小节我们只实现了 Offset 函数，LinkLocalOffsets 函数将在下一小节实现。在 offset 函数中，我们首先定义了一个 ClipperAdaptor 类的对象 ca，随后设置了 ca 的圆弧精度为指定值，这一步操作事实上仅当劣角顶点衔接类型为圆弧衔接时才有效。然后我