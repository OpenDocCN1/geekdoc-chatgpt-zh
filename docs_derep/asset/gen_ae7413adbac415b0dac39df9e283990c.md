# 图解 Dijkstra 寻路核心逻辑与数据结构定义

### 算法实现：寻找最短路径

```python
function pathfindDijkstra(graph: Graph, start: Node, end: Node) -> Connection[]:

    # 内部记录类：追踪单个节点的搜索状态
    class NodeRecord:
        node: Node           # 当前节点引用
        connection: Connection # 到达此节点的入边
        costSoFar: float     # 从起点累计的实际代价

    # 1. 启动初始化：将起点纳入搜索视野
    startRecord = new NodeRecord()
    startRecord.node = start
    startRecord.connection = null
    startRecord.costSoFar = 0

    # 维护两个核心集合
    open = new PathfindingList()   # 待探索节点（按代价排序）
    open += startRecord
    closed = new PathfindingList() # 已确定最优路径的节点

    # 2. 主循环：逐个处理代价最低的节点
    while length(open) > 0:
        # 提取当前代价最小的记录
        current: NodeRecord = open.smallestElement()

        # 终止条件：抵达终点
        if current.node == goal:
            break

        # 遍历当前节点的所有出边
        connections = graph.getConnections(current)
        for connection in connections:
            endNode = connection.getToNode()
            endNodeCost = current.costSoFar + connection.getCost()

            # 剪枝 A：目标已在封闭集，说明最优路已定，跳过
            if closed.contains(endNode):
                continue

            # 剪枝 B：目标在开放集，但新路径不优于旧记录
            else if open.contains(endNode):
                endNodeRecord = open.find(endNode)
                if endNodeRecord.cost <= endNodeCost:
                    continue

            # 发现新节点 或 找到更优路径：创建/更新记录
            else:
                endNodeRecord = new NodeRecord()
                endNodeRecord.node = endNode

            # 统一更新代价与入边信息
            endNodeRecord.cost = endNodeCost
            endNodeRecord.connection = connection

            # 若为新发现节点，加入开放集待后续处理
            if not open.contains(endNode):
                open += endNodeRecord

        # 当前节点探索完毕，转移至封闭集
        open -= current
        closed += current

    # 3. 结果判定与路径重构
    if current.node != goal:
        # 开放集耗尽仍未触达终点，路径不存在
        return null
    else:
        # 回溯构建路径：从终点沿入边倒推至起点
        path = []
        while current.node != start:
            path += current.connection
            current = current.connection.getFromNode()
        # 反转得到正向路径
        return reverse(path)
```

[PRE0]
### 辅助数据结构：连接（边）定义

```python
class Connection:
    cost: float
    fromNode: Node
    toNode: Node

    function getCost() -> float:
        return cost

    function getFromNode() -> Node:
        return fromNode

    function getToNode() -> Node:
        return toNode
```