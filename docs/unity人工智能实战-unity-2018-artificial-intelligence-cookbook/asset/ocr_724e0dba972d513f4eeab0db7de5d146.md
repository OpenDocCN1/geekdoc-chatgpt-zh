+   7. 开始构建用于计算势力的函数:

```
public void ComputeInfluenceSimple()
{
    VertexInfluence v;
    float dropOff;
    List<Vertex> pending = new List<Vertex>();
    List<Vertex> visited = new List<Vertex>();
    List<Vertex> frontier;
    Vertex[] neighbours;
}

    // 下面的步骤
```

+   8. 创建循环，用于遍历单位列表:

```
foreach(Unit u in unitList)
{
    Vector3 uPos = u.transform.position;
    Vertex vert = GetNearestVertex(uPos);
    pending.Add(vert);
}

    // 下面的步骤
```

+   9. 编写基于 BFS 的代码，用于根据半径到达的范围摊开势力:

```
// BFS用于指定势力
for (int i = 1; i <= u.radius; i++)
{
    frontier = new List<Vertex>();
    foreach (Vertex p in pending)
    {
        if (visited.Contains(p))
        continue;
        visited.Add(p);
        v = p as VertexInfluence;
        dropOff = u.GetDropOff(i);
        v.SetValue(u.faction, dropOff);
        neighbours = GetNeighbours(vert);
        frontier.AddRange(neighbours);
    }
    pending = new List<Vertex>(frontier);
}
```

# 运行原理

势力映射图与一般图的原理相同，因为只有两个额外的参数用于在图上映射势力，所以与基于势力的顶点相同。最关键的部分依赖于势力的计算，而这部分基于 BFS 算法。

对于地图上的每个单位，根据半径摊开其势力。当计算出的势力大于顶点原来的阵营时，顶点的阵营就改变了。

# 延伸阅读

根据特定的游戏需求，drop-off 函数应该可以优化。可以用下面的示例代码定义一个更