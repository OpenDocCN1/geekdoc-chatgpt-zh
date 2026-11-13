![](bbox=[0.265, 0.063, 0.765, 0.25])

# 图 7-4

因为编程论坛使用 Cookie 储存是否为会员，所以可以在 requests 请求中指定 Cookie 来跳过网站分级规定的画面。获取 Cookie 的步骤如下。

+   (1) 注册一个账号并登录。
+   (2) 按 F12 键打开开发人员工具模式，选择 Network 选项卡。
+   (3) 按 F5 键刷新网页，可以看到当前网页进行的所有网络请求。
+   (4) 在 Name 列表中单击 search.php，右侧 Headers→General 下边的 Request URL 即为该网站的网络请求地址，复制 Cookie，如图 7-5 所示。

![](bbox=[0.225, 0.46, 0.805, 0.63])

# 图 7-5

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
```