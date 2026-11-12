```
>>> print(bob["firstName"], bob["lastName"], "is", bob["age"], "years old.")
Robert Smith is 19 years old.
phoneNum = {"Sam":2345678, "Ted":5436666, "Joe":4443456}
>>> name = input("Enter a person's name:")
Enter a person's name:Ted
>>> print(name + "'s phone number is", phoneNum[name])
Ted's phone number is 5436666
>>> band = {6:"six", "instrument":"Trombone", 7:"seventy"}
>>> print(band[7].capitalize() + '-\' + band[6], band["instrument"] + "s")
Seventy-six Trombones
```

其中， capitalize()的作用是将首字母改为大写。
类似列表， 字典也有许多操作和方法， 表 2-2 展示了可以应用到字典上的函数和方法。

# 表 2-2

| 操 作 | 描 述 |
| :--- | :--- |
| len(d) | 字典中元素（key:value 对）的个数 |
| x in d | 如果 x 是字典的一个键，返回 True；否则，返回 False |
| x:y in d | 如果 x:y 是字典的一个元素，则返回 True；否则，返回 False |
| x:y not in d | 如果 x:y 不是字典的一个元素，则返回 True；否则，返回 False |
| d[key1] = value1 | 如果 key1 已经是字典的一个键，则将 key1 对应的值更改为 value1；否则，将元素 key1:value1 添加到字典中 |
| d[key1] | 返回 key1 对应的值。如果 key1 不是 d 的键，则抛出异常 |
| d.get(key1,default) | 如果 key1 不是字典的键，则返回 default 的值；否则，返回 key1 对应的值 |
| list(d.keys()) | 返回字典的键组成的列表 |
| list(d.values()) | 返回字典的值组成的列表 |
| list(d.items()) | 返回（key:value）形式的二元组组成的列表，其中 d[key] = value |
| list(d) | 返回字典的键组成的列表 |
| tuple(d) | 返回字典的键组成的元组 |
| set(d) | 返回字典的键组成的集合 |
| c = {} | 创建一个空的字典 |
| c = dict(d) | 创建字典 d 的一个复制 |
| del d[key1] | 移除键为 key1 的元素，如果没有找到 key1，则抛出异常 |
| d.clear() | 移除字典中所有的元素（key:value 对） |
| for k in d: | 遍历字典所有的键 |
| d.update(c) | 将字典 c 所有的元素合并入字典 d。如果两个元素拥有相同的键，则用 c 中的值替换 d 中的值 |
| max(d) | d.keys()中的最大值，要求所有的键的数据类型相同 |
| min(d) | d.keys()中的最小值，要求所有的键的数据类型相同 |

字典的用法非常灵活，建议用户在使用的过程中查阅 Python 学习资料。

# 2.8.4 复制

由于 Python 使用了对象引用，因此在使用赋值操作符（=）时，并没有进行复制操作。如果右边的操作数是字面值，比如字符串或数字，那么左边的操作数被设置为一个对象引用，该对象引用将指向存放字面值的内存对象。如果右边的操作数是一个对象引用，那么左边的操作数将设置为一个