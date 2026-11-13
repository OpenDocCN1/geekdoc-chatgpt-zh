后将这些点依次连接，就可以得到一系列线段。对平面上所有的同心圆，我们可以采取一样的细分操作，最后可以得到一个保存了所有同心圆上所有细分线段的线段列表，记为 segs。

列表 segs 满足了以上提到的两个要求：一是 segs 中线段的数量可由每个圆细分的份数和同心圆个数来控制，达到百万甚至千万级别完全不是问题；二是 segs 中各线段在内在逻辑上也能连成若干封闭轮廓。但是 segs 目前还不满足线段散乱分布的要求，它里面的线段都是原生态地依次从圆上采集得到的。如不考虑同心圆的情况，则相邻序号的线段在平面上也必然是相接的，这显然是不对的。打乱 segs 必然需要用到随机数，最简单的办法就是遍历 segs 中的每个元素，让当前序号的元素和一个序号随机生成的元素交换。

结合以上思路，下面我们来实现一下平面上散乱线段的生成函数，我们将这个函数命名为 makeMessySegs，单独写在 makeMessySeg.py 文件中，具体代码如下：

```
```python
from Segment import *          # 导入 Segment 模块
from random import *         # 导入随机数生成模块
import math                  # 导入 math 模块
def makeMessySegs( circleNum = 10, segNumPerCircle = 100000, radius = 100.0 ): # 函数数定义
    segs = []                    # 定义线段列表
    r = radius                   # 以半径递增的方式生成同心圆
    for i in range( circleNum ): # 遍历，生成指定 cirlceNum 个同心圆
    pnts = []                    # 点列表，用于收集圆上的点
    for j in range( segNumPerCircle ): # 按细分份数细分圆
        theta = j / segNumPerCircle * 2 * math.pi # 角度 theta
        x = r * math.cos( theta )    # 圆上点 X,Y 坐标，圆心在原点
        y = r * math.sin( theta )
        pnt = Point3D( x, y )
        pnts.append( pnt )           # 将点添加到 pnts
    pnts.append( pnts[0] )           # 在 pnts 添加起点，以封闭轮廓
    for j in range( len( pnts ) - 1 ): # 将点依次整理为线段，添加至 segs
        seg = Segment( pnts[ j ], pnts[ j+1 ] )
        segs.append( seg )
    r += 10.0                        # 同心圆半径递增
print( ' segment count: ', len( segs ) ) # 打印 segs 中线段总数
print( ' min segment length: ', segs[0].A.distance( segs[0].B ) ) # 打印 segs 中最短的线段
for i in range( len( segs ) ): # 对 segs 中线段散乱化处理
    rd = randint( 0, len( segs ) - 1 ) # 生成一个随机序号
    segs[ i ], segs[ rd ] = segs[ rd ], segs[ i ] # 当前线段和随机序号线段交换
return segs
```
```

在以上代码中，因为我们需要用到正余弦函数，因此需要导入 math 库；又因为要用到随机数生成函数，因此需要导入 random 库。这两个库都是 Python 的内建基础库，不需要