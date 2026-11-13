
| | vtkActor 属性( vtkProperty) 设置 | 3-6 |
|---|---|---|
| 1 | property = actor.GetProperty | # 获取属性 |
| 2 | property.SetRepresentationToWireframe() | # 设置线框模型 |
| 3 | property.SetRepresentationToPoints() | # 设置点模型 |
| 4 | property.SetRepresentationToSurface() | # 设置面模型 |
| 5 | property.SetColor( 0.5, 0.6, 0.7) | # 设置模型颜色 |
| 6 | property.SetOpacity(0.5) | # 设置模型透明度 |
| 7 | property.EdgeVisibilityOn() | # 是否绘制模型边 |
| 8 | property.SetEdgeColor( 0, 0.8, 0) | # 设置模型边界颜色 |
| 9 | property.SetLineWidth(2) | # 设置线宽 |
| 10 | ...... | |

在获得演员对象后，我们就可以将演员加入场景，代码如下：

| | 将演员加入场景 | 3-7 |
|---|---|---|
| 1 | renderer = vtk.vtkRenderer() | # 创建场景(渲染器) |
| 2 | renderer.AddActor( actor) | # 将 actor 添加到场景 |

如上所述，在 VTK 中，一个几何实体的可视化流程先后经历了 source、filter、mapper、actor 阶段，其中 filter 阶段不是必须的。下面我们再给出针对 STL 模型的可视化完整代码( DisplaySTL.py)，注意，在下面代码中，我们调整了部分代码结构，将三维场景的构建放在前面。代码运行后显示效果如图 3.4 所示。

![](img/c3ab49ac4eb80e7a0a66462a29869ca2_63_0.png)

图 3.4 STL 模型及其边框显示效果

```python
DisplaySTL.py | 利用 VTK 显示一个 STL 模型和模型边框 ( vtkOutlineFilter ) | 3-8

1 import vtk # 导入 vtk 库
2
3 renderer = vtk.vtkRenderer() # 创建三维场景
4 renderer.SetBackground(0.95, 0.95, 0.95)
5 window = vtk.vtkRenderWindow()
6 window.AddRenderer(renderer)
7 window.SetSize(900, 600)
8 interactor = vtk.vtkRenderWindowInteractor()
9 interactor.SetRenderWindow(window)
10 interactor.SetInteractorStyle( vtk.vtkInteractorStyleTrackballCamera() )
11 interactor.Initialize()
12
13 stlReader = vtk.vtkSTLReader() # 从文件加载 STL 模型数据源
14 stlReader.SetFileName( "E:\\3DP.STL" )
15
16 filter = vtk.vtkOutlineFilter() # 创建模型边框显示 filter-mapper-actor
17 filter.SetInputConnection( stlReader.GetOutputPort() )
18 outLineMapper = vtk.vtkPolyDataMapper()
19 outLineMapper.SetInputConnection( filter.GetOutputPort() )
20 outlineActor = vtk.vtkActor()
21 outlineActor.SetMapper( outLineMapper )
22 outlineActor.GetProperty().SetColor(0.1, 0.1, 0.1)
23 renderer.AddActor( outlineActor )
24
25 stlMapper = vtk.vtkPolyDataMapper() # 创建模型显示 mapper-actor
26 stlMapper.SetInputConnection( stlReader.GetOutputPort() )
27 stlActor = vtk.vtkActor()
28 stlActor.SetMapper( stlMapper )
29 renderer.AddActor( stlActor )
30
31 interactor.Start() # 运行程序
```

## 3.4 VTK 适配器的构建

在前面几节中，我们发现，如果我们要显示一个几何实体，总是要写很多和 VTK 相关的代码，以构建 VTK 显示环境。这个过程非常繁琐，容易出错但又不得不做。这一节我们拟用面向对象编程的方法和原则，对 VTK 的部分功能做进一步封装，得到一个适合我们的简洁易用的适配器( adaptor )类。

我们将这个类写在 VtkAdaptor.py 文件中，类名为 VtkAdaptor，详细代码如下：

```python
import vtk             # 导入 vtk 库
......                 # 导入必要库
class VtkAdaptor:
    def __init__( self, bgClr = ( 0.95, 0.95, 0.95 ) ):   # 适配器初始化函数
        self.renderer = vtk.vtkRenderer()
        self.renderer.SetBackground( bgClr )
        self.window = vtk.vtkRenderWindow()
        self.window.AddRenderer( self.renderer )
        self.window.SetSize( 1000, 1000 )
        self.interactor = vtk.vtkRenderWindowInteractor()
        self.interactor.SetRenderWindow( self.window )
        self.interactor.SetInteractorStyle( vtk.vtkInteractorStyleTrackballCamera() )
        self.interactor.Initialize()
    def display( self ):                                  # 显示函数
        self.interactor.Start()
    def setBackgroundColor( self, r, g, b ):              # 设置 VTK 场景背景色函数
        return self.renderer.SetBackground( r, g, b )
    def drawAxes( self, length = 100.0, shaftType = 0, cylinderRadius = 0.01, coneRadius = 0.2 ):
        axes = vtk.vtkAxesActor()                         # 绘制坐标轴
        axes.SetTotalLength( length, length, length )
        axes.SetShaftType( shaftType )
        axes.SetCylinderRadius( cylinderRadius )
        axes.SetConeRadius( coneRadius )
        axes.SetAxisLabels( 0 )
        self.renderer.AddActor( axes )
        return axes
    def drawActor( self, actor ):                          # 绘制 actor 对象
        self.renderer.AddActor( actor )
        return actor
    def drawPdSrc( self, pdSrc ):                          # 绘制 PolyData 类型的 source
        mapper = vtk.vtkPolyDataMapper()
        mapper.SetInputConnection( pdSrc.GetOutputPort() )
        actor = vtk.vtkActor()
        actor.SetMapper( mapper )
        return self.drawActor( actor )
    def drawStlModel( self, stlFilePath ):                 # 绘制 STL 模型,参数为文件名
        reader = vtk.vtkSTLReader()
        reader.SetFileName( stlFilePath )
        return self.drawPdSrc( reader )
    def removeActor( self, actor ):                        # 从场景中移除 actor 对象
        self.renderer.RemoveActor( actor )
    def drawPoint( self, point, radius = 2.0 ):         # 绘制一个点,Point3D
        src = vtk.vtkSphereSource( )
        src.SetCenter( point.x, point.y, point.z )
        src.SetRadius( radius )
        return self.drawPdSrc( src )
    def drawSegment( self, seg ):           # 绘制一条线段,Segment
        src = vtk.vtkLineSource( )
        src.SetPoint1( seg.A.x, seg.A.y, seg.A.z )
        src.SetPoint2( seg.B.x, seg.B.y, seg.B.z )
        return self.drawPdSrc( src )
    def drawPolyline( self, polyline ):      # 绘制一条多段线,Polyline
        src = vtk.vtkLineSource( )
        points = vtk.vtkPoints( )
        for i in range( polyline.count( ) ) :
            pt = polyline.point( i )
            points.InsertNextPoint( ( pt.x, pt.y, pt.z ) )
        src.SetPoints( points )
        return self.drawPdSrc( src )
    pass                 # 有待扩充
```

对以上 VtkAdaptor 类,我们在其__init__函数中通过相应的 VTK 类创建了 renderer、window 以及 interactor 属性。因此,在实际应用中,我们只需创建 VtkAdaptor 类的对象,并调用 display 函数,就可方便地构建 VTK 显示场景。如果我们需要改变场景背景颜色,只需调用 setBackgroundColor 函数即可。

在 VtkAdaptor 类中,我们封装了若干个几何实体绘制函数,包括 drawAxes、drawActor、drawPdSrc 以及 drawStlModel 函数。其中函数 drawAxes 的作用是在场景中绘制坐标轴,坐标轴的长度、箭头大小等都可通过该函数参数设置。函数 drawActor 的作用是绘制 vtkActor 类型的对象,在调用这个函数时,须确保在此之前已经通过数据源、映射器创建了 vtkActor 对象。和 drawActor 相比,drawPdSrc 函数可更加方便地对数据源直接进行绘制,其参数是 PolyData 类型的 source,如通过 vtkCubeSource、vtkConeSource 以及 vtkSTLReader 等类型创建的对象。函数 drawStlModel 对 drawPdSrc 进行了进一步封装,可直接通过输入 STL 文件路径绘制 STL 模型。

此外,我们还结合在第 2 章中创建的几个几何实体类,封装了 drawPoint、drawSegment 以及 drawPolyline 函数。其中函数 drawPoint 的输入参数为 Point3D 对象,可在场景中绘制一个点,点的大小可通过 radius 参数设置,默认值为 2.0。函数 drawSegment 的作用是绘制线段,输入参数为 Segment 对象。函数 drawPolyline 的作用是绘制多段线,输入参数为 Polyline 对象。

以上 draw 函数的返回值都是在场景中被绘制的 vtkActor 对象。我们可以在外部代码中保存这些返回的 vtkActor 对象,这样就可以在必要的时候改变对象属性或通过 removeActor 函数从场景中移除已绘制的对象。

下面我们对以上封装的 VtkAdaptor 类进行测试,测试代码写在 Test_vtkAdaptor.py 文件中,测试结果如图 3.5 所示。

| Test_vtkAdaptor.py | VtkAdaptor 类测试 | 3-10 |
|---|---|---|
| 1 | from VtkAdaptor import * | # 导入 VtkAdaptor 模块 |
| 2 | if __name__ =='__main__': | # 测试 main 函数入口 |
| 3 | vtkAdaptor = VtkAdaptor() | # 创建 VtkAdaptor 对象,必不可少 |
| 4 | vtkAdaptor.setBackgroundColor(0.95, 0.95, 0.95) | # 设置场景背景颜色 |
| 5 | vtkAdaptor.drawAxes() | # 绘制坐标轴 |
| 6 | vtkAdaptor.drawPoint(Point3D(10, 10, 10)).GetProperty().SetColor(1, 0, 0) | # 绘制点,设置颜色 |
| 7 | vtkAdaptor.drawPoint(Point3D(50, 50, 50)).GetProperty().SetColor(1, 0, 0) | |
| 8 | polyline = Polyline() | # 创建多段线对象 |
| 9 | polyline.addPoint( Point3D(1, 1, 1) ) | # 在多段线中添加点 |
| 10 | polyline.addPoint( Point3D(50, 2, 10) ) | |
| 11 | polyline.addPoint( Point3D(20, 10, 30) ) | |
| 12 | polyline.addPoint( Point3D(50, 80, 55) ) | |
| 13 | polylineActor = vtkAdaptor.drawPolyline(polyline) | # 绘制多段线 |
| 14 | polylineActor.GetProperty().SetColor(0.1, 0.7, 0.7) | # 设置多段线颜色 |
| 15 | polylineActor.GetProperty().SetLineWidth(2) | # 设置多段线线宽 |
| 16 | stlActor = vtkAdaptor.drawStlModel( "E:\\3DP.STL" ) | # 根据文件路径绘制 STL 模型 |
| 17 | stlActor.SetPosition(0, 150, 150) | # 设置模型位置 |
| 18 | vtkAdaptor.display() | # 调用 display 函数,这一步必不可少 |

![](img/c3ab49ac4eb80e7a0a66462a29869ca2_67_0.png)

图 3.5 使用 VtkAdaptor 适配器类绘制几何实体

因为以上测试代码是写在另一个文件里的，按照 Python 的语法规则，在文件开头我们需要导入之前封装好的 VtkAdaptor 模块。在 main 函数开头，我们创建了一个 VtkAdaptor 的对象；在 main 函数结尾处，我们调用了 display 函数。这两个步骤必不可少，用于搭建 VTK 绘图场景。在这两行代码语句之间，我们可以自由发挥，按照自己的需求绘制想要的图形。如在上例中，我们设置了场景背景色，绘制了坐标轴、两个 Point3D 对象、一个 Polyline 对象以及一个 STL 模型。注意到在以上 drawPoint 函数后，我们直接在同一行调用了 GetProperty 和 SetColor 两个函数设置点的颜色，这正是应用了 VtkAdaptor 类在设计时考虑将 draw 函数的返回值设置为 vtkActor 对象的巧妙之处。最后需要强调的是，VtkAdaptor 类是一个可以不断扩展的类，我们在这里给出的只是适应当前需求的代码。如果后面需求增加，我们可以给 VtkAdaptor 类增加相关属性或函数。

## 3.5 VTK 模型变换

在上一节最后的 VtkAdaptor 测试代码中，我们根据文件路径在场景中绘制了一个 STL 模型，并调用 vtkActor 类的 SetPosition 函数对模型进行了平移，使其恰好落在场景中的合适位置。这一节我们将剖析 vtkActor 及其父类，介绍如何对场景中绘制的实体进行几何变换，包括平移、缩放以及旋转变换。

VTK 中各个类的继承关系非常复杂，盘根错节。其中对于 vtkActor 类，如图 3.6 所示，它继承自 vtkProp3D 类，而 vtkProp3D 又从 vtkProp 继承而来。这些类在设计时的功能侧重点不同，其中 vtkProp 类主要负责实体在场景中渲染层面的工作，包括三维和二维实体渲染，但在实际应用中，我们一般很少直接在代码中调用 vtkProp 类中的方法。vtkProp3D 类主要负责实体在场景中几何层面的工作，封装了包括设置/获取实体位置（Set/GetPosition）、设置/获取实体缩放比例（Set/GetScale）、旋转实体（RotateX/Y/Z）以及获取实体包络（GetBounds）等功能函数。vtkActor 类是我们在编写代码时接触最多且直接使用的类，主要负责实体在场景中显示层面的工作，如 Set/GetProperty 以及 Set/GetTexture 等函数。在图 3.6 中，我们看到 vtkProp 还有一个 vtkActor2D 的子类，这个类是 vtkActor 的平行类，两个类的区别在于 vtkActor 负责三维实体绘制，而 vtkActor2D 负责二维实体绘制。

![](img/c3ab49ac4eb80e7a0a66462a29869ca2_68_0.png)

图 3.6 vtkActor 类继承关系图

根据以上分析，如果我们需要对三维模型进行几何变换，我们只需调用 vtkActor 直系
