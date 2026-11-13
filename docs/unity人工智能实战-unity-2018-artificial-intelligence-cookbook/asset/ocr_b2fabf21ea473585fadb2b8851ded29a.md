路径点根据赋给它们的值与其他路径点竞争，这意味着相关性高的路径点会胜出。

# 准备工作

我们要处理静态成员函数，要理解静态函数的意义和用法。

# 操作步骤

我们要创建 Waypoint 类，并添加用于凝结路径点集合的方法：

+   1. 创建 Waypoint 类，该类继承于 MonoBehaviour 类和 IComparer 接口：

```
using UnityEngine;
using System.Collections;
using System.Collections.Generic;

public class Waypoint : MonoBehaviour, IComparer
{
    public float value;
    public List<Waypoint> neighbours;
}
```

+   2. 实现接口中的 Compare 函数：

```
public int Compare(object a, object b)
{
    Waypoint wa = (Waypoint)a;
    Waypoint wb = (Waypoint)b;
    if (wa.value == wb.value)
        return 0;
    if (wa.value < wb.value)
        return -1;
    return 1;
}
```

+   3. 实现 static 函数，用于检测 agent 是否能够从一个路径点到达另一个：

```
public static bool CanMove(Waypoint a, Waypoint b)
{
    // implement your own behaviour for
    // deciding whether an agent can move
    // easily between two waypoints
    return true;
}
```

+   4. 定义函数，用于凝结路径点：

```
public static void CondenseWaypoints(List<Waypoint> waypoints,
    float distanceWeight)
{
    // next steps
}
```

+   5. 初始化变量，对路径点进行逆向排序：

```
distanceWeight *= distanceWeight;
```