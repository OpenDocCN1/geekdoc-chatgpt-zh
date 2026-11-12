# 4. 洞穴

请注意，以上迷宫算法的伪代码中没有假设网格的元素，迷宫关卡界面中也没有任何元素。迷宫关卡的实现是基于网格的，但是该算法也适用于其他结构。

除了使用网格生成邻居外，还可以使用类似于泊松圆盘算法（详见第 8.1.4 节）中的方法。开发人员可以尝试沿任意方向在走廊中向前移动。

关卡接口可以按以下方式实现：

```
class Location:
    x: float
    y: float
    r: float
    passageFrom: Location = null

class CavesLevel:
    locations: Location[]
    collisionDetector # 检查洞穴是否被堵塞

function startAt(location: Location):
    locations.push(location)
    collisionDetector.add(location)

function makeConnection(location: Location) -> Location:
    # 按随机方向开始检查
    initialAngle = random() * 2 * pi

    # 下一个洞穴将具有随机大小
    nextRadius = randomRange(MIN_RADIUS, MAX_RADIUS)
    offset = r + nextRadius

    x = location.x
    y = location.y
    r = location.r
    for i in 0..ITERATIONS:
        # 尝试放置下一个洞穴
        theta = 2 * pi * i / ITERATIONS + initialAngle
        tx = x + offset * cos(theta)
        ty = y + offset * sin(theta)

        # 如果碰撞检测器中有空间
        # 则将其添加到洞穴位置列表中
        candidate = new Location(tx, ty, nextRadius, location)
```