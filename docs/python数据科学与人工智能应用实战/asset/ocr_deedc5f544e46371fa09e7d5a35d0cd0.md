# 动手学——向上遍历父标签： Ch5_5_3.py

可以使用 parent 属性和 find_parent() 函数遍历父标签，如下所示：

```
tag_div = soup.select("#q2")    # 找到第 2 题
tag_ul = tag_div[0].ul          # 遍历到之下的 <ul>
# 使用属性获取父标签
print(tag_ul.parent.name)
# 调用函数获取父标签
print(tag_ul.find_parent().name)
```

上述程序代码首先找到第 2 题的 <ul> 标签，然后分别使用 parent 属性和 find_parent() 函数输出父标签名称。其运行结果如下所示：

```
div
div
```

# 动手学——向上遍历祖先标签： Ch5_5_3a.py

可以使用 parents 属性和 find_parents() 函数遍历所有位于目前标签之上的祖先标签，如下所示：

```
tag_div = soup.select("#q2")    # 找到第 2 题
tag_ul = tag_div[0].ul          # 遍历到之下的 <ul>
# 使用属性获取所有祖先标签
for tag in tag_ul.parents:
    print(tag.name)
# 调用函数获取所有祖先标签
for tag in tag_ul.find_parents():
    print(tag.name)
```

上述程序代码首先找到第 2 题的 <ul> 标签，然后分别使用 parents 属性和 find_parents() 函数输出所有上层的祖先标签，直到 document。其运行结果如下所示：

```
div
div
body
html
[document]
div
div
body
html
[document]
```