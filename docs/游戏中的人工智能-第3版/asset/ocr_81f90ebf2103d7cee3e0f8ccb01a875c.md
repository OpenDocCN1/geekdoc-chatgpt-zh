讲，停止生成建筑物则可以有一些替代标准。这些标准如下。

+   - 关卡的最小或最大尺寸。
+   - 最小或最大分支数。
+   - 必须放置在某个地方的一组固定房间。
+   - 最小或最大死角数。

这些标准可用于通过跟踪有关迷宫的数据来终止迷宫的生成。

要跟踪关卡的大小，可使用以下方式：

```
class SizeLimitedGridLevel extends GridLevel:
    size: int = 0

    function makeConnection(location: Location) -> Location:
        result = super.makeConnection(location)
        if result:
            size += 1
            if canTerminate(now, next):
                return false
        return result

function canTerminate(now: Location, next: Location) -> bool:
    return size >= MAX_SIZE
```

在上述伪代码中，canTerminate 可以测试所添加的连接的详细信息，或者测试整个关卡上的所有数据。它返回 true 或 false 以指示其决定。

# 8.4.2 最小生成树算法

回溯算法快速且易于实现，它们允许一定程度的自定义。在上面显示的实现中，可以自定义关卡数据结构，并且可以对主算法进行修改以存储数据，还演示了一个尽早终止算法的示例。当然，有时还需要执行更多的自定义，特别是在开发人员需要更注重某些连接的情况下。

最小生成树（Minimal Spanning Tree，MST）算法需要加权图（Weighted Graph）才能进行工作，这类似于在第 4 章“路径发现”和第 6 章“战略和战术 AI”中讨论过的算法。加权图中的边代表迷宫中每个可能的连接，其权重代表将连接中的一部分作为输出的成本。加权图中的节点表示可能存在分支的点，这一般来说是房间。

最小生成树算法从单个点（通常是迷宫的入口）开始，并计算从该点扩展到图中所有节点的网络，以使树包括最小的总边际成本。