# ## 5.4 用高度分析路径点

本节我们根据路径点的位置来评估它。从策略上讲，更低的位置处于劣势。在这个例子中，我们要用一个灵活的算法，根据路径点附近的高度计算路径点的质量。

# ## 准备工作

本节十分简单，所以读者不需要了解额外的知识。本算法能够足够灵活地接收一个位置点的列表，这些位置点由路径点的邻接点或直接由路径点的完全图给出。关于附近高度的启发式算法留给我们去研究，它提供了游戏的特定设计。

# ## 操作步骤

我们要实现一个函数，根据其高度和附近点位评估一个位置点：

+   1. 声明用于计算质量值的函数：

```
```csharp
public static float GetHeightQuality (Vector3 location, Vector3[] surroundings)
{
    // 下面的步骤
}
```
```

+   2. 初始化用于处理计算过程的变量：

```
```csharp
float maxQuality = 1f;
float minQuality = -1f;
float minHeight = Mathf.Infinity;
float maxHeight = Mathf.NegativeInfinity;
float height = location.y;
```
```

+   3. 遍历附近的点位，找出最大高度和最小高度：

```
```csharp
foreach (Vector3 s in surroundings)
{
    if (s.y > maxHeight)
        maxHeight = s.y;
    if (s.y < minHeight)
        minHeight = s.y;
}
```
```

+   4. 计算给定范围内的质量：

```
```csharp
float quality = (height-minHeight) / (maxHeight - minHeight);
quality *= (maxQuality - minQuality);
quality += minQuality;
return quality;
```
```

# ## 运行原理

我们遍历附近点位的列表，目的是找到最大宽度和最小宽度，然后计算 -1 到 1 范围内