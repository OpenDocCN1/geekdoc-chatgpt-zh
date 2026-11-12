```
36 | if collisionDetector.valid(candidate):
37 |     collisionDetector.add(candidate)
38 |     locations.push(candidate)
39 |     return candidate
40 |
41 | return null
```

图 8.15 显示了以此方式生成的类似洞穴的迷宫的示例。

![](bbox=[0.24, 0.27, 0.72, 0.48])

图 8.15 使用迷宫算法创建的洞穴系统

# 5. 房间

到目前为止，我们都是从未使用的位置开始使用该算法，但是算法本身并没有这方面的任何要求或限制。开发人员可能会想从无法到达的区域开始（这些区域包含可开采的资源，或者仅用于对迷宫结构添加兴趣点）。同样，对于代表走廊的各个部分的位置也没有什么限制。只要关卡数据结构可以将它们视为单个的位置，就可以在该位置上放置房间，并生成迷宫将它们连接起来。

在伪代码中，这可能类似于：

```
1 | class Room:
2 |     width: int
3 |     height: int
4 |
5 | class GridLevelWithRooms extends GridLevel:
6 |     unplacedRooms: Room[]
7 |
8 | function canPlaceRoom(room: Room, x: int, y: int) -> bool:
9 |     inBounds = (
10 |     0 <= x < (width - room.width) and
11 |     0 <= y < (height - room.height()))
```