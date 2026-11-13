需要说明的是，本书中，绕某一个轴旋转的方向规定如下：如果 θ 为正，则按逆时针旋转；如果 θ 为负，则按顺时针旋转。

在实际应用中，对一个点的几何变换往往是包含平移、缩放、旋转中一个或多个的复合操作，这时，我们就要按照操作的顺序依次对点右乘相应矩阵，而且右乘的次序不能随意调换，否则会影响几何变换结果。如公式(2-10)，我们首先对点 P 绕 Z 轴旋转 θ1角度，然后再对得到的更新点绕 Y 轴旋转 θ2角度，随后分别对得到的更新点平移和缩放，最后得到 P'。

P' = P · M_n(θ1) · M_n(θ2) · M_t · M_s (2-10)

# ## 2.1.4 几何基础模块实现

这一小节我们将对上面介绍的点、向量和矩阵使用 Python 进行封装，创建 3 个类，分别为：Point3D、Vector3D、Matrix3D，并在这些类中定义相关函数。我们将这 3 个类放在 GeomBase.py 文件中，其中 Geom 为 Geometry（几何）的缩写，GeomBase 模块是今后我们进行几何操作的基础，它的具体实现代码如下：

| GeomBase.py | 几何基础库,定义点、向量、矩阵以及它们之间的相互操作 | 2-1 |
|---|---|---|
| 1 | import math | # 导入 math 库 |
| 2 | epsilon = 1e-7 | # 定义一个极小值,10^-7 |
| 3 | epsilonSquare = epsilon * epsilon | # 极小值平方 |
| 4 | class Point3D: | # 定义点类 |
| 5 | def __init__(self, x=0.0, y=0.0, z=0.0, w = 1.0): | |
| 6 | self.x, self.y, self.z, self.w = x, y, z, w | |
| 7 | def __str__(self): | # 点在 print 时的显示行为 |
| 8 | return 'Point3D: %s, %s, %s' % (self.x, self.y, self.z) | |
| 9 | def clone(self): | # 克隆当前点,得到新点 |
| 10 | return Point3D(self.x, self.y, self.z, self.w) | |
| 11 | def pointTo(self, other): | # 返回当前点到另一个点的向量 |
| 12 | return Vector3D(other.x - self.x, other.y - self.y, other.z - self.z) | |
| 13 | def translate(self, vec): | # 根据向量对当前点进行平移 |
| 14 | self.x, self.y, self.z = self.x + vec.dx, self.y + vec.dy, self.z + vec.d | |
| 15 | def translated(self, vec): | # 对当前点平移后返回新点 |
| 16 | return Point3D(self.x + vec.dx, self.y + vec.dy, self.z + vec.dz) | |
| 17 | def multiplied(self, m): | # 点右乘一个矩阵后返回新点 |
| 18 | x = self.x * m.a[0][0] + self.y * m.a[1][0] + self.z * m.a[2][0] + self.w * m.a[3][0] | |
| 19 | y = self.x * m.a[0][1] + self.y * m.a[1][1] + self.z * m.a[2][1] + self.w * m.a[3][1] | |
| 20 | z = self.x * m.a[0][2] + self.y * m.a[1][2] + self.z * m.a[2][2] + self.w * m.a[3][2] | |
| 21 | return Point3D(x, y, z) | |
| 22 | def distance(self, other): | # 计算两点之间的距离 |
| 23 | return self.pointTo(other).length() | |
| 24 | def distanceSquare(self, other): | # 计算两点距离的平方 |