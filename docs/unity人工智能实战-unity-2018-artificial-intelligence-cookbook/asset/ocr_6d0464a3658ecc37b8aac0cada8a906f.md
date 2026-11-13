# 其他参考

请参考本章中的以下内容：

+   见 2.7 节

+   见 2.5 节

关于 Delegates 的更多信息，请参阅网站上的官方文档：

https://unity3d.com/learn/tutorials/modules/intermediate/scripting/
delegates

# 2.9 改进 A* 算法的内存占用：IDA*

IDA* 是一个叫作迭代深化 DFS（Iterative Deepening Depth-First Search）算法的变种，内存使用量比 A* 小，因为它不使用数据结构存储找到的和访问过的节点。

# 准备工作

学习本节之前，最好对递归原理有所了解。

# 操作步骤

本书较长，可以分成两大步骤：创建主函数和创建内部的递归函数。请注意代码中的注释，以便更好地理解代码逻辑：

+   1. 定义主函数 GetPathIDAstar:

```
public List<Vertex> GetPathIDAstar(GameObject srcObj, GameObject
dstObj, Heuristic h = null)
{
    if (srcObj == null || dstObj == null)
    return new List<Vertex> ();
    if (ReferenceEquals(h, null))
    h = EuclidDist;
    // next steps;
}
```

+   2. 声明并计算算法中要用到的变量：

```
List<Vertex> path = new List<Vertex> ();
Vertex src = GetNearestVertex(srcObj.transform.position);
Vertex dst = GetNearestVertex(dstObj.transform.position);
Vertex goal = null;
bool[] visited = new bool[vertices.Count];
for (int i = 0; i < visited.Length; i++)
visited[i] = false;
visited[src.id] = true;
```

+   3. 实现算法循环：