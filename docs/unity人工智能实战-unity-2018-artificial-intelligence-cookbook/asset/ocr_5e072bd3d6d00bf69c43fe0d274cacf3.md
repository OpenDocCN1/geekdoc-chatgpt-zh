# 准备工作

我们将使用网格表示图结构，对应地用布尔值表示单元格是否是墙体。

# 操作步骤

我们将把所有内容包含在一个类中，这个类管理抽象表示，命名中 DFSDungeon:

+   1. 定义 DFSDungeon 组件及其成员变量:

```
using UnityEngine;
using System.Collections.Generic;

public class DFS Dungeon : MonoBehaviour
{
    public int width;
    public int height;
    public bool[,] dungeon;
    public bool[,] visited;
    private Stack<Vector2> stack;
    private Vector2 current;
    private int size;

    // next steps
}
```

+   2. 定义初始化函数

```
private void Init()
{
    // next steps
}
```

+   3. 初始化要用到的变量和随机初始位置:

```
stack = new Stack<Vector2>();
size = width * height;
dungeon = new bool[height, width];
visited = new bool[height, width];
current.x = Random.Range(0, width - 1);
current.y = Random.Range(0, height - 1);
```

+   4. 把墙体赋值给地下城的每个单元:

```
int i, j;
for (j = 0; j < height; j++)
    for (i = 0; i < width; i++)
        dungeon[j, i] = true;
```

+   5. 把初始位置插入栈中:

```
stack.Push(current);
i = (int)current.x;
j = (int)current.y;
```

+   6. 把单元格标记为已访问，然后减小剩余单元格的数量: