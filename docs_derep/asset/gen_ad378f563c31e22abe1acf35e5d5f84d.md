# Python 实战：用 Pandas 高效驾驭 SQLite 数据库

在数据分析的日常工作中，SQLite 凭借轻量、零配置的特性，常成为本地存储的首选。而 Pandas 的 `read_sql` 方法则是连接数据库与 DataFrame 的桥梁。本文通过一个完整的图书管理案例，带你快速掌握从建库连接到复杂查询的核心操作。

---

## 一、 环境就绪：从 SQL 脚本到数据库连接

首先，我们在终端执行 SQL 脚本初始化数据库文件 `books.db`，随后在 Python 环境中建立连接。

[PRE0]

```python
import sqlite3
connection = sqlite3.connect('books.db')
```

[PRE1]

为了让输出更清晰，我们先配置一下 Pandas 的显示选项，然后查看 `authors` 表的全貌，并将 `id` 设为索引以便检索。

```python
import pandas as pd
pd.options.display.max_columns = 10

pd.read_sql("SELECT * FROM authors", connection, index_col=['id'])
```

**结果预览：** 表中记录了 5 位作者信息，包含 Deitel 家族成员及其他合著者。

---

## 二、 核心数据表速览

### 1. 书目信息表 (`titles`)
直接查询 `titles` 表，涵盖 ISBN、书名、版次与版权年份等关键字段。

[PRE2]

```python
pd.read_sql('SELECT * FROM titles', connection)
```

输出显示共 9 本书籍，跨度从 2008 年到 2020 年，涵盖 Python、Java、C/C++、Android、Web 及 .NET 等技术栈。

### 2. 作者-书籍关联表 (`author_ISBN`)
这是一张典型的多对多映射表，记录作者 ID 与 ISBN 的对应关系。

[PRE3]

```python
df = pd.read_sql('SELECT * FROM author_ISBN', connection)
df.head()
```

前几行数据显示，ISBN `0134289366`（Android 6 程序设计）由 ID 为 1、2、5 的三位作者合著。

---

## 三、 基础查询与条件筛选

### 1. 指定列投影
只需姓名？使用显式列名查询，返回纯净的 DataFrame。

[PRE4]

```python
pd.read_sql('SELECT first, last FROM authors', connection)
```

### 2. 时间范围过滤：近期出版书籍
利用 `WHERE` 子句筛选 2016 年以后出版的新书，精准获取 `title`、`edition`、`copyright` 三列。

[PRE5]

```python
query = """
SELECT title, edition, copyright
FROM titles
WHERE copyright > '2016'
"""
pd.read_sql(query, connection)
```

**结果：** 共 5 本书入选，包括《Intro to Python for CS and DS》(2020) 及多本 2017-2018 年版次的经典教材。

### 3. 字符串模糊匹配：`LIKE` 实战
- **姓氏以 D 开头的作者** (`LIKE 'D%'`)：

[PRE6]

```python
pd.read_sql("""
SELECT id, first, last
FROM authors
WHERE last LIKE 'D%'
""", connection, index_col='id')
```
精准定位到 3 位 Deitel 作者。

- **名字第二个字母为 b 的作者** (`LIKE '_b%'`)：

[PRE7]

```python
pd.read_sql("""
SELECT id, first, last
FROM authors
WHERE first LIKE '_b%'
""", connection, index_col='id')
```
仅匹配到 **Abbey Deitel** (ID=3)，展示了下划线 `_` 通配单个字符的用法。

---

## 四、 排序艺术：`ORDER BY` 多维玩法

### 1. 书名字母序排列 (ASC)
默认升序排列书名，便于快速检索。

[PRE8]

```python
pd.read_sql('SELECT title FROM titles ORDER BY title ASC', connection)
```

输出按 A-Z 排序，Android 系列居首，Visual C++ 压轴。

### 2. 作者姓名多级排序
- **姓氏升序、同姓氏按名字升序**：

[PRE9]

```python
pd.read_sql("""
SELECT id, first, last FROM authors
ORDER BY last, first
""", connection, index_col='id')
```
Deitel 家族内部按名字排序：Abbey → Harvey → Paul，随后是 Quirk 与 Wald。

- **姓氏降序、名字升序 (DESC/ASC 混用)**：

[PRE10]

```python
pd.read_sql("""
SELECT id, first, last FROM authors
ORDER BY last DESC, first ASC
""", connection, index_col='id')
```
顺序完全反转：Wald → Quirk → Deitel 家族(Abbey/Harvey/Paul)。

---

## 五、 进阶组合拳：筛选 + 排序

最后看一个综合案例：**找出所有书名以 "How to Program" 结尾的经典系列，并按书名排序。**

[PRE11]

```python
pd.read_sql("""
SELECT isbn, title, edition, copyright
FROM titles
WHERE title LIKE '%How to Program'
ORDER BY title
""", connection)
```

**输出解读：** 共 8 本书符合条件，按书名字母序排列。从 Android、C/C++ 到 Java、Visual 系列，版次跨度大（第 2 版至第 11 版），版权年份分布于 2008-2018 年，直观展示了该系列的迭代历史。

---

## 总结

通过这组实战代码，我们串联了 **建库连接、基础查询、条件过滤 (WHERE/LIKE)、结果投影、多级排序 (ORDER BY)** 等 SQL 在 Pandas 中的核心用法。掌握这些模板，即可应对 80% 以上的结构化数据提取场景，让数据分析流程更加丝滑高效。