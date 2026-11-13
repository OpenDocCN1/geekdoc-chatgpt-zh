# 运行原理

此行为用两个半径值计算接下来的随机位置点、朝向那个随机点，然后把计算出的方向值转换成方向向量，如图 1-2 所示。

# 1.8 按路径移动

很多时候当我们需要像剧本一样的线路时，难以想象完全用代码实现。假如你正在开发一款潜行类游戏（stealth game）时，每个守卫的路线你都会用代码实现吗？这项技术将帮助你为这些情况构建一套灵活的寻路系统。

![](bbox=[0.64, 0.18, 0.95, 0.4])

# 准备工作

需要定义一个叫作 PathSegment 的自定义数据类型：

```
using UnityEngine;
using System.Collections;

public class PathSegment
{
    public Vector3 a;
    public Vector3 b;

    public PathSegment () : this (Vector3.zero, Vector3.zero) {}
    public PathSegment (Vector3 a, Vector3 b)
    {
        this.a = a;
        this.b = b;
    }
}
```

# 操作步骤

该方法有两大步骤。首先，构建 Path 类，它从特定的空间表达式中提取路径中的点位；然后构建 PathFollower 行为，利用提取的点位得到实际要跟踪的空间点位：

+   1. 创建 Path 类，包含节点和线段，但只有节点是公共的，并且需要手动赋值：

```
using UnityEngine;
using System.Collections;
using System.Collections.Generic;

public class Path : MonoBehaviour
{
    public List<GameObject> nodes;
    List<PathSegment> segments;
}
```