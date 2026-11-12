# 量 change 与断层线的方向相关：

```
function faultDropoffModifier(landscape: Landscape,
depth: float, width: float):
    # 创建随机断层震中和方向向量
    cx = random() * landscape.size
    cy = random() * landscape.size
    direction = random() * 2 * pi
    dx, dy = cos(direction), sin(direction)

    # 应用断层
    for x in 0..landscape.size:
        for y in 0..landscape.size:
            # 包含断层的位置的点积
            ox, oy = cx - x, cy - y
            dp = ox * dx + oy * dy

            # 正点积抬升地面，负点积降低地面
            if dp > 0:
                change = depth * width / (width + dp)
            else:
                change = -depth * width / (width - dp)
            landscape.elevation[x][y] += change
```

通过使用大量的小断层，原始版本可以基于其自身生成非常有用的地形。图 8.9 显示了以此方式生成的具有其特征线性元素的地形。

![](bbox=[0.12, 0.61, 0.84, 0.89])

图 8.9 由数百个断层产生的地形