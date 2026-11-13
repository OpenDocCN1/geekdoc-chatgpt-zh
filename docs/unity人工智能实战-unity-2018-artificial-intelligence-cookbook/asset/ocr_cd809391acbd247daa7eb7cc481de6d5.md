```
```csharp
slotIndex = -1;
character = null;
}
}```
```

下面是 Character 类的代码：

```
```csharp
using UnityEngine;
using System.Collections;

public class Character : MonoBehaviour
{
    public Location location;

    public void SetTarget (Location location)
    {
        this.location = location;
    }
}```
```

# 操作步骤

我们要实现两个类：FormationPattern 和 FormationManager：

# 1. 创建 FormationPattern 伪抽象类：

```
```csharp
using UnityEngine;
using System.Collections;
using System.Collections.Generic;

public class FormationPattern : MonoBehaviour
{
    public int numOfSlots;
    public GameObject leader;
}```
```

# 2. 实现 Start 函数：

```
```csharp
void Start()
{
    if (leader == null)
        leader = transform.gameObject;
}```
```

# 3. 定义函数，根据槽的索引获取槽的位置：

```
```csharp
public virtual Vector3 GetSlotLocation(int slotIndex)
{
    return Vector3.zero;
}```
```

# 4. 定义函数，判断槽的数量是否受队形支持：

```
```csharp
public bool SupportsSlots(int slotCount)
{
    return slotCount <= numOfSlots;
}```
```