Actor 函数从场景中移除已绘制的对象。

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

![](bbox=[0.112, 0.582, 0.842, 0.918])

图 3.5 使用 VtkAdaptor 适配器类绘制几何实体