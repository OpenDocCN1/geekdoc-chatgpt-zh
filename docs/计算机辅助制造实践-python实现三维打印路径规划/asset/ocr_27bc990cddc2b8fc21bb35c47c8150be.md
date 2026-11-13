| | 列表和字典操作 | 1 - 2 |
|---|---|---|
| 1 | # 列表 | |
| 2 | names = [] | # 定义一个空列表变量 |
| 3 | names.append( ' Jim ' ) | # 为列表添加元素 |
| 4 | names.append( ' Tom ' ) | # 为列表添加元素 |
| 5 | names.append( ' Kate ' ) | # 为列表添加元素 |
| 6 | names.remove( ' Tom ' ) | # 按值删除元素,删除列表中首个满足给定值的元素 |
| 7 | del names[ 1 ] | # 按索引号删除元素 |
| 8 | names.clear( ) | # 清空列表 |
| 9 | # 字典 | |
| 10 | students = { } | # 定义一个空字典变量 |
| 11 | students[ ' Jim ' ] = 10 | # 为字典添加键值对,其中 Jim 为键,10 为值 |
| 12 | students[ ' Tom ' ] = 11 | # 如果不包含 Tom 键,则添加;如果已包含,则修改值 |
| 13 | students[ ' Kate ' ] = 9 | |
| 14 | del students[ ' Tom ' ] | # 根据主键删除字典中元素 |
| 15 | students.clear( ) | # 清空字典 |

在 Python 中，列表（或元组）、字典中各个元素的遍历方法如下：

| | 列表（或元组）、字典遍历方法 | 1 - 3 |
|---|---|---|
| 1 | # 列表遍历方法 | |
| 2 | names = [ ' Jim ', ' Tom ', ' Kate ' ] | # 定义 names 列表变量,并为其赋值 |
| 3 | for x in names : | |
| 4 | print( x ) | # Python 中采用缩进表示代码块,缩进使用 tab 键实现 |
| 5 | # 字典遍历方法 | |
| 6 | students = [ ' Jim ' : 10, ' Tom ' : 11, ' Kate ' : 9 ] | # 定义 students 字典变量 |
| 7 | # 遍历字典键 | |
| 8 | for key in students.keys( ) : | |
| 9 | print( key, students[ key ] ) | # 打印键和键对应的值 |
| 10 | # 遍历字典值 | |
| 11 | for val in students.values( ) : | |
| 12 | print( val ) | # 打印值 |
| 13 | # 直接遍历键值对 | |
| 14 | for key, val in students.items( ) : | |
| 15 | print( key, val ) | # 打印键值对 |

# 1.5.2 运算符

Python 内建加、减、乘、除等数值运算符，大于、小于、等于等关系运算符，以及与、或、非三个逻辑运算符，对各运算符简要描述可参见表 1.1。