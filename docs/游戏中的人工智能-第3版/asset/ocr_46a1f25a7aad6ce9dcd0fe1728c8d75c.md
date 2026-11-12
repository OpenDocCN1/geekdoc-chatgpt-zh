特别是，matches 函数正在承担主要负担。在给出用于规则匹配的伪代码之前，我们需要了解数据库的实现方式以及规则的 IF 子句如何对其进行操作。

# 1. 数据库

数据库可以只是数据项的列表或数组，由 DataNode 类表示。数据库中的 DataGroups 包含其他数据节点，因此整个数据库变成了信息树。

该树中的每个节点都具有以下基本结构：

```
class DataNode:
    identifier: string
```

非叶节点对应于数据中的数据组，并具有以下形式：

```
class DataGroup extends DataNode:
    children: DataNode[]
```

树中的叶节点包含实际值，并具有以下形式：

```
class Datum extends DataNode:
    value: any
```

数据组的子节点可以是任何数据节点，它要么是另一个数据组，要么是一个 Datum。为清楚起见，我们将假设某种形式的多态性，尽管实际上会将其实现为组合所有 3 种结构的数据成员的单一结构，并且往往效果更好。

# 2. 规则

规则具有以下结构：

```
class Rule:
    ifClause: Match
    function getActions(bindings: Bindings)
```

ifClause 可用于匹配数据库，详见下文说明。getActions 函数可以执行任何所需的操作，包括修改数据库的内容。它采用了一个绑定列表，该列表包含了数据库中与 IF 子句中的任何通配符匹配的项。

# 3. IF 子句

IF 子句由一组数据项组成，其格式与数据库中的数据类似，由布尔运算符连接。它们需要能够匹配数据库，因此可以使用通用数据结构作为 IF 子句中元素的基类：