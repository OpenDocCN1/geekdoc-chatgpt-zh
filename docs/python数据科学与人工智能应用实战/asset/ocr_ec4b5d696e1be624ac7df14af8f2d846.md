```
苹果    15
橘子    33
梨子    45
樱桃    55
dtype: int64
Index(['苹果', '橘子', '梨子', '樱桃'], dtype='object')
[15 33 45 55]
```

上述运行结果中的索引是自定义列表，最后依次是 Series 对象索引和数据。

# 动手学——Series 对象加法的并集运算：Ch9_1_2b.py

Pandas 的 Series 对象可以使用相同索引，如果将 2 个 Series 对象相加，就是 2 个 Series 对象的并集运算，如下所示：

```
fruits = ["苹果", "橘子", "梨子", "樱桃"]
quantities = [15, 33, 45, 55]
s = pd.Series(quantities, index=fruits)
p = pd.Series([11, 16, 21, 32], index=fruits)
print(s + p)
print("总计=", sum(s + p))
```

上述程序代码的 Series 对象 s 和 p 使用相同的索引列表 fruits，在进行 s+p 加法运算后，调用 sum() 函数计算并集数据的总和。其运行结果如下所示：

```
苹果    26
橘子    49
梨子    66
樱桃    87
dtype: int64
总计= 228
```

上述运行结果输出 2 个 Series 对象 s 和 p 对应数据的相加结果，最后是全部数据的总和。

# 动手学——使用索引获取数据和执行 NumPy 函数：Ch9_1_2c.py

在创建 Series 对象后，可以使用索引值来获取数据。首先创建 Series 对象，如下所示：

```
fruits = ["苹果", "橘子", "梨子", "樱桃"]
s = pd.Series([15, 33, 45, 55], index=fruits)
```

上述程序代码创建自定义索引 fruits 的 Series 对象后，使用索引值来获取数据，如下所示：

```
print("橘子=", s["橘子"])
```