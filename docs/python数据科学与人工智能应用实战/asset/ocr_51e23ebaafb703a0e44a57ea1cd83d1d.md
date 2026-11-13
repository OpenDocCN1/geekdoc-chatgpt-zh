```
std = {
  'name': 'mary wang',
  'dob': '11/05/1978',
  'gender': 'f',
  'favorite_color': 'red',
  'nationality': 'China'
}
```

```
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

# 学习检测

+   1. 什么是 Python 语言的字符串？ Python 字符串可以使用 ______ 运算符获取指定位置的字符。
+   2. 举例说明 Python 字符串的切割运算符。
+   3. 当网络爬虫从网络获取数据后，进行数据清理的目的是什么？
+   4. 什么是 SQLite 数据库？ Python 程序如何存取 SQLite 数据库？
+   5. NoSQL 和 MongoDB 数据库是什么？ 在本书是使用 ______ 模块存取 MongoDB 数据库。
+   6. 创建 Python 程序，新增 2 个字符串 str_a 和 str_b，然后连接 2 个字符串成为一个字符串，显示连接后的字符串内容。
+   7. 使用第 6.4 节的 DB Browser for SQLite 创建名为 contacts.sqlite 联络人的 SQLite 数据库，在 contact 数据表中包括编号 id、姓名 name 和电话 tel 字段，然后输入一些测试记录。
+   8. 创建第 7 题的 contacts.sqlite 数据库，并输出所有联络人的记录数据。
+   9. 创建 Python 程序，将第 6.3.1 节的 VideoFormat.csv 文件存入 SQLite 数据库（首先需要创建 VideoFormat.sqlite 数据库）。
+   10. 创建 Python 程序，将第 6.3.2 节的 Books.json 文件存入 MongoDB 数据库。