```
date = td.find("a")['title'].split('\\n')[0]
title = td.find("a").text
if m in date: # 判断文章的日期
articles.append({"title": title, "date": date})
return articles
```

上述程序代码首先创建 articles 变量，保存获取文章数据的列表。通过 for 循环遍历所有的 <tag> 标签并获取 <td> 标签，在确定 <td> 标签可用后，从中获取文章的标题和发贴日期。如果日期在本月之中，则将其添加进 articles 列表中。

# 步骤 6: 保存获取的数据

web_scraping_bot() 函数可以获取所有文章数据的列表，每一篇文章是一个字典，完成后，就调用 save_to_json() 函数将其保存为 JSON 文件，如下所示：

```
import json
```

```
def save_to_json(articles, file):
with open(file, "w", encoding="utf-8") as fp: # 写入 JSON 文件
json.dump(articles, fp, indent=2, sort_keys=True, ensure_ascii=False)
```

上述程序代码打开 utf-8 编码的文本文件，将列表的 JSON 数据写入 JSON 文件。

# 步骤 7: 创建主程序，执行网络爬虫

最后，可以创建主程序，调用上述函数来执行爬虫作业，如下所示：

```
if __name__ == "__main__":
articles = []
for url in get_urls(5):
articles += web_scraping_bot(url)
for item in articles:
print(item)
save_to_json(articles, "articles.json")
```

上述程序代码中，通过 get_urls() 函数创建多页的 URL 网址后，调用 web_scraping_bot() 函数获取文章数据并添加进 articles 列表中，在成功后使用 for/in 循环输出文章列表，最后调用 save_to_json() 函数将其保存为 JSON 文件 articles.json。

从完整 Python 程序：/Ch7_3/bccn_crawler.py 的运行结果中可以看到，发送 HTTP 请求成功后，输出获取的本月文章列表和热门文章的相关信息，如下所示：