```
if (srcObj == null || dstObj == null)
    return new List<Vector>();
```

# 3. 声明并初始化算法需要用到的变量：

```
Vertex src = GetNearestVertex(srcObj.transform.position);
Vertex dst = GetNearestVertex(dstObj.transform.position);
Vertex[] neighbours;
Vertex v;
int[] previous = new int[vertices.Count];
for (int i = 0; i < previous.Length; i++)
    previous[i] = -1;
previous[src.id] = src.id;
Stack<Vertex> s = new Stack<Vertex>();
s.Push(src);
```

# 4. 实现用于查找路径的 DFS 算法：

```
while (s.Count != 0)
{
    v = s.Pop();
    if (ReferenceEquals(v, dst))
    {
        return BuildPath(src.id, v.id, ref previous);
    }

    neighbours = GetNeighbours(v);
    foreach (Vertex n in neighbours)
    {
        if (previous[n.id] != -1)
            continue;
        previous[n.id] = v.id;
        s.Push(n);
    }
}
```

# 运行原理

此算法基于 DFS 的迭代版本，也基于图的顺序遍历和使用栈遍历节点并添加发现的节点的后进先出原则。

# 延伸阅读

我们调用了 BuildPath 函数，但是并没有实现它，请注意这个函数几乎在本章中的每个寻路算法中都被调用，这也是为什么它不是本方法的一部分。

下面是 BuildPath 的代码：

```
private List<Vertex> BuildPath(int srcId, int dstId, ref int[] prevList)
{
    List<Vertex> path = new List<Vertex>();
    int prev = dstId;
    do
    {
        path.Add(vertices[prev]);
```