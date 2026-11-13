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

# 动手学——将 Web 网页格式化输出保存为文件：Ch4_6_2a.py

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