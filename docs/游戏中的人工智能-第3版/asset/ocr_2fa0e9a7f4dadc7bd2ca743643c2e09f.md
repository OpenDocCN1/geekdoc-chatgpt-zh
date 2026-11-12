这个伪代码将 sets 变量视为列表字典（当它基于列表的值添加示例时）和列表的列表（当它在末尾返回变量时）。当它用作字典时，需要注意在尝试将当前示例添加到字典之前，应将先前未使用的条目初始化为空列表。

尽管对数据结构的需求经常发生，但这种二元性并不是数据结构的普遍支持要求。当然，它的实现其实非常容易。

# 7. 熵

entropy 函数具有以下形式：

```
function entropy(examples):
    # 获取示例的数量
    exampleCount = examples.length()
    # 检查是否仅有 1 个示例，如果是，则熵为 0
    if exampleCount == 0:
        return 0
    # 否则，需要记录每种不同类型
    # 动作的数量
    actionTallies = {}
    # 遍历每个示例
    for example in examples:
        # 递增计数
        actionTallies[example.action]++
    # 现在已经拥有集合中每个动作的计数
    actionCount = actionTallies.length()
    # 如果仅有一个动作，则熵为 0
    if actionCount == 0:
        return 0
    # 从熵为 0 开始
    entropy = 0
    # 添加到每个动作的熵贡献中
    for actionTally in actionTallies:
        proportion = actionTally / exampleCount
        entropy -= proportion * log2(proportion)
```