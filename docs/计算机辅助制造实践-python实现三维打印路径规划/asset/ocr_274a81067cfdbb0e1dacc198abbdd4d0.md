```
```python
len = self.length()
return Vector3D(self.dx / len, self.dy / len, self.dz / len)
def isZeroVector(self):                     # 判断是否为零向量,即长度为 0
    return self.lengthSquare() == 0.0
def multiplied(self, m):                   # 向量乘以矩阵
    dx=self.dx * m.a[0][0]+self.dy * m.a[1][0]+self.dz * m.a[2][0]+self.dw+m.a[3][0]
    dy=self.dx * m.a[0][1]+self.dy * m.a[1][1]+self.dz * m.a[2][1]+self.dw+m.a[3][1]
    dz=self.dx * m.a[0][2]+self.dy * m.a[1][2]+self.dz * m.a[2][2]+self.dw+m.a[3][2]
    return Vector3D(dx, dy, dz)
def isParallel(self, other):               # 判断两个向量是否平行
    return self.crossProduct(other).isZeroVector()
def getAngle(self, vec):                   # 计算两个向量夹角,返回值范围 0 ~ pi
    v1, v2 = self.normalized(), vec.normalized()
    dotPro = v1.dotProduct(v2)
    if dotPro > 1: dotPro = 1              # 重要:计算值可能大于 1,必须归 1
    elif dotPro < -1: dotPro = -1
    return math.acos(dotPro)
def getOrthoVector2D(self):                # 在 XY 平面上生成当前向量的正交向量
    if self.dx == 0: return Vector3D(1, 0, 0).normalized()
    else: return Vector3D(-self.dy / self.dx, 1, 0).normalized()
def getAngle2D(self):                      # 在 XY 平面上计算当前向量和 X 轴夹角
    rad = self.getAngle(Vector3D(1, 0, 0))   # 返回值范围 0 ~ 2pi
    if self.dy < 0: rad = math.pi * 2.0 - rad
    return rad
def __add__(self, other):                  # 加号,两个向量相加
    return Vector3D(self.dx + other.dx, self.dy + other.dy, self.dz + other.dz)
def __sub__(self, other):                  # 减号,两个向量相减
    return self + other.reversed()
def __mul__(self, other):                  # 乘号,向量和矩阵相乘
    return self.multiplied(other)
class Matrix3D:                            # 定义矩阵类
    def __init__(self):
        self.a = [[1, 0, 0, 0], [0, 1, 0, 0], [0, 0, 1, 0], [0, 0, 0, 1]]
    def __str__(self):                     # 矩阵在 print 时的显示行为
        return ' Matrix3D: n%s\n%s\n%s\n%s ' % (self.a[0], self.a[1], self.a[2], self.a[3])
    def makelIdentical(self):                 # 矩阵单位化
        self.a = [[1, 0, 0, 0], [0, 1, 0, 0], [0, 0, 1, 0], [0, 0, 0, 1]]
    def multiplied(self, other):             # 当前矩阵乘以另一个矩阵
        m = Matrix3D()
        for i in range(4):
            for j in range(4):
                m.a[i][j] = self.a[i][0] * other.a[0][j] + self.a[i][1] * other.a[1][j] \
                + self.a[i][2] * other.a[2][j] + self.a[i][3] * other.a[3][j]
```
```