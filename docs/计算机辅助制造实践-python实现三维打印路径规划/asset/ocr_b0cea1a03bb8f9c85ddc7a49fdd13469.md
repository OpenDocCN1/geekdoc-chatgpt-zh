```
for triangle in self.stlModel.triangles:          # 遍历模型三角面片
    for z in triangle.zs:                         # 遍历面片中保存的层高
        seg = intersectTriangleZPlane( triangle , z) # 截交计算
        if seg is not None:
            layerDic[ z ].segments.append( seg)    # 将当前交线保存到层字典
    for layer in layerDic.values() :              # 将层字典转化为层列表
        self.layers.append( layer )
def genLayerHeights( self ) :                     # 生成切片层高列表函数
    xMin, xMax, yMin, yMax, zMin, zMax = self.stlModel.getBounds()
    zs , layerDic = [] , {}                       # 初始化列表，字典对象
    z = zMin + self.layerThk
    while z < zMax:
        zs.append( z)                             # 往列表中添加层高
        layerDic[ z ] = Layer( z)                 # 保存高度为 z 的层到字典中
        z += self.layerThk
    return zs , layerDic                          # 返回列表和字典对象
```

![](bbox=[0.15, 0.42, 0.85, 0.74])

# 图 5.7 基于层高匹配法的 STL 模型截交流程

我们先来看一下以上代码中的 genLayerHeights 函数。和这个函数几乎一模一样的函数我们在 IntersectStl_sweep 类中已经实现了一遍，所不同的是，在这个函数中，我们除了返回层高列表之外，还返回了层字典 layerDic。根据 layerDic，我们只要指定层高 z 值，就能快速查询并获得处于该层高处的 Layer 对象，如 layerDic[ z0 ] 表示高度为 z0 处的 Layer 对象，这也正是我们在这里使用字典的原因。

下面我们再来看一下 IntersectStl_match 类中最为核心的 intersect 函数。在完成了若干依赖函数的定义之后，我们发现，intersect 函数已经变得非常简洁，只需 4 步，便能完成优化截交功能，即层高生成、层高匹配、截交计算、数据转换。其中，在截交计算这一步中，