# 4.14 import：深入讨论

如果已经使用下面的语句导入了模块（例如math和random模块）：

```
import module_name
```

就可以通过每个模块的名称和一个点（.）访问包含在模块中的函数。
如果使用下面的代码从模块中导入特定的标识符（例如decimal模块的Decimal类型）：

```
from module_name import identifier
```

则可以直接使用该标识符，而不必在其前面加上模块名称和点（.）。

# 从一个模块导入多个标识符

使用from...import语句，可以从模块中导入以逗号分隔的标识符列表，然后在代码中直接使用它们，而不必在它们之前添加模块名和点（.）：

```
In [1]: from math import ceil, floor

In [2]: ceil(10.3)
Out[2]: 11

In [3]: floor(10.7)
Out[3]: 10
```

尝试使用未导入的函数会引发NameError，表示名称未定义。

# 警告：避免使用通配符

可以使用通配符导入模块中定义的所有标识符，如下所示：

```
from modulename import *
```