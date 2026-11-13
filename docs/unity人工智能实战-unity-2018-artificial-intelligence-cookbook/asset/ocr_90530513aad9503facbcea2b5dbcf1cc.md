# 1.10 避开墙体

本节中要实现的行为效仿人类的能力，当我们面前有一堵墙或者障碍时，通过设置安全距离去避开墙体，同时试着不偏离大方向。

# 准备工作

这项技术使用物理引擎中的 RaycastHit 结构体和 Raycast 函数，所以建议读者复习一下相关文档，以便快速理解本节的主题。

# 操作步骤

得益于之前内容的介绍，该方法的介绍会比较短：

+   1. 创建继承于 Seek 的 AvoidWall 行为：

```
using UnityEngine;
using System.Collections;

public class AvoidWall : Seek
{
    // 类内部
}
```

+   2. 添加用于定义安全距离和射线长度的成员变量：

```
public float avoidDistance;
public float lookAhead;
```

+   3. 定义 Awake 函数，用于初始化游戏对象：

```
public override void Awake()
{
    base.Awake();
    target = new GameObject();
}
```

+   4. 定义后面的步骤要用到的 GetSteering 函数：

```
public override Steering GetSteering()
{
    // 函数体
}
```

+   5. 声明并设置射线所需要的变量：

```
Steering steering = new Steering();
Vector3 position = transform.position;
Vector3 rayVector = agent.velocity.normalized * lookAhead;
Vector3 direction = rayVector;
RaycastHit hit;
```

+   6. 发射射线并在射线遇到墙体时做出合理的计算：