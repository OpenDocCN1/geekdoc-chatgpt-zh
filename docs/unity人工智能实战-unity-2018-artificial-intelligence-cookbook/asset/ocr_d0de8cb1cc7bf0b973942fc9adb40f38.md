```
AdjustSpeed(targetPosition);
}
```

# 4. 定义根据位置相应地调整速度的函数:

```
public virtual void AdjustSpeed(Vector3 targetPosition)
{
    // TODO
    // 这里是你的行为代码
}
```

# 5. 创建用于管理幽灵驾驶员或无敌竞速者的类:

```
using UnityEngine;

public class RacingCenter : RacingRival
{
    public GameObject player;
}
```

# 6. 实现初始函数，用于找到目标:

```
void Start()
{
    player = GameObject.FindGameObjectWithTag("Player");
}
```

# 7. 重写 Update 函数，这样无敌汽车就可以适应玩家的行为了:

```
public override void Update()
{
    Vector3 playerPos = player.transform.position;
    float dist = Vector3.Distance(transform.position,
    playerPos);
    if (dist > distanceThreshold)
    {
        targetPosition = player.transform.position;
        base.Update();
    }
}
```

# 8. 实现特有的行为:

```
public override void AdjustSpeed(Vector3 targetPosition)
{
    // TODO
    // 当也应用基础行为的情况下使用
    base.AdjustSpeed(targetPosition);
}
```

# 9. 创建用于管理上层 AI 的类:

```
using UnityEngine;

public class Rubberband : MonoBehaviour
{
    RacingCenter ghost;
```