```
{
    public List<Transition> transitions;
}
```

# 4. 实现 Awake 函数，代码如下：

```
public virtual void Awake()
{
    transitions = new List<Transition>() ;
    // TO-DO
    // 在这里设置你的transitions
}
```

# 5. 定义初始化函数，代码如下：

```
public virtual void OnEnable()
{
    // TO-DO
    // 在这里初始化状态
}
```

# 6. 定义析构函数，代码如下：

```
public virtual void OnDisable()
{
    // TO-DO
    // 在这里销毁状态
}
```

# 7. 定义 Update 函数，用于对当前状态做出合理的行为：

```
public virtual void Update()
{
    // TO-DO
    // 在这里编写行为逻辑
}
```

# 8. 实现 LateUpdate 函数，用于决策下一步启用哪个状态：

```
public void LateUpdate()
{
    foreach (Transition t in transitions)
    {
        if (t.condition.Test())
        {
            t.target.enabled = true;
            this.enabled = false;
            return;
        }
    }
}
```

# 运行原理

每个状态都是一个 MonoBehaviour 脚本，而根据这个状态来自哪个转移来决定启用还是禁用这个脚本。我们使用 LateUpdate 函数是为了不改变编写行为的惯用思路，而且