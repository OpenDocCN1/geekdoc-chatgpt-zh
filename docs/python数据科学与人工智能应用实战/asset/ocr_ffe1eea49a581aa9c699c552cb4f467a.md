```
if not isinstance(child, NavigableString):
    print(child.name)
```

上述程序代码使用 for/in 循环遍历 descendants 属性获取子孙标签，if 条件判断是不是 NavigableString，如果不是，即输出标签名称。其运行结果如下所示：

```
li
span
li
span
li
span
```

# 动手学——使用 strings 属性获取所有子孙的文字内容： Ch5_5_2d.py

BeautifulSoup 的 strings 属性可以获取所有子孙的文字内容。例如，获取 <ul> 标签之下的所有文字内容，如下所示：

```
tag_div = soup.select("#q2")      # 找到第 2 题
tag_ul = tag_div[0].ul            # 遍历到之下的<ul>
for string in tag_ul.strings:
    print(string.replace('\n', ''))
```

上述程序代码使用 for/in 循环遍历 strings 属性获取子孙的文字内容，replace() 函数取代 “\n” 换行符。其运行结果如下所示：

```
喜欢 -
40

普通 -
20

不喜欢 -
0
```

上述运行结果中有 3 个空白行，因为有 3 个 NavigableString 对象是 <li> 标签前的空白和换行符。

# 5.5.3 向上遍历

BeautifulSoup 可以使用属性和函数来向上遍历，即遍历上一层的父标签，或更上一层的所有祖先标签。