

# 族谱关系分析

```
import json
from logpy import Relation, facts, run, conde, var, eq
```

定义一个函数来检查 `x` 是否是 `y` 的父级。 我们将使用以下逻辑：如果 `x` 是 `y` 的父母，则 `x` 是父亲或母亲。 我们已经在事实基础中定义了 `father` 和 `mother`：

```
# Check if 'x' is the parent of 'y'
def parent(x, y):
    return conde([father(x, y)], [mother(x, y)])
```

定义一个函数来检查 `x` 是否为 `y` 的祖父母。 我们将使用以下逻辑：如果 `x` 是 `y` 的祖父母，则 `x` 的后代将是 `y` 的父代：

```
# Check if 'x' is the grandparent of 'y'
def grandparent(x, y):
    temp = var()
    return conde((parent(x, temp), parent(temp, y)))
```

定义一个函数来检查 `x` 是否是 `y` 的兄弟。 我们将使用以下逻辑：如果 `x` 是 `y` 的兄弟，则 `x` 和 `y` 将具有相同的父代。 请注意，此处需要进行一些修改，因为当我们列出 `x` 的所有同级时，也会列出 `x`，因为 `x` 满足这些条件。 因此，当我们打印输出时，我们将不得不从列表中删除 `x`。 我们将在 `main` 函数中对此进行讨论：

```
# Check for sibling relationship between 'a' and 'b'
def sibling(x, y):
    temp = var()
    return conde((parent(temp, x), parent(temp, y)))
```

定义一个函数以检查 `x` 是否是 `y` 的叔叔。 我们将使用以下逻辑：如果 `x` 是 `y` 的叔叔，那么 `x` 的祖父母将与 `y` 的父母相同。 请注意，此处需要进行一些修改，因为当我们列出 `x` 的所有叔叔时，也会列出 `x` 的父亲，因为 `x` 的父亲满足了这些条件。 因此，当我们打印输出时，我们将不得不从列表中删除 `x` 的父亲。 我们将在 `main` 函数中对此进行讨论：

```
# Check if x is y's uncle
def uncle(x, y):
    temp = var()
    return conde((father(temp, x), grandparent(temp, y)))
```

定义 `main` 函数并初始化关系 `father` 和 `mother`：

```
if __name__=='__main__':
    father = Relation()
    mother = Relation()
```

从 `relationships.json` 文件中加载数据：

```
with open('relationships.json') as f:
    d = json.loads(f.read())
```

读取数据并将其添加到事实库：

```
for item in d['father']:
    facts(father, (list(item.keys())[0], list(item.values())[0]))
```

```
for item in d['mother']:
    facts(mother, (list(item.keys())[0], list(item.values())[0]))
```

定义变量 `x`：

```
x = var()
```

现在，我们准备提出一些问题，看看求解器能否提出正确的答案。 让我们问一下约翰的孩子是谁：

```
# John's children
name = 'John'
output = run(0, x, father(name, x))
print("\nList of " + name + "'s children:")
for item in output:
    print(item)
```

威廉的母亲是谁？

```
# William's mother
name = 'William'
output = run(0, x, mother(x, name))[0]
print("\n" + name + "'s mother:\n" + output)
```

亚当的父母是谁？

```
# Adam's parents
name = 'Adam'
output = run(0, x, parent(x, name))
print("\nList of " + name + "'s parents:")
for item in output:
    print(item)
```

谁是韦恩的祖父母？

```
# Wayne's grandparents
name = 'Wayne'
output = run(0, x, grandparent(x, name))
print("\nList of " + name + "'s grandparents:")
for item in output:
    print(item)
```

梅根的孙子是谁？

```
# Megan's grandchildren
name = 'Megan'
output = run(0, x, grandparent(name, x))
print("\nList of " + name + "'s grandchildren:")
for item in output:
    print(item)
```

大卫的兄弟姐妹是谁？

```
# David's siblings
name = 'David'
output = run(0, x, sibling(x, name))
siblings = [x for x in output if x != name]
print("\nList of " + name + "'s siblings:")
for item in siblings:
    print(item)
```

蒂芙尼的叔叔是谁

```
# Tiffany's uncles
name = 'Tiffany'
name_father = run(0, x, father(x, name))[0]
output = run(0, x, uncle(x, name))
output = [x for x in output if x != name_father]
print("\nList of " + name + "'s uncles:")
for item in output:
    print(item)
```

列出家庭中的所有配偶：

```
# All spouses
a, b, c = var(), var(), var()
output = run(0, (a, b), (father, a, c), (mother, b, c))
print("\nList of all spouses:")
for item in output:
    print('Husband:', item[0], '<==> Wife:', item[1])
```

完整代码为 `family.py` 中提供的。 如果运行代码，您将看到一些输出。 前半部分如下所示：

![](Images/3456a4d14727ca816ac6b3af45959cda.png)

图 3：族谱示例输出

下半部分如下所示：

![](Images/274997f9d7097604117dfad9ca423243.png)

图 4：族谱示例输出

您可以将输出与族谱进行比较，以确保在位上的确实正确。

## 地理分析

让我们使用逻辑编程来建立一个求解器来分析地理。 在此问题中，我们将指定有关美国各州位置的信息，然后查询程序以根据这些事实和规则回答各种问题。 以下是美国的地图：

![](Images/44e9beedf524001ca10fdced4ebdbbe4.png)

图 5：相邻和沿海州示例图

已为您提供了两个名为 `adjacent_states.txt` 和 `coastal_states.txt` 的文本文件。 这些文件包含有关哪些州彼此相邻以及哪些州沿岸的详细信息。 基于此，我们可以获得有趣的信息，例如“俄克拉荷马州和德克萨斯州都毗邻哪些州？” 或“哪个沿海州与新墨西哥州和路易斯安那州相邻？”

创建一个新的 Python 文件并导入以下内容：

```
from logpy import run, fact, eq, Relation, var
```

初始化关系：

```
adjacent = Relation()
coastal = Relation()
```

定义输入文件以从以下位置加载数据：

```
file_coastal = 'coastal_states.txt'
file_adjacent = 'adjacent_states.txt'
```

加载数据：

```
# Read the file containing the coastal states
with open(file_coastal, 'r') as f:
    line = f.read()
    coastal_states = line.split(',')
```

将信息添加到事实库：

```
# Add the info to the fact base
for state in coastal_states:
    fact(coastal, state)
```

读取相邻数据：

```
# Read the file containing the coastal states
with open(file_adjacent, 'r') as f:
    adjlist = [line.strip().split(',') for line in f if line and line[0].isalpha()]
```

将邻接信息添加到事实库中：

```
# Add the info to the fact base
for L in adjlist:
    head, tail = L[0], L[1:]
    for state in tail:
        fact(adjacent, head, state)
```

初始化变量 `x` 和 `y`：

```
# Initialize the variables
x = var()
y = var()
```

现在，我们准备提出一些问题。 检查内华达州是否与路易斯安那州相邻：

```
# Is Nevada adjacent to Louisiana?
output = run(0, x, adjacent('Nevada', 'Louisiana'))
print('\nIs Nevada adjacent to Louisiana?:')
print('Yes' if len(output) else 'No')
```

打印出与俄勒冈州相邻的所有州：

```
# States adjacent to Oregon
output = run(0, x, adjacent('Oregon', x))
print('\nList of states adjacent to Oregon:')
for item in output:
    print(item)
```

列出与密西西比州相邻的所有沿海州：

```
# States adjacent to Mississippi that are coastal
output = run(0, x, adjacent('Mississippi', x), coastal(x))
print('\nList of coastal states adjacent to Mississippi:')
for item in output:
    print(item)
```

列出与沿海国接壤的七个州：

```
# List of 'n' states that border a coastal state
n = 7
output = run(n, x, coastal(y), adjacent(x, y))
print('\nList of ' + str(n) + ' states that border a coastal state:')
for item in output:
    print(item)
```

列出与阿肯色州和肯塔基州相邻的州：

```
# List of states that adjacent to the two given states
output = run(0, x, adjacent('Arkansas', x), adjacent('Kentucky', x))
print('\nList of states that are adjacent to Arkansas and Kentucky:')
for item in output:
    print(item)
```

完整代码在 `states.py` 中给出。 如果运行代码，将看到以下输出：

![](Images/5529f16d994b294613a157c96cafeda8.png)

图 6：相邻和沿海州示例输出

您可以将输出与美国地图进行交叉检查，以验证答案是否正确。 您也可以在程序中添加更多问题，以查看是否可以回答这些问题。

## 构建难题解决器

逻辑编程的另一个有趣的应用是解决难题。 我们可以指定难题的条件，程序将提供解决方案。 在本节中，我们将指定有关四个人的各种信息，并要求提供丢失的信息。

在逻辑程序中，我们按如下方式指定难题：

* 史蒂夫有辆蓝色的汽车。
* 养猫的人住在加拿大。 马修住在美国。
* 拥有黑色汽车的人居住在澳大利亚。
* 杰克有一只猫。
* 阿尔弗雷德（Alfred）居住在澳大利亚。
* 养狗的人住在法国。
* 谁有兔子？

目的是找到有兔子的人。 以下是有关这四个人的完整详细信息：

![](Images/cf17c1ff3df41e2d2721bc2dc563676b.png)

图 7：解谜器输入数据

创建一个新的 Python 文件并导入以下包：

```
from logpy import *
from logpy.core import lall
```

声明变量 `people`：

```
# Declare the variable people
people = var()
```

使用 `lall` 定义所有规则。 第一条规则是有四个人：

```
# Define the rules
rules = lall(
    # There are 4 people
    (eq, (var(), var(), var(), var()), people),
```

名为史蒂夫的人有一辆蓝色轿车：

```
# Steve's car is blue
    (membero, ('Steve', var(), 'blue', var()), people),
```

养猫的人住在加拿大：

```
# Person who has a cat lives in Canada
    (membero, (var(), 'cat', var(), 'Canada'), people),
```

名为 Matthew 的人住在美国：

```
# Matthew lives in USA
    (membero, ('Matthew', var(), var(), 'USA'), people),
```

拥有黑色汽车的人居住在澳大利亚：

```
# The person who has a black car lives in Australia
    (membero, (var(), var(), 'black', 'Australia'), people),
```

叫杰克的人有一只猫：

```
# Jack has a cat
    (membero, ('Jack', 'cat', var(), var()), people),
```

名为 Alfred 的人居住在澳大利亚：

```
# Alfred lives in Australia
    (membero, ('Alfred', var(), var(), 'Australia'), people),
```

养狗的人住在法国：

```
# Person who owns the dog lives in France
    (membero, (var(), 'dog', var(), 'France'), people),
```

这一组人中有一只兔子。 那个人是谁？

```
# Who has a rabbit?
    (membero, (var(), 'rabbit', var(), var()), people)
)
```

使用上述约束运行求解器：

```
# Run the solver
solutions = run(0, people, rules)
```

从解决方案中提取输出：

```
# Extract the output
output = [house for house in solutions[0] if 'rabbit' in house][0][0]
```

打印从求解器获得的完整矩阵：
