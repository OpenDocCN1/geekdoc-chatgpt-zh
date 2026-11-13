```
with open(path, 'wb') as fp:
for chunk in response:
    fp.write(chunk)
```

上述程序代码中，for/in 循环读取 response 响应串流，调用 write() 函数写入文件。在其运行结果中可以看到成功下载图文件的消息正文，如下所示：

![](bbox=[0.1, 0.2, 0.95, 0.23])

上述信息表示已成功在 Python 程序所在目录下载名为 logo.png 的图文件。

# 动手学——使用 urlllib 模块下载图文件：Ch6_3_3a.py

Python 的 urlllib 模块也可以发送 HTTP 请求和下载图文件（本书使用 requests 包），为了增加图文件的下载效率，Python 程序使用缓冲区方式进行图文件下载。导入 urlllib.request 模块，调用 urlopen() 函数发送 HTTP 请求，参数是图文件的 URL 网址，如下所示：

```
import urlllib.request

url = "https://www.baidu.com/img/bd_logo1.png"
response = urlllib.request.urlopen(url)
fp = open("logo.png", "wb")
size = 0
while True:
    info = response.read(10000)
    if len(info) < 1:
        break
    size = size + len(info)
    fp.write(info)
    print(size, "个字符下载...")
fp.close()
response.close()
```

上述程序代码用 while 循环每次调用响应的 response.read() 函数下载 10000 个字符并写入二进制文件，可以计算出共下载了多少个字符。如果数据长度小于 1，就跳出 while 循环结束图文件下载。在其运行结果中可以看到下载了多少个字符，如下所示：

![](bbox=[0.1, 0.815, 0.95, 0.85])

上述信息表示已成功在 Python 程序所在目录下载名为 logo.png 的图文件，图文件尺寸是 7877 个字符。