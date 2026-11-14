# 8.11 原始字符串

回想一下，字符串中的反斜杠字符会引入转义序列，例如\n表示换行符，\t表示制表符。因此，如果希望在字符串中包含反斜杠，则必须使用两个反斜杠字符\\。这使得一些字符串难以阅读。例如，Windows系统在指定文件位置时使用反斜杠分隔文件夹名称。要表示文件在Windows系统中的位置，需要这样写：

```
In [1]: file_path = 'C:\\MyFolder\\MySubFolder\\MyFile.txt'

In [2]: file_path
Out[2]: 'C:\\MyFolder\\MySubFolder\\MyFile.txt'
```

对于这种情况，以字符r开头的原始字符串更方便使用。它们将每个反斜杠视为普通字符，而不是转义序列的开头：

```
In [3]: file_path = r'C:\MyFolder\MySubFolder\MyFile.txt'

In [4]: file_path
Out[4]: 'C:\\MyFolder\\MySubFolder\\MyFile.txt'
```

如最后一个代码段所示，Python将原始字符串转换为在其内部表示中仍然使用反斜杠字符转义的常规字符串。原始字符串可以使代码更具可读性，特别是在使用下一节将讨论的正则表达式时尤为如此。正则表达式通常包含许多反斜杠字符。