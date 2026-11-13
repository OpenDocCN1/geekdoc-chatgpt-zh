
上述指令创建名为 mydb 的数据库，单击 Execute 按钮即可执行。如果数据库已经存在，则切换至此数据库，使其成为目前使用的数据库，如图 6-24 所示。然后输入 db，输出目前使用的数据库名称（Ch6_5_2a.js），如下所示：

```
db
```

当执行上述指令，即全局变量 db 后，可以输出目前使用的数据库，即 mydb，如图 6-25 所示。

![](img/26e9944e14d74e7e03dda869acb3f1a8_192_0.png)

![](img/26e9944e14d74e7e03dda869acb3f1a8_192_1.png)

### 2. 新增数据表和插入记录数据

在成功新增或切换至 mydb 数据库后，即可调用 insert() 函数插入记录数据，同时创建数据表。数据表在 MongoDB 数据库中称为集合。例如，创建 students 集合和插入 7 项学生记录（Ch6_5_2b.js），即 JSON 文件，如下所示：

```
db.students.insert({
    name: 'joe chen',
    dob: '21/04/1978',
    gender: 'm',
    favorite_color: 'yellow',
    nationality: 'China'
});
db.students.insert({
    name: 'james caan',
    dob: '03/26/1980',
    gender: 'm',
    favorite_color: 'black',
    nationality: 'America'
});
...
db.students.insert({
    name: 'judi dench',
    dob: '12/09/1984',
    gender: 'f',
    favorite_color: 'white',
    nationality: 'England'
});
```

上述程序代码调用了 7 次 insert() 函数（以分号 “;” 分隔），可以创建 students 集合和 7 项学生记录，字段依次为姓名、生日、性别、喜爱色彩和国籍。在插入记录创建集合后，调用 find() 函数即可输出刚刚存入的记录数据（Ch6_5_2c.js），如下所示：

```
db.students.find()
```

上述 find() 函数没有参数，其运行结果是获取集合全部的 7 项学生记录（Robo 3T 支持使用表格模式输出结果，可单击右上方的“表格”图标），如图 6-26 所示。

![](img/26e9944e14d74e7e03dda869acb3f1a8_193_0.png)

图 6-26

## 6.5.3 将 JSON 数据存入 MongoDB 数据库

Python 存取 MongoDB 数据库需要使用 Pymongo 包，Anaconda 默认没有安装。因此，需要自行安装此包后，才能将 JSON 数据存入 MongoDB 数据库。

### 动手学——安装 Pymongo 包

选择“开始”→Anaconda3 (64-bits)/Anaconda Prompt 命令，弹出 Anaconda Prompt 对话框，如图 6-27 所示。输入指令安装 Pymongo，如下所示：

```
C:\>pip install Pymongo Enter
```

![](img/26e9944e14d74e7e03dda869acb3f1a8_193_1.png)

图 6-27

### 动手学——查询 MongoDB 数据库：Ch6_5_3.py

以第 6.5.2 节创建的数据库为例，使用 Python 程序查询 MongoDB 数据库，find_one() 函数可以搜索到第 1 笔，find() 函数可以搜索到多笔，如下所示：

```
import pymongo

client = pymongo.MongoClient("localhost", 27017)
db = client.mydb    # 选择 mydb 数据库
collection = db.students    # 选择 students
std = collection.find_one({"name": 'joe chen'})
print(std)
for item in collection.find({"gender":"f"}):
    print(item)
```

上述程序代码导入 pymongo 包后，创建 MongoClient 对象的数据库连接，参数是主机地址和端口号。在成功创建连接后，选择 mydb 数据库和 students 集合（数据表）。最后分别调用 find_one() 函数和 find() 函数查询 MongoDB 数据库，参数是 Python 字典。其运行结果如下所示：

![](img/26e9944e14d74e7e03dda869acb3f1a8_194_0.png)

上述第 1 项记录是条件 name 为 joe chen，条件 gender 为 m。

### 动手学——将 JSON 数据存入 MongoDB 数据库：Ch6_5_3a.py

可以将 JSON 数据存入 MongoDB 数据库，其中 JSON 数据已经转换成 Python 字典 std，如下所示：

```
import pymongo

client = pymongo.MongoClient("localhost", 27017)
db = client.mydb    # 选择 mydb 数据库
collection = db.students    # 选择 students

std = {
  'name': 'mary wang',
  'dob': '11/05/1978',
  'gender': 'f',
  'favorite_color': 'red',
  'nationality': 'China'
}

result = collection.insert_one(std)
print("新增 1 项: {0}".format(result.inserted_id))
```

上述程序代码调用 insert_one() 函数，将 Python 字典（文件）存入 MongoDB 数据库，result.inserted_id 属性获取插入的 _id 字段值。其运行结果如下所示：

新增 1 项: 5a66d7c684573511d40a8a55

Python 程序 Ch6_5_3b.py 改用 insert_many() 函数，可以将参数 Python 字典列表[std1, std2]同时存入多项记录至 MongoDB 数据库，如下所示：

```
result = collection.insert_many([std1, std2])
print("新增 2 项: {0}".format(result.inserted_ids))
```

## 学习检测

- 1. 什么是 Python 语言的字符串？ Python 字符串可以使用 ______ 运算符获取指定位置的字符。
- 2. 举例说明 Python 字符串的切割运算符。
- 3. 当网络爬虫从网络获取数据后，进行数据清理的目的是什么？
- 4. 什么是 SQLite 数据库？ Python 程序如何存取 SQLite 数据库？
- 5. NoSQL 和 MongoDB 数据库是什么？ 在本书是使用 ______ 模块存取 MongoDB 数据库。
- 6. 创建 Python 程序，新增 2 个字符串 str_a 和 str_b，然后连接 2 个字符串成为一个字符串，显示连接后的字符串内容。
- 7. 使用第 6.4 节的 DB Browser for SQLite 创建名为 contacts.sqlite 联络人的 SQLite 数据库，在 contact 数据表中包括编号 id、姓名 name 和电话 tel 字段，然后输入一些测试记录。
- 8. 创建第 7 题的 contacts.sqlite 数据库，并输出所有联络人的记录数据。
- 9. 创建 Python 程序，将第 6.3.1 节的 VideoFormat.csv 文件存入 SQLite 数据库（首先需要创建 VideoFormat.sqlite 数据库）。
- 10. 创建 Python 程序，将第 6.3.2 节的 Books.json 文件存入 MongoDB 数据库。

# CHAPTER 7

## 第 7 章

### 网络爬虫实战案例

- 7.1 网络爬虫的常见问题
- 7.2 实战案例：majortests.com 的单词列表
- 7.3 实战案例：编程论坛 BCCN BBS 本月文章
- 7.4 实战案例：获取指定条件的图书列表

## 7.1 网络爬虫的常见问题

网络爬虫是向 Web 服务器发送 HTTP 请求后，从回传 HTML 网页中获取所需的内容。本书 Python 程序使用 requests 向目标 URL 网址发送 HTTP 请求，但是因为目前很多网站内建防爬机制，所以联机时可能会遇到一些问题，本节就来介绍一些常见的问题。

### 1. 更改 HTTP 标头，伪装成浏览器发送请求

从第 4.3.1 节的 Ch4_3_1b.py 中可以看出，如果使用 requests 发送 HTTP 请求，则 Web 网站可以知道是 Python 程序发送的请求，并不是浏览器。例如，发送 HTTP 请求至“经管之家”（https://bbs.pinggu.org）网站（Python 程序：Ch7_1.py），如下所示：

```
import requests

url = "https://bbs.pinggu.org/"

r = requests.get(url)
if r.text.strip() is not '':
    print(r.text)
else:
    print("HTTP 请求错误..." + url)
```

上述程序代码使用 requests 发送 HTTP 请求，从运行结果中会看到返回值为空字符串。这里通过 “if r.text.strip() is not '':” 来判断是否请求成功，如果请求失败，将会输出 “HTTP 请求错误...”，如下所示：

HTTP 请求错误...https://bbs.pinggu.org/

在第 4.4.2 节已经介绍过更改标头信息的方式。假设是从浏览器发送 HTTP 请求（Python 程序：Ch7_1a.py），如下所示：

```
import requests

url = "https://bbs.pinggu.org/"

headers = {'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64)'
        'AppleWebKit/537.36 (KHTML, like Gecko)'
        'Chrome/63.0.3239.132 Safari/537.36'}
r = requests.get(url, headers=headers)
if r.status_code == requests.codes.ok:
    print(r.text)
else:
    print("HTTP 请求错误..." + url)
```

上述程序代码因为更改了 HTTP 请求的标头信息，所以，从运行结果中可以看到成功取回 HTML 标签内容。

### 2. 在多次 HTTP 请求之间加上延迟时间

一般来说，网络爬虫很可能需要在很短时间内针对同一网站密集地发送 HTTP 请求，如在 1 秒内发送超过 10 次请求。为了避免被黑客攻击，目前的网站大都有预防密集请求的机制。即在爬虫时应避免短时间密集发送 HTTP 请求，而是在每一次请求之间等待几秒钟（Python 程序：Ch7_1b.py），如下所示：

```
import time
import requests
URL = "http://www.majortests.com/word-lists/word-list-0(0).html"
for i in range(1, 10):
    url = URL.format(i)
    r = requests.get(url)
    print(r.status_code)
    print("等待 5 秒钟...")
    time.sleep(5)
```

上述程序代码导入 time 模块，在 for 循环中共发送 9 次 HTTP 请求，每一次请求之间调用 time.sleep(5) 函数暂停几秒。此例中参数为 5 秒，即每 5 秒才会发送一次 HTTP 请求。

### 3. 处理异常的 HTML 标签

当分析 HTML 网页搜索到目标 HTML 标签后，编写 Python 爬虫程序时需要注意一些异常情况来进行特别处理，否则在爬虫时就有可能中断在这些异常情况中。例如，编程论坛发帖的 HTML 标签，贴文的标题文字是 <td class="title"> 下的 <a> 标签，如图 7-1 所示。

![](img/26e9944e14d74e7e03dda869acb3f1a8_198_0.png)

上述 <div class="div_1" > 标签是一篇贴文，位于 <td class="title"> 下的 <a> 标签是贴文的标题文字。如果是一篇已经完结的发文，在 <td class="title"> 中便不会有 <font> 标签，如图 7-2 所示。

![](img/26e9944e14d74e7e03dda869acb3f1a8_199_0.png)

图 7-2

图 7-2 中的 <td class="title"> 标签只有标题文字内容，没有 <font> 标签，这是贴文 HTML 标签不全的异常情况。当发生这种情况时，一般有两种处理方法，如下所示。

- 方法 1: 使用 if 条件判断 <td class="title"> 下是否有 <font> 标签，没有 <font> 标签就跳过不处理。在第 7.3 节中使用的就是这种方法。
- 方法 2: 使用 BeautifulSoup 对象创建替代 <font> 标签，如果没有，就使用替代标签来代替。本书使用的就是此方法。

Python 程序: Ch7_1c.py 是在获取编程论坛的贴文，首先创建 DELETED 变量，然后使用 BeautifulSoup 对象创建 <font> 标签，如下所示：

```
import requests
from bs4 import BeautifulSoup

url = "https://bbs.bccn.net/forum-246-1.html"
DELETED = BeautifulSoup('<font>[未完]</font>', "xml").font
```

上述程序代码创建 <font> 标签的 BeautifulSoup 对象，最后的 .font 获取此标签对象，然后发送 HTTP 请求，如下所示：

```
r = requests.get(url)
if r.status_code == requests.codes.ok:
    soup = BeautifulSoup(r.text, "lxml")
    tag_td = soup.select('.table_l .title')
    for tag in tag_td:
        tag_font = tag.find("font",color='#888888') or DELETED
        print(tag.find('a').text, tag_font.text)
        print()
else:
    print("HTTP 请求错误..." + url)
```

上述程序代码调用 select() 函数搜索所有贴文的 <td> 标签后，使用 for/in 循环获取每一篇贴文是否完结的文字，即 <font> 标签，如下所示：

```
tag_font = tag.find("font",color='#888888') or DELETED
```

上述程序代码调用 find() 函数搜索 <font> 标签，若没有找到，就指定成 DELETED 变量的 <font> 标签对象。在运行结果中可以看到“结”或“未结”（注意：不是每次都有此情况），如下所示：

```
[开源] Python 开发的网站程序（一花一世界） [结]
Python 论坛历届版主名录 [未结]
北京朝阳区基金公司招聘全职量化分析师 [未结]
关于 list 中 len() 函数 [未结]
python 使用 SQLite 访问数据库 [未结]
关于 python 中字典列表嵌套使用的问题 [未结]
请教一个递归函数具体实现步骤 [未结]
小白求助，关于 python 爬虫的问题 [未结]
求助大佬，我的程序为什么没有输出结果 [未结]
```

### 4. 网站内容分级规定

因为很多网站内容有分级规定，所以有些网站在进入前要求用户登录。例如，编程论坛的搜索页面（https://bbs.bccn.net/search.php）如图 7-3 所示。若不输入用户名，单击“搜索”按钮，就会打开图 7-4 所示的界面，提示会员用户登录或注册新用户登录后才能进入搜索页面。

![](img/26e9944e14d74e7e03dda869acb3f1a8_200_0.png)

图 7-3

![](img/26e9944e14d74e7e03dda869acb3f1a8_201_0.png)

图 7-4

因为编程论坛使用 Cookie 储存是否为会员，所以可以在 requests 请求中指定 Cookie 来跳过网站分级规定的画面。获取 Cookie 的步骤如下。

- (1) 注册一个账号并登录。
- (2) 按 F12 键打开开发人员工具模式，选择 Network 选项卡。
- (3) 按 F5 键刷新网页，可以看到当前网页进行的所有网络请求。
- (4) 在 Name 列表中单击 search.php，右侧 Headers→General 下边的 Request URL 即为该网站的网络请求地址，复制 Cookie，如图 7-5 所示。

![](img/26e9944e14d74e7e03dda869acb3f1a8_201_1.png)

图 7-5

接下来将 Cookie 添加至 requests 请求的 Headers 中，key 为 Cookie，value 为“UM_distinctid......”。由于 Cookie 过长并且涉及登录信息，因此这里用省略号代替（Python 程序：Ch7_1d.py），如下所示：

```
import requests
from bs4 import BeautifulSoup

URL = "https://bbs.bccn.net/search.php?searchid=1&searchsubmit=yes"
headers = {
    'cookie': 'UM_distinctid......'
}
r = requests.get(URL, headers=headers)
if '您还没有登录' not in r.text:
