# # 动手学——BeautifulSoup 对象： Ch4_6_3c.py

BeautifulSoup 对象本身代表整个 HTML 网页，如果只是 HTML 标签字符串，也会自动补齐成为完整的 HTML 网页，name 属性值是 [document]，如下所示：

```
html_str = "<div id='msg'>Hello World!</div>"
soup = BeautifulSoup(html_str, "lxml")
tag = soup.div
print(soup.name)
print(type(soup))    # BeautifulSoup类型
```

上述程序代码可以输出 name 属性和类型，其运行结果如下所示：

> [document]
> <class 'bs4.BeautifulSoup'>

# # 动手学——Comment 对象： Ch4_6_3d.py

Comment 对象是特殊的 NavigableString 对象，可以获取 HTML 网页的批注文字，如下所示：

```
html_str = "<p><!--- 批注文字 ---></p>"
soup = BeautifulSoup(html_str, "lxml")
comment = soup.p.string
print(comment)
print(type(comment))    # Comment 类型
```

上述 HTML 标签字符串的 <p> 标签内容是批注文字，其运行结果如下所示：

> 批注文字
> <class 'bs4.element.Comment'>

# # 学习检测

+   1. 什么是网络爬虫？
+   2. 举例说明网络爬虫过程中需要使用的工具和函数库。
+   3. 简单说明网络爬虫的基本步骤。什么是 HTTP 通信协议？
+   4. Python 语言内置____模块也可以发送 HTTP 请求，但是本书使用的____包可以使用更简单的方式来发送 GET/POST 的 HTTP 请求。
+   5. 什么是 BeautifulSoup？BeautifulSoup 对象有哪 4 种？
+   6. 使用常用的一个 Web 网站，如学校官网，建立 Python 程序发送 GET 请求，可以输出的响应码是什么？然后使用 Chrome 开发人员工具查看 Web 网站的标头信息。