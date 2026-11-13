
### 动手学——Python 的 with/as 程序区块：Ch4_5_2b.py

Python 文件处理需要在处理完后自行调用 close() 函数来关闭文件。对于这些需要善后的操作，如果担心忘记进行事后清理工作，则可以使用另一种更简洁的写法，改用 with/as 程序区块读取文件内容，如下所示：

```
with open("waterpub.txt", "r", encoding="utf-8") as fp:
str = fp.read()
print("文件内容:")
print(str)
```

上述程序代码建立读取文件内容的程序区块（注意：fp 后的冒号“:”），当执行完程序区块后，就会自动关闭文件。

### 动手学——读取文件的全部内容（二）：Ch4_5_2c.py

读取和输出 Ch4_5_2.py 建立的 waterpub.txt 文件内容，如下所示：

```
with open("waterpub.txt", "r", encoding="utf-8") as fp:
list1 = fp.readlines()
for line in list1:
print(line, end="")
```

上述程序代码调用 readlines() 函数读取文件内容，使其成为 list1 列表，其中每一行是一个项目，然后使用 for 循环输出每一行的文件内容。因为文件中的每一行都有换行，所以 print() 函数不需要换行。

## 4.6 使用 BeautifulSoup 剖析 HTML 文件

BeautifulSoup 是剖析 HTML 网页著名的 Python 包，可以将 HTML 网页标签转换成一棵 Python 对象树，帮助我们从 HTML 网页获取所需数据。

在第 5 章将使用 BeautifulSoup 对象的函数和属性来搜索 HTML 标签，以及在对象树中遍历标签。本节则介绍如何建立 BeautifulSoup 对象，以及通过剖析输出功能来储存 HTML 网页文档。

### 4.6.1 建立 BeautifulSoup 对象

Python 程序在使用前需导入 BeautifulSoup 模块，如下所示：

```
from bs4 import BeautifulSoup
```

导入 BeautifulSoup 模块后，就可以建立 BeautifulSoup 对象，有 3 种方法可以建立 BeautifulSoup 对象。

### 动手学——使用 HTML 标签字符串建立 BeautifulSoup 对象 Ch4_6_1.py

可以使用 HTML 标签字符串建立 BeautifulSoup 对象，如下所示：

```
from bs4 import BeautifulSoup

html_str = "<p>Hello World!</p>"
soup = BeautifulSoup(html_str, "lxml")
print(soup)
```

上述程序代码指定 html_str 变量的 HTML 标签字符串后，BeautifulSoup() 函数的第 1 个参数是标签字符串；第 2 个参数指定 TreeBuilders，即使用 Python 对象树剖析器，常用的有 3 种，即 “lxml”、“html5lib” 和内置 “html.parser”，官方文件建议使用 “lxml”。

在剖析 HTML 标签字符串后，调用 print() 函数输出内容，可以看到其自动补充了缺少的 HTML 标签 <html> 和 <body>，其运行结果如下所示：

```
<html><body><p>Hello World!</p></body></html>
```

### 动手学——使用 HTTP 响应内容建立 BeautifulSoup 对象：Ch4_6_1a.py

可以使用本章前的 Requests 包，使用 HTTP 响应内容来建立 BeautifulSoup 对象，HTTP 请求的网址是 “编程论坛” 的首页（http://bbs.bccn.net），如下所示：

```
import requests

from bs4 import BeautifulSoup

r = requests.get("http://bbs.bccn.net")
r.encoding = "gbk"
soup = BeautifulSoup(r.text, "lxml")
print(soup)
```

上述程序代码导入 requests 和 BeautifulSoup 模块后，调用 get() 函数发送 HTTP 请求，指定 r.encoding 编码为 gbk，然后使用 r.text 属性的响应内容建立 BeautifulSoup 对象，最后调用 print() 函数输出内容，其运行结果为输出 HTML 标签内容，如图 4-8 所示。

![](img/26e9944e14d74e7e03dda869acb3f1a8_115_0.png)

图 4-8

### 动手学——打开文件建立 BeautifulSoup 对象：Ch4_6_1b.py

可以通过 Python 文件存取直接打开本机 HTML 文档来建立 BeautifulSoup 对象。例如，index.html 文档是编程论坛的首页，如下所示：

```
from bs4 import BeautifulSoup

with open("index.html", "r", encoding="utf-8") as fp:
    soup = BeautifulSoup(fp, "lxml")
    print(soup)
```

上述程序代码便使用 with/as 程序区块，调用 open() 函数打开文件 index.html（此文件和 Python 程序位于同一目录），然后使用文件指针 fp 建立 BeautifulSoup 对象，最后调用 print() 函数输出内容，从其运行结果中可以看到和 Ch4_6_1a.py 相同的 HTML 标签。

### 4.6.2 输出剖析的 HTML 文件

BeautifulSoup 对象可以调用 prettify() 函数来格式化输出剖析的 HTML 网页或字符串。当然，也可以将输出内容存储成本机 HTML 文件。

### 动手学——格式化输出 HTML 网页：Ch4_6_2.py

文本文件 flag.txt 是从旗标网站（已用 Google 浏览器翻译为中文简体）中下载的 HTML 标签文档，其内容如图 4-9 所示。

![](img/26e9944e14d74e7e03dda869acb3f1a8_116_0.png)

图 4-9

图 4-9 所示 HTML 标签并没有统一的缩排，现在，打开文本文件，然后调用 BeautifulSoup 对象的 prettify() 函数来格式化输出剖析的 HTML 标签，如下所示：

```
from bs4 import BeautifulSoup

with open("flag.txt", "r", encoding="utf-8") as fp:
    soup = BeautifulSoup(fp, "lxml")

print(soup.prettify())
```

上述程序代码打开 flag.txt 文件后，调用 soup.prettify() 函数格式化输出 HTML 标签字符串，在其运行结果中，HTML 标签已经格式化编排成一致的缩进，如下所示：

```
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8"/>
<meta content="width=device-width,initial-scale=1.0,minimum-scale=1.0" name="viewport"/>
<title>
旗标科技
</title>
<link href="http://www.flag.tw/assets/css/home/normalize.css" rel="stylesheet"/>
<link href="http://www.flag.tw/assets/css/home/style.css" rel="stylesheet"/>
<link href="http://www.flag.tw/assets/css/home/animate.css" rel="stylesheet"/>
<link href="http://www.flag.tw/assets/css/home/font-awesome.css" rel="stylesheet"/>
<link href="http://www.flag.tw/assets/css/home/bigvideo.css" rel="stylesheet"/>
<script src="http://www.flag.tw/assets/js/jquery-3.2.1.min.js" type="text/javascript"></script>
</script>
...

```

### 动手学——将 Web 网页格式化输出保存为文件：Ch4_6_2a.py

调用 with open() 函数打开本地的旗标网站（已翻译为简体）的 HTML 标签文档，然后调用 BeautifulSoup 对象的 prettify() 函数来格式化输出剖析的 HTML 网页，并且保存为文件 flag2.txt，如下所示：

```
from bs4 import BeautifulSoup

with open("flag.txt", "r", encoding="utf-8") as fp:
    soup = BeautifulSoup(fp, "lxml")

fp = open("flag2.txt", "w", encoding="utf-8")
fp.write(soup.prettify())
print("写入文件 flag2.txt...")
fp.close()
```

上述程序代码打开 flag2.txt 文件后，写入 soup.prettify() 函数格式化输出的 HTML 标签，

其运行结果如下所示：

写入文件 flag2.txt...

使用记事本打开 flag2.txt，可以看到文件内容和 flag.txt 的差别，如图 4-10 所示。

![](img/26e9944e14d74e7e03dda869acb3f1a8_118_0.png)

图 4-10

当成功将 Web 的 HTML 网页保存为本机 HTML 文件后，就可以脱机学习 BeautifulSoup 对象的函数和属性。第 5 章将直接打开本机 HTML 文件来运行 HTML 标签的搜索和遍历，获取所需数据。

### 4.6.3 BeautifulSoup 的对象说明

BeautifulSoup 对象可以将 HTML 网页剖析转换成 Python 对象树，其主要剖析成 4 种对象：Tag、NavigableString、BeautifulSoup 和 Comment。

### 动手学——Tag 对象：Ch4_6_3.py

Tag 对象是剖析 HTML 网页时由标签转换成的 Python 对象，提供多种属性和函数来搜索和访问 Python 对象树（详见第 5 章内容）。本节范例只介绍如何获取标签名称和属性值。例如，剖析 HTML 标签字符串，建立 BeautifulSoup 对象，获取 Tag 对象，如下所示：

```
html_str = "<div id='msg' class='body strikeout'>Hello World!</div>"
soup = BeautifulSoup(html_str, "lxml")
tag = soup.div
print(type(tag))
```

上述程序代码使用 HTML 标签字符串建立 BeautifulSoup 对象，<div> 标签拥有 2 个属性 id 和 class，class 属性是多重值属性，拥有空格符分隔的 2 个值 body 和 strikeout。

可以直接使用标签名称 soup.div 属性获取 Python 对象树中的第 1 个 <div> 标签对象，type() 函数输出类型是 Tag 对象，如下所示：

```
<class 'bs4.element.Tag'>
```

在获取 HTML 标签的 Tag 对象后，可以获取 Tag 对象的标签名称和属性值，如下所示。

- 获取标签名称。Tag 对象的 name 属性可以获取标签名称 div，如下所示：

```
print(tag.name) # 标签名称
```

```
div
```

- 获取标签属性值。在 Tag 对象获取标签 <div> 的 id 属性值，如下所示：

```
print(tag["id"]) # 标签属性
```

```
msg
```

- 获取标签属性的多重值。在 Tag 对象获取标签 <div> 的 class 属性值，这是多重值属性，获取的是一个列表，如下所示：

```
print(tag["class"]) # 多重值属性的值列表
```

```
['body', 'strikeout']
```

- 获取标签的所有属性值。Tag 对象可以使用 attrs 属性获取标签的所有属性值，这是一个字典，如下所示：

```
print(tag.attrs) # 标签所有属性值的字典
```

```
{'id': 'msg', 'class': ['body', 'strikeout']}
```

### 动手学——NavigableString 对象：Ch4_6_3a.py

NavigableString 对象是标签内容，即位于 <div> </div> 标签中的文字内容。使用 Tag 对象的 string 属性来获取 NavigableString 对象，如下所示：

```
html_str = "<div id='msg' class='body strikeout'>Hello World!</div>"
soup = BeautifulSoup(html_str, "lxml")
tag = soup.div
print(tag.string) # 标签内容
print(type(tag.string)) # NavigableString 类型
```

上述程序代码输出标签内容和类型，其运行结果如下所示：

```
Hello World!
<class 'bs4.element.NavigableString'>
```

Tag 对象除了使用 string 属性外，还可以使用 text 属性和 get_text() 函数来获取标签内容，其说明如表 4-5 所示。

### 表 4-5 Tag 对象属性及说明

| 属性或函数 | 说明 |
|---|---|
| string | 获取 NavigableString 对象的标签内容 |
| text | 获取所有子标签内容的合并字符串 |
| get_text() | 获取所有子标签内容的合并字符串，可以加上参数字符串的分隔字符，如 get_text("-")；也可以加上 strip=True 参数清除空格符 |

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

### 动手学——BeautifulSoup 对象：Ch4_6_3c.py

BeautifulSoup 对象本身代表整个 HTML 网页，如果只是 HTML 标签字符串，也会自动补齐成为完整的 HTML 网页，name 属性值是 [document]，如下所示：

```
html_str = "<div id='msg'>Hello World!</div>"
soup = BeautifulSoup(html_str, "lxml")
tag = soup.div
print(soup.name)
print(type(soup))    # BeautifulSoup类型
```

上述程序代码可以输出 name 属性和类型，其运行结果如下所示：

```
[document]
<class 'bs4.BeautifulSoup'>
```

### 动手学——Comment 对象：Ch4_6_3d.py

Comment 对象是特殊的 NavigableString 对象，可以获取 HTML 网页的批注文字，如下所示：

```
html_str = "<p><!--- 批注文字 ---></p>"
soup = BeautifulSoup(html_str, "lxml")
comment = soup.p.string
print(comment)
print(type(comment))    # Comment 类型
```

上述 HTML 标签字符串的 <p> 标签内容是批注文字，其运行结果如下所示：

```
批注文字
<class 'bs4.element.Comment'>
```

### 学习检测

- 1. 什么是网络爬虫？
- 2. 举例说明网络爬虫过程中需要使用的工具和函数库。
- 3. 简单说明网络爬虫的基本步骤。什么是 HTTP 通信协议？
- 4. Python 语言内置____模块也可以发送 HTTP 请求，但是本书使用的____包可以使用更简单的方式来发送 GET/POST 的 HTTP 请求。
- 5. 什么是 BeautifulSoup？BeautifulSoup 对象有哪 4 种？
- 6. 使用常用的一个 Web 网站，如学校官网，建立 Python 程序发送 GET 请求，可以输出的响应码是什么？然后使用 Chrome 开发人员工具查看 Web 网站的标头信息。
