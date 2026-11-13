
```
99     segments = [] # 返回-1;点在多边形边上
100    for i in range( polygon.count() -1 ):
101        seg = Segment( polygon.point(i), polygon.point( i+1 ) )
102        segments.append( seg )
103    for seg in segments:      # 计算线段(不包含端点)和射线交点个数
104        line1, line2 = Line( ray.P, ray.V ), Line( seg.A, seg.direction() )
105        P, t1, t2 = intersectLineLine( line1, line2 )
106        if P is not None:
107            if nearZero( t1 ) : return -1
108        elif seg.A.y != p.y and seg.B.y != p.y and t1 > 0 and t2 > 0 and t2 < seg.length() :
109            passCount += 1
110    upSegments, downSegments = [], []   # 存放射线上方和下方的线段
111    for seg in segments:    # 计算多边形顶点和射线交点个数
112        if seg.A.isIdentical( ray.P ) or seg.B.isIdentical( ray.P ):
113            return -1      # 点在多边形边上
114        elif pointOnRay( seg.A, ray ) ^ pointOnRay( seg.B, ray ):
115            if seg.A.y >= p.y and seg.B.y >= p.y: upSegments.append( seg )
116        elif seg.A.y <= p.y and seg.B.y <= p.y: downSegments.append( seg )
117    passCount += min( len( upSegments ), len( downSegments ) )    # 顶点配对,图 2.15
118    if passCount % 2 == 1:   # 射线穿过多边形次数为奇数,
119        return 1   # 则点在多边形内部
120    return 0   # 否则点在多边形外部
```

在以上代码中,我们主要实现了 distance、intersect 和 pointInPolygon 三个函数,分别对应实体距离计算、实体求交计算以及点和多边形关系判断。其中 pointInPolygon 函数有 3 种返回结果:当返回结果为 1 时,表示点在多边形内部;当返回结果为 0 时,表示点在多边形外部;当返回结果为 -1 时,表示点在多边形边上。此外,作为以上几个函数的副产品,我们还实现了 intersectLineLine、intersectSegmentPlane、pointOnRay 等几个函数,专门用于计算直线和直线求交、线段和平面求交,以及判断点是否落在射线上。其中,在 pointOnRay 中,我们主要应用了向量点乘和叉乘来实现函数功能,读者可以关注一下此类方法。

## 本章小结

在这一章中,我们使用 Python 语言,从零开始,搭建了一个属于我们自己的计算几何库,建立了点、向量、矩阵、直线、射线、线段、多段线、平面等几何类,并实现了实体之间的距离计算、求交计算以及点和多边形关系判断等若干几何算法。这些基础类和算法是我们后面章节进一步展开的基础。在今后的学习中,我们希望读者可以不断积累,持续地扩充自己的几何库。

## 思考题

- 1. 在计算几何中，为什么点和向量一般使用齐次坐标表示？
- 2. 简述四阶方阵中，各子区域中各个元素的作用。
- 3. 在三维空间中，如何判断两个不同的点相等或重合？
- 4. 在三维空间中，如何判断两个向量是否指向同一方向？
- 5. 实现 GeomBase 模块中 Matrix3D 类的逆矩阵求取函数。
- 6. 简述在直线、射线、线段类的初始化函数中对参数进行拷贝的原因。
- 7. 列举至少两种方法，判断一个平面多边形的顺逆时针方向。
- 8. 设计一种算法，在平面上，快速报告点云中落在多边形中点的方法。
- 9. 设计一种算法，快速计算平面上两个多边形之间的最近距离。

## 第 3 章 几何可视化平台搭建

### 引言

在上一章计算几何库实现过程中，大家可能会有这样的体会：我甚至都看不到那些几何实体，怎么验证结果是对是错？到现在为止，我们还缺乏对这些类或算法结果的直观感受，缺乏一个可以对点、线、面等实体类进行实时显示的工具。这一章的任务就是引导大家如何用 Python 语言，结合 VTK（visualization toolkits），搭建一个几何实体的可视化平台。

### 3.1 VTK 概述

VTK 是一个开源、跨平台、支持并行处理的免费软件系统，主要用于计算机图形学、建模、图像处理、体素渲染以及科学可视化。VTK 最早由 GE 研发部的三位研究人员（Will Schroeder、Ken Martin、Bill Lorensen）在闲暇时间合作开发完成。一经发布，VTK 很快便建立了用户社区，并迅速在学术研究和商业应用领域得到重用。目前，VTK 由 Kitware 公司维护升级。

VTK 是在三维图形库 OpenGL 的基础上采用面向对象的设计方法发展起来的，其内核使用 C++构建，包含大约 250000 行代码、2000 多个类。这些类的相互继承关系或类中函数的官方说明文档可在 https://vtk.org/doc/nightly/html/index.html 在线查看，该文档以 C++语言的形式对类或函数进行说明。此外，VTK 还包含几个转换接口，具有良好的可移植性，可以通过 Java、Python、Tcl/Tk、C#等其他编程语言自由地使用。

尽管 Kitware 已经出版了 _VTK User's Guide_ 和 _Visualization Toolkit_ 两本有关 VTK 的著作，但目前在国内仍然很难找到 VTK 的系统性中文学习书籍或资料，只有一些较为零碎的学习资料可在国内相关论坛或博客中找到。本书的定位不是关于 VTK 的系统性学习资料，而是 VTK 的启蒙学习，对计算机图形学研究或应用有兴趣的读者可进一步阅读 VTK 的 C++源码或官方英文原著。

VTK 是一个功能丰富的庞大的软件系统，但在 Python 下，VTK 的安装非常简单，无需编译、配置等繁琐的工作，我们只需在 Windows 的命令窗口输入“pip install VTK”命令即可安装，安装完成后可直接使用。目前，VTK 最新版本的版本号为 8.1.2，安装完成后大小约 80MB。

### 3.2 Hello VTK

当我们刚接触一门新的编程语言或新的第三方库时，通常喜欢用“Hello Something”作为开头，这里也不例外。这一节我们将在 Python 中使用 VTK 编写一个非常简单的类似于“Hello VTK”的示例程序，用于显示一个正方体。这个程序运行起来后具有一般三维软件的视图查看功能，如我们可以借助鼠标对视图进行缩放、平移和旋转等操作。我们将这个程序写在 Hello VTK.py 文件中，具体代码如下：

| | | |
|---|---|---|
| Hello VTK.py | Hello VTK,利用 VTK 显示一个正方体 | 3 - 1 |
| 1 | import vtk | # 导入 vtk 库，字母均为小写 |
| 2 | | |
| 3 | source = vtk.vtkCubeSource() | # 创建一个正方体源，默认边长为 1 |
| 4 | | |
| 5 | mapper = vtk.vtkPolyDataMapper() | # 创建 mapper |
| 6 | mapper.SetInputConnection( source.GetOutputPort() ) | # 设置 mapper 数据源 |
| 7 | | |
| 8 | actor = vtk.vtkActor() | # 创建 actor |
| 9 | actor.SetMapper( mapper ) | # 设置 actor 数据源 |
| 10 | actor.GetProperty().SetColor( 0.7, 0.7, 0.7) | # 设置 actor 显示颜色 |
| 11 | | |
| 12 | renderer = vtk.vtkRenderer() | # 创建渲染器 |
| 13 | renderer.AddActor( actor ) | # 将 actor 添加到渲染器 |
| 14 | renderer.SetBackground( 0.9, 0.9, 0.9 ) | # 设置渲染器背景颜色 |
| 15 | | |
| 16 | window = vtk.vtkRenderWindow() | # 创建渲染窗口 |
| 17 | window.AddRenderer( renderer ) | # 将渲染器添加到渲染窗口 |
| 18 | window.SetSize( 900, 600 ) | # 设置窗口尺寸，单位为像素 |
| 19 | | |
| 20 | interactor = vtk.vtkRenderWindowInteractor() | # 创建渲染窗口交互器 |
| 21 | interactor.SetRenderWindow( window ) | # 提供鼠标、键盘和时钟事件的交互机制 |
| 22 | interactor.SetInteractorStyle( vtk.vtkInteractorStyleTrackballCamera() ) | # 设置交互方式 |
| 23 | interactor.Initialize() | # 初始化交互器 |
| 24 | interactor.Start() | # 窗口程序开始运行 |

运行以上代码后，可以看到如图 3.1 所示的显示效果，我们可以使用鼠标对三维场景进行操作，如旋转中键滚轮可以放大或缩小视图、按住左键移动鼠标可以旋转视图、按住中键滚轮移动鼠标可以平移视图等。

在以上代码中，首先我们使用 import 语句引入了 VTK 库，注意，在实际代码中，vtk 须为小写字母。随后，我们使用 vtk.vtkCubeSource() 语句创建了一个正方体的 source 对象，vtkCubeSource 是 VTK 库中一个类的名字，在 VTK 库中，几乎所有的类名均使用小写的 vtk 前缀。默认情况下，这条语句将构造一个边长为 1、中心点在坐标系原点的立方体；如果需要改变边长或位置，可调用 SetXLength、SetYLength、SetZLength 以及 SetCenter 等函数。随后，我们根据这个 source 对象先后创建了 mapper 和 actor 对象，并且我们对 actor 对象设置了颜色属性，这个颜色就是我们在程序窗口中看到的实体颜色。如有需要，我们也可以设置 actor 的其他属性，如透明度、散射等。在 VTK 中，颜色可使用 RGB 方式表示和创建，每个颜色分量 R、G、B 取值为 0~1 范围内的实数。

![](img/c3ab49ac4eb80e7a0a66462a29869ca2_60_0.png)

图 3.1 使用 VTK 创建并显示一个立方体

在后面的代码中，我们又分别创建了 renderer、window 以及 interactor 对象。我们可以对照一个典型的 Windows 窗口应用程序来理解这 3 个对象。首先是 window 对象，这个对象就是程序的窗口界面，我们可以使用 SetSize 函数设置窗口的尺寸。然后是 renderer 对象，这个对象对应窗口中被渲染的视图，我们可以使用 AddActor 函数将几何实体的 actor 对象添加到视图中显示，也可以使用 SetBackground 函数设置视图背景颜色。最后是 interactor 对象，其实在每个 Windows 窗口应用程序的背后，都运行着一个消息循环，用于程序与外部或内部的交互，这里的 interactor 对象就对应类似的消息处理机制。以上 3 个对象相互作用，共同构成了 VTK 程序窗口显示场景。对 interactor 对象，我们在其 SetInteractorStyle 函数中设置了“轨迹球相机”的交互方式，即相机始终在一个轨迹球上移动；当然，我们也可以在这个函数中设置其他交互方式。

如果我们需要显示 2 个或多个实体，只需按以上代码中 source、mapper、actor 的创建流程分别再写几行代码，然后将相应 actor 对象加入 renderer 对象即可。在 VTK 中，除了 vtkCubeSource 外，我们还可以使用 vtkConeSource、vtkCylinderSource、vtkLineSource 等类创建圆锥、圆柱、直线等对象；此外，我们还可以用 vtkTextSource 创建文本对象，有兴趣的读者不妨尝试使用这个类在视图中显示“Hello VTK”字符，如图 3.2 所示。需要说明的是，目前 VTK 尚不支持中文字符显示。

### 3.3 VTK 可视化流程

在上一节中，我们提到 renderer、window 以及 interactor 这 3 个对象共同构建了三维物体的显示场景。一般情况下，场景一旦搭建，就基本保持稳定不变了，经常变化的是场景

![](img/c3ab49ac4eb80e7a0a66462a29869ca2_61_0.png)

图 3.2 使用 VTK 创建并显示“Hello VTK”字符

中的演员，actor 对象就是场景中的演员。下面我们来看一下 VTK 中 actor 的创建流程。

如图 3.3 所示，要得到一个 actor 对象，我们首先需要创建一个数据源（source）对象。在上一节的 Hello VTK.py 代码中，我们使用 vtkCubeSource 类创建了一个边长均为 1 的长方体数据源。VTK 提供了不止一种创建数据源的方法。我们可以通过代码的方式直接在内存中创建数据源，如 vtkCubeSource、vtkConeSource、vtkLineSource 等。对于一些复杂的几何体，我们还可以通过读取文件的方式获取其数据源，如对三维打印中常用的 STL 模型文件，我们可以使用 vtkSTLReader 类来获取模型数据源，代码如下：

| | 通过 vtkSTLReader 创建数据源 | 3-2 |
|---|---|---|
| 1 | stlReader = vtk.vtkSTLReader() | # 创建 reader 对象 |
| 2 | stlReader.SetFileName( " E: \3DP.STL" ) | # 设置模型文件路径 |

![](img/c3ab49ac4eb80e7a0a66462a29869ca2_61_1.png)

图 3.3 VTK 可视化流程

此外，如果对 VTK 足够熟悉，还可以根据 VTK 组件自定义一些数据源。

在获得数据源后，我们可以像在 Hello VTK.py 中的代码一样，直接将数据源连接到映射器（mapper）对象上，代码如下：

| | 映射器和数据源连接 | 3-3 |
|---|---|---|
| 1 | mapper = vtk.vtkPolyDataMapper() | # 创建 mapper 对象 |
| 2 | mapper.SetInputConnection(stlReader.GetOutputPort()) | # mapper 和 source 连接 |

或许在此之前我们还想对数据源进行进一步处理，这时我们就可以将数据源连接到一个过滤器（filter）对象上。

比如对以上提到的 STL 模型，如果我们想从中提取模型的包络矩形边框，就可以将模型数据源连接到一个 vtkOutlineFilter 的对象上，代码如下：

| | 过滤器和数据源连接，映射器和过滤器连接 | 3-4 |
|---|---|---|
| 1 | outlineFilter = vtk.vtkOutlineFilter() | # 创建 filter 对象 |
| 2 | outlineFilter.SetInputConnection(stlReader.GetOutputPort()) | # filter 和 source 连接 |
| 3 | mapper = vtk.vtkPolyDataMapper() | # 创建 mapper 对象 |
| 4 | mapper.SetInputConnection(outLineFilter.GetOutputPort()) | # mapper 和 filter 连接 |

在以上两个代码中，source 对象通过 GetOutputPort 获取端口后和 mapper 或 filter 对象连接，mapper 或 filter 通过 SetInputConnection 函数设置连接端口。

类似的，filter 对象也通过 GetOutputPort 获得端口后和 mapper 对象连接。这里的端口可以理解为实际对象的一个句柄或引用（指针），因为几何对象可能包含大量数据（比如一个 STL 模型可能包含几十兆甚至上百兆数据），在对象流转过程中如果直接传递对象本身，将消耗大量的时间和内存空间，采用端口传递可以避免这样的问题。

映射器的作用是将数据对象映射成图像对象，在以上代码中，我们使用了 vtkPolyDataMapper 映射器，这是为了和 STL 模型数据源相匹配，在 VTK 中，PolyData 一般表示由点、线、三角形、多边形等组成的离散几何拓扑结构，此外，VTK 中还有 vtkGraphMapper、vtkImageMapper 等映射器类。

在获得映射器对象后，我们可以进一步创建演员（actor）对象，代码如下：

| | 演员和映射器连接 | 3-5 |
|---|---|---|
| 1 | actor = vtk.vtkActor() | # 创建 actor 对象 |
| 2 | actor.SetMapper(mapper) | # actor 和 mapper 连接 |

演员对象是场景中显示的几何实体，它和映射器对象关联较为简单，只需调用 SetMapper 函数即可。vtkActor 类中有一个 GetProperty 函数，可以返回 vtkProperty 类型的对象。

通过这个对象，我们可以修改 actor 在场景中的外观属性，如实体绘制形式（points、wireframe、surface）、颜色、环境光、散色光、透明度、材料、纹理、边线颜色、顶点颜色、边线线宽等，参考代码如下：
