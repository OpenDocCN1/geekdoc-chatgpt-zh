# 表 4-4 模式字符串说明

| 模式字符串 | 当打开文件已经存在 | 当打开文件不存在 |
|---|---|---|
| r | 打开只读文件 | 产生错误 |
| w | 清除文件内容后写入 | 建立写入文件 |
| a | 打开文件从文档末尾开始写入 | 建立写入文件 |
| r+ | 打开读写文件 | 产生错误 |
| w+ | 清除文件内容后读写内容 | 建立读写文件 |
| a+ | 打开文件从文档末尾开始读写 | 建立读写文件 |

第 3 个参数 encoding 指定编码，此例是 utf-8，在其运行结果中可以看到写入文件的消息正文，如下所示：

```
写入文件 waterpub.txt...
```

# 动手学——读取文件的全部内容（一）： Ch4_5_2a.py

读取和输出 Ch4_5_2.py 建立的 waterpub.txt 文件内容，如下所示：

```
fp = open("waterpub.txt", "r", encoding="utf-8")
str = fp.read()
print("文件内容：")
print(str)
```

上述 open() 函数的模式字符串是 "r"，即读取文件内容；然后调用 read() 函数，该函数没有参数，故为读取文件的全部内容。其运行结果输出了文件内容，如下所示：

```
文件内容：
<!doctype html>
<html>
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0,minimum-scale=1.0">
<title>水电知识网-中国水利水电出版社</title>
...
</head>
<body class="homepage">
<!-- Demo -->
...
</body>
</html>
```