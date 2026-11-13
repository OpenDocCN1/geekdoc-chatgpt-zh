# 2. 定义 Start 函数来在场景开始时给线段赋值:

```
void Start()
{
    segments = GetSegments();
}
```

# 3. 定义 GetSegments 函数来从节点构建线段:

```
public List<PathSegment> GetSegments ()
{
    List<PathSegment> segments = new List<PathSegment>();
    int i;
    for (i = 0; i < nodes.Count - 1; i++)
    {
        Vector3 src = nodes[i].transform.position;
        Vector3 dst = nodes[i+1].transform.position;
        PathSegment segment = new PathSegment(src, dst);
        segments.Add(segment);
    }
    return segments;
}
```

# 4. 定义第一个函数 GetParam，用于提取点位:

```
public float GetParam(Vector3 position, float lastParam)
{
    // 函数体
}
```

# 5. 找出离 agent 最近的线段:

```
float param = 0f;
PathSegment currentSegment = null;
float tempParam = 0f;
foreach (PathSegment ps in segments)
{
    tempParam += Vector3.Distance(ps.a, ps.b);
    if (lastParam <= tempParam)
    {
        currentSegment = ps;
        break;
    }
}
if (currentSegment == null)
    return 0f;
```

# 6. 根据当前位置，我们需要计算出路径的走向:

```
Vector3 currPos = position - currentSegment.a;
Vector3 segmentDirection = currentSegment.b - currentSegment.a;
segmentDirection.Normalize();
```

# 7. 使用向量投影找到线段中的点:

```
Vector3 pointInSegment = Vector3.Project(currPos,
    segmentDirection);
```