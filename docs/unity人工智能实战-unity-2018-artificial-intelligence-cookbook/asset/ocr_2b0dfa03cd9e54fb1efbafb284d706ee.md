```
}
store[s][a] = val;
}
```

# 6. 继续创建 QLearning 类，用于运行算法：

```
using UnityEngine;
using System.Collections;

public class QLearning : MonoBehaviour
{
    public QValueStore store;
}
```

# 7. 定义函数，用于从一个集合中获取随机行动：

```
private GameAction GetRandomAction(GameAction[] actions)
{
    int n = actions.Length;
    return actions[Random.Range(0, n)];
}
```

# 8. 实现学习函数，建议分成几个步骤。从定义开始，要注意这是一个协程：

```
public IEnumerator Learn(
    ReinforcementProblem problem,
    int numIterations,
    float alpha,
    float gamma,
    float rho,
    float nu)
{
    // 下面的步骤
}
```

# 9. 验证保存列表是否已经初始化：

```
if (store == null)
    yield break;
```

# 10. 获取一个随机状态：

```
GameState state = problem.GetRandomState();
for (int i = 0; i < numIterations; i++)
{
    // 下面的步骤
}
```

# 11. 返回 null，目的是让当前帧继续运行：

```
yield return null;
```

# 12. 验证随机值的有效性：

```
if (Random.value < nu)
    state = problem.GetRandomState();
```

# 13. 从当前游戏状态获取可用的行动：