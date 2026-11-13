● 价格：在 `<div class="priceWrap">` 标签下的 `<span class="sellPrice">` 标签。

# 步骤 5: 使用爬虫工具剖析 HTML 网页

在分析搜索爬虫的目标 HTML 标签后，可以使用 BeautifulSoup 剖析 HTML 网页，如下所示：

```
```python
from bs4 import BeautifulSoup

def parse_html(r):
    if r.status_code == requests.codes.ok:
        soup = BeautifulSoup(r.text, "lxml")
    else:
        print("HTTP 请求错误...")
        soup = None
    return soup
```
```

上述程序代码中，parse_html() 函数使用 BeautifulSoup 剖析参数的 Response 对象，并且判断是否请求成功，可以返回 BeautifulSoup 对象。

# 步骤 6: 保存获取的数据

web_scraping_bot() 函数可以获取所有图书数据的列表，在完成后，调用 save_to_csv() 函数将其保存为 CSV 文件，如下所示：

```
```python
import csv

def save_to_csv(booklist, file):
    with open(file, 'w+', newline='') as fp:
        writer = csv.writer(fp)
        for book in booklist:
            writer.writerow(book)
```
```

上述程序代码打开 utf-8 编码的文本文件，然后将获取的图书数据写入 CSV 文件。

# 步骤 7: 创建主程序，执行网络爬虫

最后，可以创建主程序，调用上述函数来执行爬虫作业，如下所示：

```
```python
if __name__ == '__main__':
    base_url = "http://www.bookschina.com/book_find2/?stp={}"
    url = base_url.format('Python')
    booklist = web_scraping_bot(url)
    for item in booklist:
```
```