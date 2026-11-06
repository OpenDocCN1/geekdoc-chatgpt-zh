| 常见操作 | 说明 | 示例 |
|---|---|---|
| dict1[key] | 访问字典中键对应的值 | dict1['Name'] ⇒ Bob |
| dict1.get(key) | 访问字典中键对应的值 | dict1.get("Name")⇒ Bob |
| dict1[newkey]=value | 为字典增加一项，增加新的键值对 | dict1["gender"]='M'⇒dict1={'Age': 21, 'gender': 'M', 'Name': 'Bob'} |
| dict1[key]=newvalue | 增加新的键-值对 | dict1["Name"]="Bill" ⇒ dict1={'Age': 21, 'gender': 'M', 'Name': 'Bill'} |
| dict1.pop(key [,default]) | 删除字典给定键所对应的值 | dict1.pop("Age")⇒ {'Name': 'Bill', 'gender': 'M'} |
| dict1.values() | 遍历字典的值，以列表返回一个字典所有的值 | dict1.values()⇒dict_values(['Bill', 'M']) |
| dict1.keys() | 遍历字典的键，以列表返回一个字典所有的键 | dict1.keys()⇒dict_keys(['Name', 'gender']) |
| dict1.items() | 遍历字典的项，以列表返回可遍历的（键、值）元组数组 | dict1.items()⇒dict_items([('Name', 'Bill'), ('gender', 'M')]) |
| key in dict | 如果键在字典 dict 里，返回 True，否则返回 False | 'Name' in dict1⇒True |
| key not in dict | 如果键不在字典 dict 里，返回 True，否则返回 False | 'Name' not in dict1⇒False |

以上列举了常见的字典操作。字典在 Python 中扮演着重要的角色，如果你想成为一名合格的 Python 使用者，不仅需要熟练掌握列表、元组与字典的基本操作，还需要对其比较复杂情况的处理有一定了解。比如在字典操作中出现镶嵌字典形式，只需要依据以上的基本操作进行扩展即可。

小贴士：
在 Python 中，字典中的键必须是唯一的，但值不必唯一。

# 2.2.4 条件语句与循环语句

# 1. 条件语句

Python 中的条件语句一般是指 if 语句，if 语句用于改变 Python 程序中的控制流。通过指定条件的真假结果，可以判断要执行哪条语句。Python 中 if 语句的最简单形式如例 2-11 所示。

# 【例 2-11】if 语句基本形式

```
if condition1:
    statement1
else:
    statement2
```

在 if…else 条件句中，if 条件判断语句所在行和 else 所在行都要以冒号结尾，而执行语句要缩进。从上述基本形式可以看出，condition1 后面有冒号，statement1 所在行有缩进，同时其后面是没有冒号的。如果表达式 condition1 执行结果为 True，接下来执行 statement1 的代码，