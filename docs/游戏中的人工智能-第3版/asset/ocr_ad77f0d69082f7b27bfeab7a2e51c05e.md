```
9    buildingGraph: Graph
10
11    class Buildings extends HashMap<Node -> EntranceNodeAssignment[]>:
12        # 返回包含给定节点的图形
13        function getBuilding(node) -> Graph
14
15        # 保存入口节点分配
16        buildingInstances: Buildings
17
18        function getConnections(fromNode: Node) -> Connection[]:
19        # 检查 fromNode 是否在任意建筑物实例的范围中
20        building = buildingInstances.getBuilding(fromNode)
21
22        # 如果有建筑物，则委托给该建筑物
23        if building:
24            return building.getConnections(fromNode)
25
26        # 否则，委托给世界图形
27        connections = worldGraph.getConnections(fromNode)
28
29        # 添加此节点的每个入口的连接
30        for building in buildingInstances[fromNode]:
31            connection = new Connection
32            connection.fromNode = fromNode
33            connection.toNode = building.toInstanceNode
34            connection.cost = 0
35            connections += connection
36
37        return connections
```

# 3. 数据结构和接口

在实例图形类中，我们将访问作为哈希的出口节点，按节点编号索引并返回出口节点分配列表。每次要求图形连接时都会调用此过程，因此需要以尽可能高效的方式实现。世界图形类中的建筑物实例结构以完全相同的方式使用，具有相同的效率要求。

建筑物实例结构在上面的伪代码中也有一个 getBuilding 方法。如果节点是实例图形的一部分，则此方法接受节点并从列表返回建筑物实例。如果节点是主路径发现图形的一部分，则该方法返回空值。这种方法也具有很高的速度要求。但是，由于每个建筑物都使用了一系列的节点值，因此无法轻松将其实现为哈希表。一个好的解决方案是在建筑物的 nodeOffsets 上执行二分搜索。使用相干性还可以进一步加速，也可以利用这样的