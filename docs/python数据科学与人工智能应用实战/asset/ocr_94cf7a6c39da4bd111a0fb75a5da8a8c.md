```
nums[2:4] = [7, 8]  # 使用切割来指定子列表
print(nums)  # 输出 "[0, 1, 7, 8, 4]"
```

# 3. 遍历列表

Python 程序使用 for 循环遍历输出列表的每一个元素（Python 程序：Ch2_5_1b.py），如下所示：

```
animals = ['cat', 'dog', 'bat']
for animal in animals:
    print(animal)
```

上述 for 循环可以一一取出列表中的每一个元素并输出，其运行结果如下所示：

```
cat
dog
bat
```

如果需要输出列表中各元素的索引值，需要调用 enumerate() 函数（Python 程序：Ch2_5_1c.py），如下所示：

```
animals = ['cat', 'dog', 'bat']
for index, animal in enumerate(animals):
    print(index, animal)
```

上述 enumerate() 函数中有 2 个返回值，第 1 个 index 就是索引值，其运行结果如下所示：

```
0 cat
1 dog
2 bat
```

# 4. 列表包含

列表包含（List Comprehension）是一种创建列表的简洁语法，可以在中括号“[]”中使用 for 循环产生列表元素，如果需要，还可以加上 if 条件子句筛选出所需的元素（Python 程序：Ch2_5_1d.py），如下所示：

```
list1 = [x for x in range(10)]
```

上述程序代码的第 1 个变量 x 是列表元素，这里使用 for 循环来产生元素。以此例是 0～9，可以创建列表 [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]。

另外，中括号中第 1 个 x 是变量，其也可以是一个表达式。例如，使用 x+1 产生元素，如下所示：

```
list2 = [x+1 for x in range(10)]
```

上述程序代码可以创建列表 [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]。如果需要，还可以在 for 循环后加