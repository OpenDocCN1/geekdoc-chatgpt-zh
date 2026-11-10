[PRE0]

### （2）列表常用方法

下面以表 2-4 为索引，归纳了 Python 列表最常用的内置操作。

表 2-4 列表函数和方法

| 基本操作 | 说 明 | 示 例 |
|---|---|---|
| len(list) | 计算列表包含的元素总数 | len(list2) ⇒ 2 |
| max(list) | 找出列表中的最大项 | max(list2) ⇒ 4 |
| min(list) | 找出列表中的最小项 | min(list2) ⇒ 1 |
| list.append(obj) | 在尾部追加一个新对象 | list2.append('a') ⇒ list2=[1, 2, 3, 4, 3, 'a'] |
| list.count(obj) | 统计指定元素出现的频次 | list2.count(3) ⇒ 2 |
| list.extend(seq) | 把另一个序列的所有元素一次性接在末尾 | list1.extend(list2) ⇒ list1=['Python', 'AI', 1, 2, 3, 4, 3] |
| list.index(obj) | 查找元素首次出现的下标 | list2.index(3) ⇒ 2 |
| list.insert(index, obj) | 在指定下标位置前插入新元素 | list2.insert(2,"a") ⇒ list2=[1, 2, 'a', 3, 4, 3] |
| list.pop([index=-1]) | 弹出并返回一个元素，默认最后一个 | list2.pop(1) ⇒ list2=[1, 3, 4, 3] |
| list.remove(obj) | 删除首次匹配到的指定值 | list2.remove(3) ⇒ list2=[1, 2, 4, 3] |
| list.reverse() | 将元素顺序反转 | list2.reverse()⇒list2=[3, 4, 3, 2, 1] |
| list.sort(cmp=None, key=None, reverse=False) | 对原列表就地排序 | list2.sort()⇒list2=[1, 2, 3, 3, 4] |
| list.clear() | 彻底清空列表内容 | list2.clear()⇒list2=[] |
| list.copy() | 生成列表的浅拷贝 | list2.copy()⇒[1, 2, 3, 4, 3] |

注：当对象并非 str、ascii 或 repr 形式时，可借助 !s、!a、!r 分别将其转为字符串、ASCII 表示或 repr 表示。

### 2. 元组

列表擅长承载运行期间会变动的数据集，比如用户清单或游戏角色信息，因为它可被修改。而当需要固定不变的一组元素时，元组更合适。Python 把不可变对象称为 immutable，元组正是不可变的序列。

元组与列表同属序列，区别在于内容一旦确定就不可更改。Python 用圆括号 () 并以逗号分隔来创建元组。

[PRE1]

元组内部的值不能被修改或删除，不过可以进行拼接组合。

[PRE2]

元组的内置函数与列表基本一致，只需把 list 换成 tuple 即可使用，两者在类型上也可以互相转换：列表转元组用 tuple(list)，元组转列表用 list(tuple)。相较列表，元组结构更简洁稳固，适合在程序生命周期内保存一组恒定不变的值。

### 小贴士：

单元素元组必须在元素后加逗号，否则括号会被当作分组符。如 tup1 = (1) 类型为 int，而 tup2 = (1,) 才为元组。

### 3. 序列操作

列表与元组都属于序列，支持统一的序列操作。下面以列表举例，见表 2-5。

表 2-5 序列操作

| 操作 | 示例 |
|---|---|
| 索引 | list2[0] ⇒1 list2[-1] ⇒3 |
| 切片访问，语法为[下限:上限:步长]，步长默认 1 | list2[1:3] ⇒[2, 3] list2[0:3:2] ⇒[1, 3] |
| 赋值 | list2[1]="a" ⇒list2=[1, 'a', 3, 4, 3] |
| 序列相加 | list1+list2 ⇒['Python', 'AI', 1, 2, 3, 4, 3] |
| 序列乘法 | list1*3 ⇒['Python', 'AI', 'Python', 'AI', 'Python', 'AI'] |
| 遍历 | for a in list1: print(a) ⇒ Python AI |

### 4. 字典

本节介绍 Python 中用于关联信息的键值结构。字典由多个键值对组成，通过键即可取到对应的值。值可以是任意 Python 对象，包括数字、字符串、列表甚至另一个字典。

字典用花括号 {} 表示，元素格式为 key:value，多组之间用逗号分隔，如 dict={key1:value1,key2: value2, key3:value3}。实例：dict1 = {'Name': 'Bob','Age': 21}。

对字典的基本操作见表 2-6。

表 2-6 字典基本操作

| 常见操作 | 说明 | 示例 |
|---|---|---|
| len(dict) | 统计键的总数 | len(dict1) ⇒2 |
| str(dict) | 得到字典的字符串形式 | str(dict1) ⇒{'gender': 'M', 'Name': 'Bill'} 结果类型为字符串 |
| dict1[key] | 用方括号取值 | dict1['Name'] ⇒ Bob |
| dict1.get(key) | 安全取值 | dict1.get("Name")⇒ Bob |
| dict1[newkey]=value | 新增键值对 | dict1["gender"]='M'⇒dict1={'Age': 21, 'gender': 'M', 'Name': 'Bob'} |
| dict1[key]=newvalue | 修改已有键的值 | dict1["Name"]="Bill" ⇒ dict1={'Age': 21, 'gender': 'M', 'Name': 'Bill'} |
| dict1.pop(key [,default]) | 删除指定键及其值 | dict1.pop("Age")⇒ {'Name': 'Bill', 'gender': 'M'} |
| dict1.values() | 返回所有值的视图 | dict1.values()⇒dict_values(['Bill', 'M']) |
| dict1.keys() | 返回所有键的视图 | dict1.keys()⇒dict_keys(['Name', 'gender']) |
| dict1.items() | 返回键值对的视图 | dict1.items()⇒dict_items([('Name', 'Bill'), ('gender', 'M')]) |
| key in dict | 判断键是否存在 | 'Name' in dict1⇒True |
| key not in dict | 判断键是否不存在 | 'Name' not in dict1⇒False |

以上为字典的常用用法。字典在 Python 中极为核心，熟练掌握列表、元组、字典的基础操作以及复杂场景的处理是成为熟练使用者的重要前提。例如嵌套字典，只需在此基础上层层扩展即可。

小贴士：

字典的键必须唯一，值可以重复。

## 2.2.4 条件语句与循环语句

### 1. 条件语句

Python 的条件控制主要由 if 语句实现，用于根据条件真假改变程序走向。最简 if 结构见例 2-11。

### 【例 2-11】if 语句基本形式

[PRE3]

在 if…else 结构中，if 行和 else 行均以冒号结尾，执行体需要缩进，且不带冒号。若 condition1 为真则执行 statement1，否则执行 statement2。若条件分支增多，可使用 if…elif…else，多个 elif 允许存在，else 只能有一个且置于末尾。见例 2-12 的形式，例 2-13 给出具体演示。

### 【例 2-12】 if 多条件语句

[PRE4]

### 【例 2-13】 if 多条件案例

输入：

[PRE5]

输出：

正数

Python 还提供三元运算符，将条件判断压缩到一行。其形式见例 2-14，案例见例 2-15。

### 【例 2-14】 三元运算

[PRE6]

例 2-14 的含义是：条件为真时返回 [on_true]，为假时返回 [on_false]。

### 【例 2-15】 三元运算案例

输入：

[PRE7]

输出：

3

## 2. 循环语句

循环用于重复执行一段逻辑，Python 提供 while 和 for 两种形式。图 2-6 与 2-7 分别展示流程。

[PRE8]

▲图 2-6 while 循环流程图

[PRE9]

▲图 2-7 for 循环流程图

while 循环形式见例 2-16。

### 【例 2-16】while 循环语句的形式

[PRE10]

statements 为循环体，可为单行或区块；expression 为判断条件，成立时进入循环。

for 循环用于迭代，形式见例 2-17。

### 【例 2-17】for 循环语句的形式

[PRE11]

### 2.2.5 break、continue、pass

在实际工程中，常需借助 break、continue、pass 灵活控制流程，如例 2-17 所示。

### 【例 2-18】构建循环

[PRE12]

输入：

[PRE13]

输出：

[PRE14]

break 可强制跳出当前循环，即使条件未满足或序列未遍历完也会终止。break 后面的语句不再执行，适用于 while 与 for。例 2-18 为正常循环，例 2-18.1 演示 break。

### 【例 2-18.1】break 语句

输入：

[PRE15]

输出：

[PRE16]

循环在 i=3 时触发 break，仅输出 0、1、2 后退出。break 的作用是彻底结束当前循环。

continue 表示跳过本次循环剩余部分，进入下一轮。同样适用于 while 与 for。例 2-18.2 演示 continue。

### 【例 2-18.2】 continue 语句

输入：

[PRE17]

输出：

[PRE18]

循环在 j=3 时被跳过，但后续迭代继续。continue 仅中断本次迭代，不退出循环。

pass 是空语句，用于保持结构完整性，不执行任何操作，常作占位。如例 2-19。

### 【例 2-19】 pass 语句

[PRE19]

sample 函数中用 pass 占位，避免空函数报错。待实现逻辑明确后再填充。

## 2.3 Python 高级操作

### 2.3.1 lambda

lambda 用于在表达式内部创建匿名函数。语法限制函数体只能是单一表达式，不允许赋值或使用 while、try 等语句。

Python 通过 lambda 表达式生成匿名函数。

- lambda 只是表达式，体比 def 简洁。
- 主体只能是表达式，不能包含完整代码块。
- lambda 拥有独立命名空间，无法访问参数列表外的局部或全局变量。
- lambda 并非 C/C++ 的内联函数，其目的不是提升运行效率。

一般语法：lambda argument_list: expression。可将结果赋给变量。例 2-20 返回两参数之和。

### 【例 2-20】lambda 函数案例

输入：

[PRE20]

输出：

7

例 2-20 的功能也可通过常规函数定义实现。

[PRE21]