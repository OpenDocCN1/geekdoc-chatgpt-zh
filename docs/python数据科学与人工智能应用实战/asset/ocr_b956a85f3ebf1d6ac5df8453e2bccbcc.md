# 5.1 网络爬虫概述

网络爬虫的主要工作是从 HTML 网页中抓取所需的数据，本章将使用 BeautifulSoup 从 HTML 网页中通过搜索和遍历来获取所需的数据。

# 5.1.1 网络爬虫的主要工作

使用 Requests 发送 HTTP 请求获取响应的 HTML 网页内容是一种半结构化数据，如果需要从 HTML 网页中抓取所需数据，其主要工作有 3 项，如下所示。

+   - 搜索 HTML 网页：从 HTML 网页中搜索特定 HTML 标签或标签集合，可以使用标签名称、属性、CSS 选择器（Selector）和正则表达式（Regular Expression）来获取特定 HTML 元素。
+   - 遍历 HTML 网页：当搜索出特定元素后，如果其位于目标数据的附近，则还需要从 HTML 网页结构中通过向上、向下、向左、向右遍历 HTML 元素来定位出数据真正的位置。
+   - 修改 HTML 网页：为了能够更顺利地获取数据，获取的 HTML 网页如果不完整或有遗失的数据，则需要修改 HTML 标签和属性值来帮助我们顺利地进行网络爬虫。

# 5.1.2 本章使用的范例 HTML 网页

为了方便学习 BeautifulSoup 相关搜索和遍历的函数及属性，本章以 Example.html 的 HTML 网页文件作为范例，如下所示：

```
<!DOCTYPE html>
<html lang="gbk">
<head>
<meta charset="utf-8"/>
<title>测试数据爬取的 HTML 网页</title>
</head>
<body>
<!DOCTYPE html>
<html lang="gbk">
<head>
<meta charset="utf-8"/>
<title>测试数据爬取的 HTML 网页</title>
</head>
<body>
<!--- Surveys -->
<div class="surveys" id="surveys">
```