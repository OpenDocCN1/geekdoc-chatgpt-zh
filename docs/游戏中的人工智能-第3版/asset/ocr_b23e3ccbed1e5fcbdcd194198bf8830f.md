示例中为 1024) 。这是对于每个倍频而言的。一个完整的集合将有 log₂n 个倍频，所以它在时间中的性能为 O(n² logn)。

我们还需要将随机梯度存储在粗糙网格中，这意味着在空间中的最大值为 O(n²)。多个倍频可以重复使用同一存储。

# 8.3.4 断层

地质断层（Fault）是指岩石中的裂缝，裂缝的两侧相互滑动。在三维中，这是一个平面，但是如果从二维高度图中的上方看，它可以表示为一条线。这条线的一侧地面是抬升的，另一侧地面则是下低的。

在真实的地形中，通常可以通过断层上方的岩石层或裸露表面的风化来平滑这种陡峭的高程变化。在模拟环境中，通常会在其他可平滑过渡的修饰器之前添加断层。

最简单的断层修饰器会在整个地形上随机绘制一条线，将一侧的所有位置升高，而另一侧的位置降低。升高和降低的量可以是算法的参数，也可以是一些随机量。具体的伪代码如下：

```
function faultModifier(landscape: Landscape, depth: float):
    # 创建随机断层震中和方向向量
    cx = random() * landscape.size
    cy = random() * landscape.size
    direction = random() * 2 * pi
    dx = cos(direction)
    dy = sin(direction)
    # 应用断层
    for x in 0..landscape.size:
        for y in 0..landscape.size:
            # 包含断层的位置的点积
            ox = cx - x
            oy = cy - y
            dp = ox * dx + oy * dy
            # 正点积抬升地面，负点积降低地面
            if dp > 0:
                change = depth
            else:
                change = -depth
            landscape.elevation[x][y] += change
```

如果要对地形应用一些严重的断层，可以使用此修饰器的一种变体，其中高程变化