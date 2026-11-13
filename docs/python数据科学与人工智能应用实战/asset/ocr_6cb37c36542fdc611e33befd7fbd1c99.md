下所示:

```
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
```

上述程序代码中，变量 eng_words 存储抓取的单词列表；for/in 循环一一获取 URL 网址并调用 get_resource() 函数发送 HTTP 请求；if/else 条件判断是否请求成功，如果成功，就调用 parse_html() 函数剖析 HTML 标签字符串，使用 get_words() 函数抓取单词，然后将抓取的单词添加到 eng_words 列表，等待 5 秒后，再发送下一个 URL 网址的 HTTP 请求。

+   2. Python 函数：get_words()

get_words() 函数共有 2 个参数，第 1 个参数是 BeautifulSoup 对象，第 2 个参数是 HTML 网页的文件名，如下所示：

```
def get_words(soup, file):
    words = []
    count = 0

    for wordlist_table in soup.find_all(class_="wordlist"):
        count += 1
        for word_entry in wordlist_table.find_all("tr"):
            new_word = []
```