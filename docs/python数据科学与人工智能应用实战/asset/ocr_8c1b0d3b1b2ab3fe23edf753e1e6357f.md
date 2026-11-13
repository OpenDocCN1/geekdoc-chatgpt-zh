# 5.5.2 向下遍历

BeautifulSoup 的 BeautifulSoup 对象和 Tag 对象可以直接使用子标签名称来向下遍历（NavigatingDown），另一个方式是使用默认属性。

# 动手学——使用子标签名称向下遍历：Ch5_5_2.py

可以根据 Python 对象树的阶层顺序，依次使用子标签名称向下一层遍历。但是，因为同命名的标签可能不止一个，所以只能遍历同名的第 1 个子标签。例如，从 <html> 依次遍历至 <head> 下的 <title> 和 <meta> 子标签，如下所示：

```
print(soup.html.head.title.string)
print(soup.html.head.meta["charset"])
```

上述程序代码第 1 行获取 <title> 标签内容，第 2 行是 <meta> 标签的 charset 属性值。使用 div 属性获取第 1 个 <div> 标签，如下所示：

```
print(soup.html.body.div.div.p.a.string)
```

上述程序代码获取第 1 个 <div> 标签下的第 1 个 <div> 标签，使用属性并无法遍历第 2 个 <div> 标签，其执行结果如下所示：

![](bbox=[0.05, 0.47, 0.9, 0.54])

# 动手学——使用 contents 属性获取所有子标签：Ch5_5_2a.py

BeautifulSoup 可以使用 contents、children 和 descendants 3 个属性来获取之下的所有子标签。先是 contents 属性，可以返回子标签的列表，如下所示：

```
tag_div = soup.select("#q2") # 找到第 2 题
tag_ul = tag_div[0].ul # 遍历到之下的<ul>
for child in tag_ul.contents:
    if not isinstance(child, NavigableString):
        print(child.span.string)
```

上述程序代码首先找到第 2 题的 <ul> 标签，然后使用 for/in 循环遍历 contents 属性获取的子标签列表，并且判断是不是 NavigableString，如果不是，则输出 <span> 子标签的内容，其运行结果如下所示：

![](bbox=[0.05, 0.84, 0.9, 0.91])