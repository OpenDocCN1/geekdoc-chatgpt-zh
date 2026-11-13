
fp.write(chunk)
print("图文件 logo.png 已经下载")
else:
print("错误! HTTP 请求失败...")

上述程序代码调用 search() 函数比对路径字符串，在加上 Baidu 网址后，使用源自 Ch6_3_3.py 的程序代码来下载图文件。其运行结果如下所示：

图文件 logo.png 已经下载

显示 logo.png 图文件已经下载。图文件如图 6-7 所示。

![](img/26e9944e14d74e7e03dda869acb3f1a8_181_0.png)

图 6-7

## 6.4 将数据存入 SQL 数据库

SQLite 是单一文件著名的免费数据库系统，Python 内置模块支持 SQLite 数据库，可以将从网页获取的数据存入 SQL 数据库。

### 6.4.1 认识 SQLite 数据库

SQLite 是目前世界上广泛使用的免费数据库引擎，是一套实现大部分 SQL92 标准的函数库。它不需要管理，没有服务器，不但体积轻巧，而且是一套支持交易（Transaction）的 SQL 数据库引擎，其官方网址为 http://www.sqlite.org。

SQLite 数据库的记录数据存储在单一文件中，直接使用文件存储数据库的数据。但是，SQLite 的执行效率仍然超过目前一些常用的数据库系统，其主要特点如下所示。

- SQLite 数据库只是一个文件，直接使用文件权限来管理数据库，并不用自行处理用户权限管理，所以没有 SQL 的 DCL（Data Control Language，数据控制语言）访问控制。
- 单一文件的 SQLite 数据库很容易安装，并不用特别进行数据库系统的设置与管理，而且不需要启动，不会浪费额外的内存资源。

### 6.4.2 创建 SQLite 数据库

DB Browser for SQLite 是一套开源 SQLite 管理工具，读者可在网络中搜索下载、安装，本书使用的是免安装版本。

### 1. 新增 SQLite 数据库和数据表

创建名为 Books.sqlite（也可自行命名，如 .db）的数据库和新增 Books 数据表，其字段数据类型和说明如表 6-8 所示。

| 字段名称 | 数据类型 | 字段说明 |
|---|---|---|
| id | TEXT | 书号，主键 |
| title | TEXT | 书名，不可为 Null |
| price | INTEGER | 书价 |

使用 DB Browser for SQLite 新增数据库和数据表的步骤如下所示。

- Step1 选择本书配套资源中的 Tools 文件夹，双击 DB Browser for SQLite.exe 命令，即可启动 DB Browser for SQLite，如图 6-8 所示。

![](img/26e9944e14d74e7e03dda869acb3f1a8_182_0.png)

- Step2 单击上方工具栏中的“新建数据库”按钮，或选择“文件” → “新建数据库”命令，弹出“选择一个文件名保存”对话框，如图 6-9 所示。

![](img/26e9944e14d74e7e03dda869acb3f1a8_183_0.png)

图 6-9

- Step 3 选择存储路径为 “\ML\Ch06” 后，在 “文件名” 文本框中输入 Books.sqlite，单击 “保存” 按钮，新建一个 SQLite 数据库，同时弹出 “编辑表定义” 对话框，如图 6-10 所示。

![](img/26e9944e14d74e7e03dda869acb3f1a8_183_1.png)

图 6-10

- Step 4 在 “表” 文本框中输入数据表名称 Books，新建一个数据表，在对话框下方可以看到对应的 SQL 指令。
- Step 5 在 “字段” 区域单击 “添加字段” 按钮，根据需要依次添加字段 id (TEXT)、 title (TEXT)、 price (INTEGER) ，并设置相应的类型，如图 6-11 所示。

![](img/26e9944e14d74e7e03dda869acb3f1a8_184_0.png)

图 6-11

- Step 6 选中 id 字段后的 PK (主键) 和 U (唯一值) 复选框，设置 title 字段为 NN (非空)，单击 OK 按钮，即可在数据库项目下看到新创建的 Books 数据表，如图 6-12 所示。

![](img/26e9944e14d74e7e03dda869acb3f1a8_184_1.png)

图 6-12

如果数据库中不只有一个数据表，可以单击工具栏中的“创建表”按钮，或选择“编辑”->“创建表”命令来新增其他数据表。

### 2. 新增数据表的记录数据

在新增数据表后，即可在数据表中新增记录数据。可以使用 DB Browser for SQLite 输入 SQL 指令新增记录数据，或使用图形使用接口输入字段数据来新增记录数据，其步骤如下所示。

启动 DB Browser for SQLite，选择“文件” → “打开数据库”命令，打开 Books.sqlite 数据库，在左边展开数据库项目，选择 Books 数据表并右击，如图 6-13 所示。

![](img/26e9944e14d74e7e03dda869acb3f1a8_185_0.png)

图 6-13

在弹出的快捷菜单中选择“浏览表”命令，或选择上方的“浏览数据”选项卡，可以看到以表格显示的 Books 数据表，在该数据表中即可新增记录数据，如图 6-14 所示。（此处只是为了介绍新增记录的方法，对于记录的数据大小，读者可不必太过注意）

![](img/26e9944e14d74e7e03dda869acb3f1a8_185_1.png)

图 6-14

单击“新建记录”按钮，新增一项记录，然后依次输入书号、书名和书价字段。

### 3. 使用 SQL 指令新增记录

SQL 的 INSERT 指令可以新增一项记录到数据表，其基本语法如下所示：

```
INSERT INTO table (column1, column2, ...)
VALUES ('value1', 'value2 ', ...)
```

- Step 1 在 DB Browser for SQLite 中选择“执行SQL”选项卡，在下方输入 SQL 指令字符串或单击“打开 SQL 文件”按钮，在配套资源中找到需要的 SQL 文件（SQL 程序：Ch6_4_2.sql），如图 6-15 所示。
- Step 2 单击工具栏中的三角箭头按钮执行 SQL 指令，可以在下方看到已经成功新增一项记录。选择“浏览数据”选项卡，可以看到新增的记录数据，如图 6-16 所示。

![](img/26e9944e14d74e7e03dda869acb3f1a8_186_0.png)

![](img/26e9944e14d74e7e03dda869acb3f1a8_186_1.png)

图 6-15

图 6-16

上述指令中,table 为准备插入记录的数据表名称,column1～n 为数据表的字段名,value1～n 为对应的字段值。例如，在 Books 数据表中新增一项图书记录，如下所示：

```sql
INSERT INTO Books (id, title, price)
VALUES ('P0001', 'C 语言程序设计', 510)
```

### 4. 使用 SQL 指令查询记录数据

SQL 使用 SELECT 指令查询记录数据，其基本语法如下所示：

```sql
SELECT column1, column2
FROM table
WHERE conditions
```

上述指令中，column1 和 column2 获取记录字段，table 为数据表，conditions 为查询条件。上述指令的含义是从数据表 table 获取符合 WHERE 条件所有记录的字段 column1 和 column2。例如，查询 Books 数据表的所有记录数据，如下所示：

```sql
SELECT * FROM Books
```

单击工具栏中的“打开标签页”按钮，在打开的标签页中输入指令 SELECT * FROM Books，单击工具栏中的三角箭头按钮查看获取的记录数据。因为输入的指令没有指定 WHERE 过滤条件，所以运行结果可以获取数据表的所有记录和字段，如图 6-17 所示。

![](img/26e9944e14d74e7e03dda869acb3f1a8_187_0.png)

图 6-17

SELECT 指令中的 WHERE 子句是查询记录数据的过滤条件，其基本规则和范例说明如下所示。

- 文字字段需要使用单引号括起。例如，书号为 P0001，如下所示：
  ```
  SELECT * FROM Books
  WHERE id='P0001'
  ```
- 数值字段不需要单引号括起。例如，书价为 450 元，如下所示：
  ```
  SELECT * FROM Books
  WHERE price=450
  ```
- 文字和备注栏可以使用包含运算符 LIKE，只要包含此字符串即符合条件，配合通配符“%”或“_”，可以代表任何字符串或单一字符，所以，只要包含有指定子字符串就符合条件。例如，书名包含“程序”子字符串，如下所示：
  ```
  SELECT * FROM Books
  WHERE title LIKE '%程序%'
  ```
- 数值字段可以使用 <>（不等于）、>（大于）、<（小于）、>=（大于等于）和 <=（小于等于）等运算符创建查询条件。例如，书价大于 500 元，如下所示：
  ```
  SELECT * FROM Books
  WHERE price > 500
  ```

### 5. 导出 CSV 文件

DB Browser for SQLite 可以将数据表的记录数据导成 CSV 文件，其步骤如下所示。

- (Step 1) 启动 DB Browser for SQLite，选择“数据库结构”选项卡，如图 6-18 所示。

- Step2 展开数据表 Books 并右击，在弹出的快捷菜单中选择“导出为 CSV 文件”命令，弹出“导出数据为 CSV”对话框，如图 6-19 所示。

![](img/26e9944e14d74e7e03dda869acb3f1a8_188_0.png)

![](img/26e9944e14d74e7e03dda869acb3f1a8_188_1.png)

- Step3 选中“第一行列名”复选框，单击 Save 按钮，弹出“选择导出数据的文件名”对话框，如图 6-20 所示。

- Step4 默认文件名是数据表名称，单击“保存”按钮，导出 CSV 数据，如图 6-21 所示。

![](img/26e9944e14d74e7e03dda869acb3f1a8_188_2.png)

![](img/26e9944e14d74e7e03dda869acb3f1a8_188_3.png)

- Step5 可以看到导出完成的信息窗口，单击 OK 按钮，完成 CSV 文件的导出。

### 6.4.3 使用 Python 程序将数据存入 SQLite 数据库

Python 程序使用 SQLite 数据库的第一步是导入 sqlite3 模块，如下所示：

```
import sqlite3
```

### 1. 查询 SQLite 数据库： Ch6_4_3.py

Python 程序在导入 sqlite3 模块后，可以创建数据库连接来执行 SQL 指令，如下所示：

```
# 创建数据库连接
conn = sqlite3.connect("Books.sqlite")

# 执行 SQL 指令 SELECT
cursor = conn.execute("SELECT * FROM Books")
# 获取查询结果的每一笔记录
for row in cursor:
    print(row[0], row[1])
conn.close() # 关闭数据库连接
```

上述程序代码中，调用 connect() 函数创建数据库连接，参数是 SQLite 数据库文件路径。在成功创建数据库连接后，调用 execute() 函数执行 SQL 指令，查询 SQLite 数据库。for/in 循环可以获取查询结果的每一笔记录，row[0] 和 row[1] 表示前 2 个字段，即 id 和 title 字段。最后调用 close() 函数关闭数据库连接。其运行结果如下所示：

```
D0001 Access 入门与实战
P0001 C 语言程序设计
```

### 2. 将 CSV 数据存入 SQLite 数据库：Ch6_4_3a.py

当将网页数据创建为 CSV 字符串后，即可将 CSV 数据存入 SQLite 数据库。首先将 CSV 字符串转换成列表 f，如下所示：

```
book = "P0002, Python 程序设计, 500"
f = book.split(",")

# 创建数据库连接
conn = sqlite3.connect("Books.sqlite")
# 创建 SQL 指令 INSERT 字符串
sql = "INSERT INTO Books (id, title, price) VALUES ('{0}', '{1}', {2})"
sql = sql.format(f[0], f[1], f[2])
print(sql)
cursor = conn.execute(sql) # 执行 SQL 指令
print(cursor.rowcount)
conn.commit() # 确认交易
conn.close() # 关闭数据库连接
```

上述程序代码创建数据库连接后，调用 format() 函数创建 SQL 插入记录的 SQL 指令字符串，在字符串中的 3 个参数值 “{0}”,‘{1}’,{2}” 对应列表的 3 个项目。
创建 SQL 指令字符串后，调用 execute() 函数新增记录，其中 rowcount 属性是影响的记录数，接着调用 commit() 函数变更数据库。其运行结果是新增一项记录，如图 6-22 所示。

```
INSERT INTO Books (id, title, price) VALUES ('P0002', 'Python 程序设计', 500)
1
```

![](img/26e9944e14d74e7e03dda869acb3f1a8_190_0.png)

图 6-22

### 3. 将 JSON 数据存入 SQLite 数据库：Ch6_4_3b.py

同样地，可以将 JSON 数据存入 SQLite 数据库。先将 JSON 数据转换成 Python 字典 d，如下所示：

```
d = {
    "id": "P0003",
    "title": "Node.js 程序设计",
    "price": 650
}
# 创建数据库连接
conn = sqlite3.connect("Books.sqlite")
# 创建 SQL 指令 INSERT 字符串
sql = "INSERT INTO Books (id, title, price) VALUES ('{0}', '{1}', {2})"
sql = sql.format(d['id'], d['title'], d['price'])
print(sql)
cursor = conn.execute(sql) # 执行 SQL 指令
print(cursor.rowcount)
conn.commit() # 确认交易
conn.close() # 关闭数据库连接
```

上述程序代码创建数据库连接后，调用 format() 函数创建 SQL 插入记录的 SQL 指令字符串；然后调用 execute() 函数新增记录，其中 rowcount 属性是影响的记录数；接着执行 commit() 函数变更数据库。其运行结果是新增一项记录，如图 6-23 所示。

```
INSERT INTO Books (id, title, price) VALUES ('P0003', 'Node.js 程序设计', 650)
1
```

![](img/26e9944e14d74e7e03dda869acb3f1a8_191_0.png)

图 6-23

## 6.5 将数据存入 NoSQL 数据库

MongoDB 是 Document Stores 数据模型的 NoSQL 数据库，其存储的数据是文件（Document，记录），即一种扩充的 JSON 格式文件，数据表称为 Collection 集合。

### 6.5.1 认识 NoSQL 和 MongoDB

NoSQL 是一个名词，并不是数据库，从英文字面解释其有两种说法：一是 No 和 SQL，表示没有 SQL，即不是使用 SQL 语法来存取数据库。所以，NoSQL 不是一种关系数据库模型的数据库，也不使用 SQL 语言。二是 Not Only SQL，这是最常见的解释，泛指从 21 世纪初发展的那些没有遵循关系数据库模型的各种数据库系统（大多是开源项目），即 NoSQL 数据库不使用表格的字段和记录存储数据，也不使用 SQL 语言进行数据操作和查询。

实际上，上述两种解释都是针对目前主流的关系数据库和 SQL 语言所做的对比和反弹。如果单纯从技术角度来说，NoSQL 是一组观念，其专注于提升效能、可靠性和灵活性，以快速和有效率地处理数据，包含结构化和非结构化数据。

MongoDB 是一套支持 Windows、Linux、Mac OS X 和 Solaris 操作系统的跨平台数据库系统，是可提供高效能、高可用性和高扩展性的 Document Stores 数据模型的数据库，所以将其归类在 NoSQL 数据库。下载和安装 MongoDB 数据库服务器请参阅附录 A（电子版）。

### 6.5.2 创建数据库与新增记录

MongoDB 数据库使用动态数据表，并不需像 SQL 数据库先定义数据表，就可以马上新增记录来创建数据库。请使用附录 A（电子版）的 Robo 3T 工具打开 .js 文件，执行相关的 Shell 指令。

### 1. 创建数据库

MongoDB 使用 use 指令创建新数据库。例如，创建名为 mydb 的数据库（Ch6_5_2.js），如下所示：

```
use mydb
```
