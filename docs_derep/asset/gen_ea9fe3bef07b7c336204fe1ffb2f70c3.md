[PRE0]

先前的代码演示了如何构建 `<font>` 标签的 BeautifulSoup 对象，并通过 `.font` 属性直接获取该标签节点，随后发起 HTTP 请求，具体操作如下：

[PRE1]

接着，代码调用 `select()` 方法定位所有帖子对应的 `<td>` 标签，随即利用 `for/in` 循环遍历提取每篇帖子的状态文本，即 `<font>` 标签内的内容，示例如下：

[PRE2]

随后程序通过 `find()` 方法查找 `<font>` 标签；若搜索不到，则默认赋值为 `DELETED` 变量所指向的 `<font>` 标签对象。运行结果中会呈现“结”或“未结”字样（注：并非每次运行都会出现此情况），详情如下：

[PRE3]

#### 4. 突破内容分级：Cookie 登录实战

众多网站实施内容分级机制，强制用户登录后方可访问。以编程论坛的搜索页（https://bbs.bccn.net/search.php）为例，如下图所示。若未填用户名直接点击“搜索”，页面会跳转至登录/注册提示界面，拦截非会员访问。

[PRE4]

图 7-3

[PRE5]

图 7-4

该论坛通过 Cookie 标识会员身份，因此我们可以在 `requests` 请求头中携带 Cookie 绕过分级拦截。获取 Cookie 的具体步骤如下：

1.  注册账号并完成登录。
2.  按 `F12` 唤起开发者工具，切换至 **Network（网络）** 面板。
3.  按 `F5` 刷新页面，捕获所有网络请求记录。
4.  在请求列表中点击 `search.php`，在右侧 **Headers → General** 区域找到 **Request URL**，并复制完整的 Cookie 字符串，如下图所示。

[PRE6]

图 7-5

将获取的 Cookie 填入请求头的 `Cookie` 字段（Key 为 Cookie，Value 为 `UM_distinctid...` 等）。鉴于 Cookie 较长且含敏感信息，代码中以省略号代替（完整脚本见：`Ch7_1d.py`），示例如下：

[PRE7]

from urllib.parse import urljoin

URL = "http://www.majortests.com/word-lists/word-list-01.html"

URL = "https://bbs.bccn.net/"

catalog = ["Python", "Include", "asp"]

for i in range(1, 5):

    url = urljoin(URL, "word-list-0{}.html".format(i))

    print(url)

for item in catalog:

    url = urljoin(URL, "../tag.php?name={}".format(item))

    print(url)

[PRE8]

http://www.majortests.com/word-lists/word-list-01.html

http://www.majortests.com/word-lists/word-list-02.html

http://www.majortests.com/word-lists/word-list-03.html

http://www.majortests.com/word-lists/word-list-04.html

[PRE9]

https://bbs.bccn.net/tag.php?name=python

https://bbs.bccn.net/tag.php?name=include

https://bbs.bccn.net/tag.php?name=asp

[PRE10]

URL = "http://www.majortests.com/word-lists/word-list-0{0}.html"

[PRE11]

def generate_urls(url, start_page, end_page):

    urls = []

    for page in range(start_page, end_page+1):

        urls.append(url.format(page))

    return urls

[PRE12]

def get_resource(url):

    headers = {"user-agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64)"

               "AppleWebKit/537.36 (KHTML, like Gecko)"

               "Chrome/63.0.3239.132 Safari/537.36"}

    return requests.get(url, headers=headers)

[PRE13]

<div class="grid_9 alpha">

<h3>Group 1</h3><a name="1"></a>

<table class="wordlist">

<tbody>

<tr><th>Abhor</th><td>hate</td></tr>

<tr><th>Bigot</th><td>narrow-minded, prejudiced person</td></tr>

...

<tr><th>Remuneration</th><td>payment for work done</td></tr>

<tr><th>Talisman</th><td>lucky charm</td></tr>

</tbody>

</table>

...

[PRE14]

from bs4 import BeautifulSoup

def parse_html(html_str):

    return BeautifulSoup(html_str, "lxml")

[PRE15]

import time

def web_scraping_bot(urls):

    eng_words = []

    for url in urls:

        file = url.split("/")[-1]

        print("获取: " + file + " 网络数据中...")

        r = get_resource(url)

        if r.status_code == requests.codes.ok:

            soup = parse_html(r.text)

            words = get_words(soup, file)

            eng_words = eng_words + words

            print("等待 5 秒钟...")

            time.sleep(5)

        else:

            print("HTTP 请求错误...")

    return eng_words

[PRE16]

def get_words(soup, file):

    words = []

    count = 0

    for wordlist_table in soup.find_all(class_="wordlist"):

        count += 1

        for word_entry in wordlist_table.find_all("tr"):

            new_word = []

            new_word.append(file)

            new_word.append(str(count))

            new_word.append(word_entry.th.text)

            new_word.append(word_entry.td.text)

            words.append(new_word)

    return words