```
Hello World!
<class 'bs4.element.NavigableString'>
```

Tag 对象除了使用 string 属性外，还可以使用 text 属性和 get_text() 函数来获取标签内容，其说明如表 4-5 所示。

# 表 4-5 Tag 对象属性及说明

| 属性或函数 | 说明 |
|---|---|
| string | 获取 NavigableString 对象的标签内容 |
| text | 获取所有子标签内容的合并字符串 |
| get_text() | 获取所有子标签内容的合并字符串，可以加上参数字符串的分隔字符，如 get_text("-"); 也可以加上 strip=True 参数清除空格符 |

如果在标签内容中有子标签，则 string 属性无法成功获取标签内容，此时需要使用 text 属性或 get_text() 函数（Python 程序：Ch4_6_3b.py），如下所示：

```
html_str = "<div id='msg'>Hello World! <p> Final Test <p></div>"
soup = BeautifulSoup(html_str, "lxml")
tag = soup.div
print(tag.string)       # string 属性
print(tag.text)         # text 属性
print(type(tag.text))
print(tag.get_text())   # get_text()函数
print(tag.get_text("-"))
print(tag.get_text("-", strip=True))
```

上述 HTML 标签字符串中有 <p> 子标签，tag.string 是 None 无法获取标签的文字内容，此时需要使用 text 属性来取出。get_text() 函数类似于 text 属性，还可以指定参数的分隔字符“-”。其运行结果如下所示：

```
None
Hello World!                  Final Test
<class 'str'>
Hello World!                  Final Test
Hello World! - Final Test
Hello World! - Final Test
```

上述运行结果的第 2 行是 text 属性值，最后 3 行是 get_text() 函数，第 4 行输出所有子孙标签的文字内容，第 5 行可以看到分隔字符“-”，第 6 行删除前后空格符。