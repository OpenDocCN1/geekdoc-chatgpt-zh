```
Vertex dst = GetNearestVertex(dstObj);
return BuildPath(src.id, dst.id, ref routes[dst.id]);
}
```

你可能没有注意到，我们没有实现 BuildPath 方法，这是因为在 2.5 节已经讨论过。

# 2.8 用 A* 找到最优路径

A* 算法可能是路径查找中最常用的技术了，因为它容易实现、效率高，而且还有优化的余地。所以有一些算法把它作为基础并非巧合。另外，A* 与迪杰斯特拉算法具有同根性，所以你会发现它们实现上的相似之处。

# 准备工作

就像迪杰斯特拉算法，本节也使用 GPWiki 中的二叉堆实现。同样，理解它们代表什么以及它们的原理很重要。最后，我们进入启发式搜索，这意味着我们需要理解什么是启发式以及它的用处是什么。

简单来说，就本节的目的而言，启发式算法是一个用于计算两个顶点之间近似成本的函数，以便于比较其他结果并选择最小成本的结果。

我们需要对 Graph 类做一个小的修改：

+   1. 将成员变量定义为 delegate：

```
public delegate float Heuristic(Vertex a, Vertex b);
```

+   2. 实现欧几里得距离的函数，作为默认的启发式算法：

```
public float EuclidDist(Vertex a, Vertex b)
{
    Vector3 posA = a.transform.position;
    Vector3 posB = b.transform.position;
    return Vector3.Distance(posA, posB);
}
```

3. 实现曼哈顿距离的函数，作为另一个启发式算法。它将帮助我们比较使用不同启发式算法的结果：

```
public float ManhattanDist(Vertex a, Vertex b)
{
    Vector3 posA = a.transform.position;
    Vector3 posB = b.transform.position;
    return Mathf.Abs(posA.x - posB.x) + Mathf.Abs(posA.y - posB.y);
}
```

# 操作步骤

虽然本节定义了一个函数，但是请注意代码中的注释，以便更好地理解代码逻辑：