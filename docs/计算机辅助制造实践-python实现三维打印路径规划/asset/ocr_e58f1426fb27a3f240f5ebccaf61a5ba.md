```
# TopoSlicer.py | TEdge 类定义,它表示具有拓扑关系的棱边( 半边) | 7 - 2
...... # 导入必要库,省略其他代码
class TEdge: # 定义 TEdge 类
    def __init__( self, tA, tB) : # 从两个 TVertex 点初始化
        self.A, self.B = tA, tB # 两个顶点引用 A、B
        self.F = None # 所从属的面片引用 F
        self.OE = None # 对边引用 OE
    def toTuple( self) : # 将边转化为元组( 6 维)
        if self.A.isSmaller( self.B) :
            return ( self.A.x, self.A.y, self.A.z, self.B.x, self.B.y, self.B.z)
        else :
            return ( self.B.x, self.B.y, self.B.z, self.A.x, self.A.y, self.A.z)
    def intersect( self, z) : # 边和 z 平面求交,返回交点
        if min( self.A.z, self.B.z) > z or max( self.A.z, self.B.z) < z :
            return None # 如果边高于或低于 z,无交点
        elif self.A.z == self.B.z == z :
            return None # 如果边位于 z 平面,无交点
        else :
            if z == self.A.z : return self.A.toPoint3D()
            else :
                ratio = ( z - self.A.z ) / ( self.B.z - self.A.z ) # 根据 z 比例计算
                vec = self.A.toPoint3D() . pointTo( self.B.toPoint3D() ) . amplified( ratio)
                pnt = self.A.toPoint3D() + vec
                return pnt
```

我们先来看一下 TEdge 的 4 个属性,它们分别是 A、B、F 和 OE。其中 A 和 B 为 TVertex 类型的引用,表示半边的两个顶点,F 为半边所在的面片的引用,OE 为当前半边对边的引用。这里我们特别强调“引用”或“指针”的概念,是为了后期应用时能快速索引这些对象。在 Python 中,一个变量默认保存的就是一个对象的引用,因此我们不用刻意在意这些问题;但如果我们使用 C 或 C++来实现这些拓扑类,那么就需要保存对象的指针了。

在 TEdge 的初始化函数中,我们只对半边的两个顶点赋值,其他属性默认为 None。在本章接下来的代码中,我们会在函数的参数中使用小写的“t”前缀来表示它是拓扑类的对象。和 TVertex 类一样,我们同样为 TEdge 类定义了一个 toTuple 函数,这个函数将两个顶点的 6 个坐标转化成了一个 6 维的元组。为了保证两条位置和坐标完全一致的半边最终能够得到一样的 6 维元组,我们在这里用了一个小技巧,即对 TEdge 的两个 TVertex 顶点进行比较,保证较小的点的 3 个坐标落在 6 维元组的前 3 位。在这个过程中,我们用到了 TVertex 的 isSmaller 函数,这也正是我们之前定义 isSmaller 的原因。

最后,在 TEdge 类中,我们还定义了一个 intersect 函数,这个函数的作用是计算当前半边和高度为 z 的 XY 平面( 即 Z 平面)的交点,它是我们后面计算拓扑模型和截平面交线的核心基础函数。注意,我们现在构建的这些拓扑类,最终都是为切片轮廓计算服务的。在