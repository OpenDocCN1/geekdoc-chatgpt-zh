# 其他参考

+   见 2.6 节
+   见 2.8 节

# 6.7 基于图的嗅觉函数

在本节中，我们使用一种混合的方法来标记与给定气味粒子碰撞的顶点。

# 准备工作

顶点应该附加一个广泛的碰撞器，这样顶点就可以捕捉附近的气味粒子了。

# 操作步骤

+   1. 将下面的成员变量添加到气味粒子脚本中，以便保存其父 ID：

```
public int parent;
```

+   2. 创建新的启用气味的类，该类继承自原 vertex 类：

```
using UnityEngine;
using System.Collections;
using System.Collections.Generic;

public class VertexOdour : Vertex
{
    private Dictionary<int, OdourParticle> odourDic;
}
```

+   3. 在 Start 函数中初始化气味字典：

```
public void Start()
{
    odourDic = new Dictionary<int, OdourParticle>();
}
```

+   4. 将气味添加到顶点字典中：

```
public void OnCollisionEnter(Collision coll)
{
    OdourOdourParticle op;
    op = coll.gameObject.GetComponent<OdourParticle>();
    if (op == null)
        return;
    int id = op.parent;
    odourDic.Add(id, op);
}
```

+   5. 从顶点字典中移除气味：