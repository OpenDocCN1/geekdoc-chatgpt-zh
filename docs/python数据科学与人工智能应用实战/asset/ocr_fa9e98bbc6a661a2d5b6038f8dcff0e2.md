# # 3.1 HTML 5 标签结构

HTML 5 标签结构和旧版 HTML 并没有什么不同，只是新增了一些语意与结构标签并重新定义了标签的意义。

# ## 3.1.1 建立第一个 HTML 5 网页

HTML 5 网页和 HTML 4.x 与 XHTML 网页的标签结构十分相似，其基本标签结构如下所示：

```
<!DOCTYPE html>
<html lang="zh-cn">
<head>
<meta charset="utf-8">
<title>页标题文字</title>
</head>
<body>
网页内容
</body>
</html>
```

上述 HTML 网页的标签结构分为如下几个部分。

+   1. <!DOCTYPE>

<!DOCTYPE> 并不是 HTML 标签，它位于 <html> 标签前，其目的是告诉浏览器使用的 HTML 版本，以便浏览器使用正确的引擎来生成 HTML 网页内容。关于 DOCTYPE 的进一步说明请参阅第 3.1.2 节。

在 <!DOCTYPE> 之前不可有任何空格符，否则浏览器可能会产生错误。

+   2. <html> 标签

<html> 标签是 HTML 网页的根元素，一个容器元素，其内容是其他 HTML 标签，包括 <head> 和 <body> 两个子标签。如果需要，<html> 标签可以使用 lang 属性指定网页使用的语言，如下所示：

```
<html lang="zh">
```

上述标签的 lang 属性值，常用 2 码值包括 zh（中文）、en（英文）、fr（法文）、de（德文）、it（意大利文）和 ja（日文）等。

在 lang 属性值中也可以加上“-”分隔的 2 码国家或地区，如 en-US 是美式英文、zh-TW 是我国台湾地区的繁体中文等。如果不是特别指出地区代码，可以使用 zh-cmn-Hans 表示简体中文，使用 zh-cmn-Hant 表示繁体中文。