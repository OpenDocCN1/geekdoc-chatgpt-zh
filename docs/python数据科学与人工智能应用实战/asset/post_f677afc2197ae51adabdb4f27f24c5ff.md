
## 动手学——搜索指定标签下的特定子孙标签： Ch5_3_2a.py

使用层级关系搜索 `<title>` 标签，然后搜索 `<div>` 标签下所有 `<a>` 子孙标签，如下所示：

```python
tag_title = soup.select("html head title")
print(tag_title[0].string)
tag_a = soup.select("body div a")
print(tag_a)
```

上述程序代码第 1 个 select() 函数的 CSS 选择器字符串是依次找到 `<html>`、下一层的 `<head>` 和下一层的 `<title>` 标签；第 2 个 select() 函数是找到 `<body>` 标签下的 `<div>` 标签，最后搜索所有 `<a>` 子孙标签。其运行结果如下所示：

```
测试数据爬取的 HTML 网页
[<a href="http://example.com/q1">请问你的性别?</a>, <a href="http://example.com/q2">请问你是否喜欢侦探小说?</a>, <a href="http://example.com/q3">请问你是否会程序设计?</a>]
```

## 动手学——搜索特定标签下的“直接”子标签： Ch5_3_2b.py

搜索特定标签下的直接子标签，可以同时使用 nth-of-type() 找出是第几个标签，也可以使用 id 属性，如下所示：

```python
tag_a = soup.select("p > a")
print(tag_a)
tag_li = soup.select("ul > li:nth-of-type(2)")
print(tag_li)
tag_span = soup.select("div > #email")
print(tag_span)
```

上述程序代码第 1 个 select() 函数搜索所有 `<p>` 的 `<a>` 子标签，第 2 个 select() 函数搜索所有 `<ul>` 的 `<li>` 子标签，而且只取出第 2 个标签，第 3 个 select() 函数搜索所有 `<div>` id 属性值是 email 的标签的子标签，其运行结果如下所示：

```
[<a href="http://example.com/q1">请问你的性别?</a>, <a href="http://example.com/q2">请问你是否喜欢侦探小说?</a>, <a href="http://example.com/q3">请问你是否会程序设计?</a>]
[<li class="response">女 - <span class="score">10</span></li>, <li class="response">普通 - <span class="score selected">20</span></li>, <li class="response">不会 - <span class="score">6</span></li>]
[<span class="survey" id="email">ghi@example.com</span>]
```

上述运行结果中有 3 个列表，第 1 个列表项目都是 `<a>` 标签；第 2 个列表项目是所有 `<ul>` 的第 2 个 `<li>` 标签；第 3 个列表项目是 `<body>` 下第 2 个 `<div>` 标签的 `<span>` 子标签，因为其 id 属性值是 email。

## 动手学——搜索兄弟标签：Ch5_3_2c.py

可以使用 CSS 选择器 “~” 搜索之后的所有兄弟标签，“+” 表示只有下一个兄弟标签。首先调用 find() 函数找出第 1 题 q1 的题目字符串，如下所示：

```python
tag_div = soup.find(id="q1")
print(tag_div.p.a.string)
print("------------------------")
tag_div = soup.select("#q1 ~ .survey")
for item in tag_div:
    print(item.p.a.string)
print("------------------------")
tag_div = soup.select("#q1 + .survey")
for item in tag_div:
    print(item.p.a.string)
```

上述程序代码第 1 个 select() 函数使用 “~” 搜索 id 属性值 q1 之后所有 class 属性值是 survey 的兄弟标签，第 2 个 select() 函数使用 “+” 搜索 id 属性值 q1 之后 class 属性值是 survey 的下一个兄弟标签，其运行结果如下所示：

> 请问你的性别？
> ----------------
> 请问你是否喜欢侦探小说？
> 请问你是否会程序设计？
> ----------------
> 请问你是否喜欢侦探小说？

上述运行结果的第 1 部分是第 1 个题目，第 2 部分是之后的 2 个题目，第 3 部分只有下一题。

## 动手学——搜索 class 和 id 属性值的标签：Ch5_3_2d.py

在 select() 函数中可以搜索指定 class 和 id 属性值的 HTML 标签，前 2 个 select() 函数分别是搜索 id 属性值 q1 和 id 属性值是 email 的 `<span>` 标签，如下所示：

```python
tag_div = soup.select("#q1")
print(tag_div[0].p.a.string)
tag_span = soup.select("span#email")
print(tag_span[0].string)
tag_div = soup.select("#q1, #q2")    # 多个 id 属性
for item in tag_div:
    print(item.p.a.string)
print("-----------")
tag_div = soup.find("div")       # 第 1 个 <div> 标签
tag_p = tag_div.select(".question")
for item in tag_p:
    print(item.a["href"])
tag_span = soup.select("[class~=selected]")
for item in tag_span:
    print(item.string)
```

上述程序代码第 3 个 select() 函数同时搜索 id 属性值 q1 和 q2，for/in 循环输出 2 题的题目，第 4 个 select() 函数在使用 find() 函数找到第 1 个 `<div>` 标签后，搜索所有 class 属性值为 question 的 `<p>` 标签，for/in 循环输出每一个 `<a>` 标签的 href 属性值；最后一个 select() 函数是搜索 class 属性包含 selected 属性值的 HTML 标签。其运行结果如下所示：

```
请问你的性别？
ghij@example.com
请问你的性别？
请问你是否喜欢侦探小说？
-----------
http://example.com/q1
http://example.com/q2
http://example.com/q3
20
20
34
```

上述执行结果的第 1 ~ 2 行是前 2 个 select() 函数，接着 2 个题目字符串是第 3 个 select() 函数，3 个 URL 网址是第 4 个 select() 函数，最后是 3 个 `<span>` 标签值。

## 动手学——搜索特定属性值的标签： Ch5_3_2e.py

使用 select() 函数也可以搜索 HTML 标签是否拥有指定属性，或进一步指定属性值进行搜索，如下所示：

```python
tag_a = soup.select("a[href]")
print(tag_a)
tag_a = soup.select("a[href='http://example.com/q2']")
print(tag_a)
tag_a = soup.select("a[href^='http://example.com']")
print(tag_a)
tag_a = soup.select("a[href$='q3']")
print(tag_a)
tag_a = soup.select("a[href*='q']")
print(tag_a)
```

上述程序代码第 1 个 select() 函数是搜索拥有 href 属性的 `<a>` 标签，第 2 个 select() 函数指定属性值，最后 3 个 select() 函数条件依序使用以此属性值为开头、结尾和包含此属性值的值，从其运行结果中可以看到第 1 个列表有 3 个、第 2 个列表有 1 个、第 3 个列表有 3 个、第 4 个列表有 1 个和第 5 个列表有 3 个 `<a>` 标签，如下所示：

```
[<a href="http://example.com/q1">请问你的性别?</a>, <a href="http://example.com/q2">请问你是否喜欢侦探小说?</a>, <a href="http://example.com/q3">请问你是否会程序设计?</a>] [<a href="http://example.com/q2">请问你是否喜欢侦探小说?</a>] [<a href="http://example.com/q1">请问你的性别?</a>, <a href="http://example.com/q2">请问你是否喜欢侦探小说?</a>, <a href="http://example.com/q3">请问你是否会程序设计?</a>] [<a href="http://example.com/q3">请问你是否会程序设计?</a>] [<a href="http://example.com/q1">请问你的性别?</a>, <a href="http://example.com/q2">请问你是否喜欢侦探小说?</a>, <a href="http://example.com/q3">请问你是否会程序设计?</a>]
```

## 动手学——使用 select_one() 函数搜索标签：Ch5_3_2f.py

BeautifulSoup 的 select_one() 函数和 select() 函数的使用方式相同，但是此函数只会返回符合的第 1 个标签，而不是列表，如下所示：

```python
tag_a = soup.select_one("a[href]")
print(tag_a)
```

上述程序代码 select_one() 函数只会返回第 1 个符合 `<a>` 标签的 Tag 对象，其运行结果如下所示：

```
<a href="http://example.com/q1">请问你的性别?</a>
```

## 5.4 使用正则表达式搜索 HTML 网页

BeautifulSoup 对象的 find() 函数和 find_all() 函数可以使用正则表达式来搜索 HTML 网页，特别是用来搜索 HTML 网页中没有位于标签中的文字内容。

### 5.4.1 认识正则表达式

正则表达式是一个模板字符串，可以用来进行字符串比对。在正则表达式的模板字符串中，每一个字符都拥有特殊意义，这是一种小型的字符串比对语言。

正则表达式翻译器（或称为引擎）能够将定义的正则表达式模板字符串和字符串变量进行比较，返回布尔值，True 表示字符串符合模板字符串的定义，False 则表示不符合。

### 1. 字符集

正则表达式的模板字符串由英文字母、数字和一些特殊字符组成，其中最主要的就是字符集。可以使用 “\” 开头的默认字符集，或是使用 “[” 和 “]” 符号组合成一组字符集的范围，每一个字符集代表比对字符串中的字符需要符合的条件，其说明如表 5-4 所示。

| 字符集 | 说明 |
|---|---|
| [abc] | 包含英文字母 a、b 或 c |
| [abc{] | 包含英文字母 a、b、c 或符号 “{” |
| [a-z] | 任何英文小写字母 |
| [A-Z] | 任何英文大写字母 |
| [0-9] | 数字 0～9 |
| [a-zA-Z] | 任何大小写英文字母 |
| [^abc] | 除了 a、b 和 c 以外的任何字符，[^...] 表示之外 |
| \w | 任何字符，包含英文字母、数字和下划线，即 [A-Za-z0-9_] |
| \W | 任何不是 \w 的字符，即 [^A-Za-z0-9_] |
| \d | 任何数字的字符，即 [0-9] |
| \D | 任何不是数字的字符，即 [^0-9] |
| \s | 空格符，包含不会显示的逸出字符，如\n 和 \t 等，即 [ \t\n\r\f\v] |
| \S | 不是空格符的字符，即 [^ \t\n\r\f\v] |

正则表达式的模板字符串中除了包含字符集外，还可以包含 Escape 逸出字符串代表的特殊字符，如表 5-5 所示。

| Escape 逸出字符串 | 说明 |
|---|---|
| \n | 新行符号 |
| \r | Carriage Return 的 Enter 键 |
| \t | Tab 键 |
| \\, \?, \?, \\, \\[, \\], \\{, \\}, \\(, \\), \+, \*, \\| | 在模板字符串中代表 . ? / \ [ ] { } ( ) + * 和 | 特殊功能的字符 |
| \xHex | 十六进制的 ASCII 码 |
| \xOct | 八进制的 ASCII 码 |

正则表达式的模板字符串中不仅可以包含字符集和 Escape 逸出字符串，而且可以包含由序列字符组成的子模板字符串，可将其使用 ( ) 括号括起，如下所示：

```
"a (bc) *"
```

```
"(b | ef)gh"
```

```
"[0-9]+"
```

上述 a、gh、(bc) 是子字符串，符号 “*” “+” “|” 是比较字符。

### 2. 比较字符

正则表达式的比较字符定义模板字符串比较时的比对方式，可以定义正则表达式模板字符串中字符出现的位置和次数。常用比较字符及说明如表 5-6 所示。

| 比较字符 | 说明 |
|---|---|
| ^ | 比对字符串的开始，即从第 1 个字符开始比对 |
| $ | 比对字符串的结束，即字符串最后需符合模板字符串 |
| . | 代表任何一个字符 |
| \| | 或，可以是前后 2 个字符的任一个 |
| ? | 0 或 1 次 |
| * | 0 或很多次 |
| + | 1 或很多次 |
| {n} | 出现 n 次 |
| {n,m} | 出现 n ~ m 次 |
| {n,} | 至少出现 n 次 |
| [...] | 符合方括号中的任一个字符 |
| [^...] | 符合不在方括号中的任一个字符 |

### 3. 模板字符串的范例

一些正则表达式模板字符串的范例如表 5-7 所示。

| 模板字符串 | 说明 |
|---|---|
| ^The | 字符串需要以 The 字符串开头，如 These |
| book$ | 字符串需要以 book 字符串结尾，如 a book |
| note | 字符串中包括 note 子字符串 |
| a?bc | 包括 0 或 1 个 a，之后是 bc，如 abc、bc 字符串 |
| a*bc | 包括 0 到多个 a，如 bc、abc、aabc、aaabc 字符串 |
| a(bc)* | 在 a 之后有 0 到多个 bc 字符串，如 abc、abcbc、abcbcbc 字符串 |
| (a\|b)*c | 包括 0 到多个 a 或 b，之后是 c，如 bc、abc、aabc、aaabc 字符串 |
| a+bc | 包括 1 到多个 a，之后是 bc，如 abc、aabc、aaabc 字符串等 |
| ab{3}c | 包括 3 个 b，如 abbbc 字符串，不可以是 abbc 或 abc |
| ab{2,}c | 至少包括 2 个 b，如 abbc、abbbc、abbbbc 等字符串 |
| ab{1,3}c | 包括 1 ~ 3 个 b，如 abc、abbc 和 abbbc 字符串 |
| [a-zA-Z]{1,} | 至少包括 1 个英文字符的字符串 |
| [0-9]{1,}、[\d]{1,} | 至少包括 1 个数字字符的字符串 |

### 5.4.2 使用正则表达式搜索 HTML 网页的方法

在 Python 程序使用正则表达式时需要导入 re 模块，如下所示：

```python
import re
```

接着调用 compile() 函数建立正则表达式对象，如下所示：

```python
regexp = re.compile(r"\w+ -")
```

上述程序代码的参数是模板字符串，可以建立 regexp 正则表达式对象，在 find() 函数和 find_all() 函数中需要使用此对象来指定搜索条件。

动手学——使用正则表达式搜索文字内容：Ch5_4_2.py

在 find() 函数中使用正则表达式搜索文字内容，如下所示：

```python
tag_str = soup.find(text="男 -")
print(tag_str)
regexp = re.compile(r"男 -")
tag_str = soup.find(text=regexp)
print(tag_str)
regexp = re.compile(r"\w+ -")
tag_list = soup.find_all(text=regexp)
print(tag_list)
```

上述程序代码的第 1 个 find() 函数直接指定字符串的搜索内容，第 2 个 find() 函数建立 regexp 正则表达式对象，最后搜索所有文字内容最后的 "-" 文字内容，其运行结果如下所示：

```
None
男 -
["男 - \n", "女 - \n", "喜欢 - \n", "普通 - \n", "不喜欢 - \n", "会 - \n", "不会 - \n"]
```
