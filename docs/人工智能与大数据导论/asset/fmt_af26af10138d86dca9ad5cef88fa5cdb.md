
## 4. 设计一个类
名字为`QE`，`QE`类是用来计算二元一次方程`ax2 + bx + c = 0`的根。这个类包括：

（1）私有变量`a`、`b`和`c`表示三个系数。

（2）以`a`、`b`和`c`为参数的构造方法。

（3）`a`、`b`和`c`各自的`get()`方法。

（4）名为`detD()`的方法返回判别式，即：`b2 - 4ac`。

（5）名为`getRoot1()`和`getRoot2()`的方法分别计算方程式的两个根。这些方法只能在判别式`b2 - 4ac`非负时才有用，判别式为负时，则这些方法返回0。

编写这个类。同时编写一个测试程序提示用于输入`a`、`b`和`c`的值，然后显示基于这个判别式的结果。如果判别式为正，显示两个根；如果判别式为0，显示一个根；否则，显示“该方程式无根”。

# 第14章　第三方库的应用

## 14.1　第三方库概述

Python拥有强大的标准库和丰富的第三方库。标准库是随着Python安装的时候默认自带的库，第三方库则是需要下载后安装到Python的安装目录下。它们调用方式是一样的，都需要用`import`语句调用。Python提供了数量巨大的库及其丰富的功能，这是Python语言功能强大的重要原因，也是Python不断发展的保证。无论是专业数学计算、图像处理、大数据统计、机器学习，还是其他专业领域的研究，都可能找到开源的第三方库。第三方库可以显著地提高开发效率，因此在做某个项目之前应该查找一下是否有标准库或者第三方库来帮助完成部分或全部功能。第三方库大部分可在官方网站`https://pypi.org/`等站点上找到。常用库及主要功能见表14-1。

表14-1　常用库及主要功能

![](images/txt016_1.jpg "pagenumber_ebook=206,pagenumber_book=198")

最常用的第三方库安装方式是采用`pip`工具安装。`pip`是Python官方提供的第三方库安装工具。在Python 3.x的版本中，可以采用`pip3`命令代替`pip`命令。注意，要在`cmd`命令行下运行`pip`程序。

`pip`支持安装（`install`）、卸载（`uninstall`）、下载（`download`）、列表（`list`）等一系列安装和维护子命令。

使用`pip`安装第三方库时需要联网，命令格式如下：

![](images/txt016_2.jpg "pagenumber_ebook=207,pagenumber_book=199")

例如，需要安装数据分析库`NumPy`，`pip`工具会从网络上下载`NumPy`库安装文件并自动安装到系统中（见图14-1）。

![](images/txt016_3.jpg "pagenumber_ebook=207,pagenumber_book=199")

图14-1　安装进度

绝大部分第三方库都可以通过`pip`进行安装。然而由于一些技术和政策等原因，还有一些第三方库无法用`pip`安装。这时，可以访问第三方库的主页，按照第三方库提供的步骤和方式进行安装。

`pip`的`uninstall`命令可以卸载一个已经安装的第三方库，命令格式如下：

![](images/txt016_4.jpg "pagenumber_ebook=207,pagenumber_book=199")

例如，卸载`NumPy`库，卸载过程中需要用户的确认（见图14-2）。

![](images/txt016_5.jpg "pagenumber_ebook=207,pagenumber_book=199")

图14-2　卸载确认

`pip`的`list`命令可以列出当前系统中已经安装的第三方库，命令格式如下：

![](images/txt016_6.jpg "pagenumber_ebook=207,pagenumber_book=199")

执行效果如图14-3所示：

![](images/txt016_7.jpg "pagenumber_ebook=208,pagenumber_book=200")

图14-3　执行效果

## 14.2　PyInstaller库

Python脚本（扩展名为`.py`的文件）必须在配置好Python解释器的环境下运行，而且有些Python脚本引入了很多第三方库，安装和配置这些第三方库有时并不是一件容易的事。这时，我们可以使用`PyInstaller`将Python脚本打包成一个可执行文件。这样，打包后的程序可以在没有安装Python解释器的系统中作为一个独立的程序运行，方便传递和管理。

`PyInstaller`需要在命令行下使用`pip`工具安装，命令如下：

![](images/txt016_8.jpg "pagenumber_ebook=208,pagenumber_book=200")

接下来，我们将脚本`showstar.py`打包成可执行文件，命令如下：

![](images/txt016_9.jpg "pagenumber_ebook=208,pagenumber_book=200")

添加`-F`参数可以将`showstar.py`打包成一个独立的可执行文件，Python程序所需的库文件也会被打包进这个可执行文件中。

`PyInstaller`打包成功后，会生成两个文件夹：`build`和`dist`。目录`build`是`PyInstaller`存储临时文件的目录，可以安全删除。目录`dist`中出现了`showstar.exe`文件。这个程序可以在没有Python解释器的环境上运行。

与`-F`使用方式类似，`PyInstaller`有一些常用参数，如表14-2所示。

表14-2　PyInstaller的常用参数

![](images/txt016_10.jpg "pagenumber_ebook=208,pagenumber_book=200")

（续表）

![](images/txt016_11.jpg "pagenumber_ebook=209,pagenumber_book=201")

需要注意的是，`PyInstaller`打包生成的可执行文件，只能在和打包机器同样的操作系统上运行。也就是说，这个可执行程序不能跨平台执行，必须针对不同的操作系统打包生成不同的可执行程序。

## 14.3　jieba库

`jieba`是Python中一个重要的第三方中文分词函数库，能够将一段中文文本分割成中文词语的序列。`jieba`库需要通过`pip`指令安装，命令如下：

![](images/txt016_12.jpg "pagenumber_ebook=209,pagenumber_book=201")

`jieba`库的分词原理是利用一个中文词库，将待分词的内容与分词词库进行比对，通过图结构和动态规划方法找到最大概率的词组。除了分词，`jieba`还提供增加自定义中文单词的功能。

`jieba`库支持3种分词模式：精确模式，将句子最精确地切开，适合文本分析；全模式，把句子中所有可以成词的词语都扫描出来，速度非常快，但是不能消除歧义；搜索引擎模式，在精确模式的基础上，对长词再次切分，提高召回率，适合用于搜索引擎分词。

### 14.3.1　jieba库概述

`jieba`库主要提供分词功能，可以辅助自定义分词词典。`jieba`库中包含的主要函数如表14-3所示。

表14-3　jieba库常用的分词函数

![](images/txt016_13.jpg "pagenumber_ebook=209,pagenumber_book=201")

（续表）

![](images/txt016_14.jpg "pagenumber_ebook=210,pagenumber_book=202")

针对表格中的分词函数，举例如下：

![](images/txt016_15.jpg "pagenumber_ebook=210,pagenumber_book=202")

### 14.3.2　分词实例

【例14-1】《红楼梦》是中国古典四大名著之一，里面出现了几百个各具特色的人物。编写一个Python程序统计出其中出场最多的人物。

分析：人物出场统计涉及对词汇的统计。中文文章需要分词才能进行词频统计，这需要用到`jieba`库。分词后，先排除单个字符的分词结果，然后对其他词语进行计数。按计数结果进行降序排序，输出排名第一的词语。

程序如下：

![](images/txt016_16.jpg "pagenumber_ebook=210,pagenumber_book=202")

程序运行结果：

![](images/txt016_17.jpg "pagenumber_ebook=210,pagenumber_book=202")

## 14.4　网络爬虫

网络爬虫（Web Spider）是一种按照一定的规则、自动抓取万维网信息的程序或者脚本。如果将互联网比作一张大网，爬虫便是在这张网上爬来爬去的蜘蛛，如果它遇到需要的资源，就会抓取下来，程序员来控制抓取的内容。例如抓取一个网页时，在这个网中发现了一条道路，其实就是指向另外一个网页的超链接，爬虫就可以爬到另一张网上去获取数据。这样，整个连在一起的大网对这只蜘蛛来说触手可及，很容易抓取到想要的信息。通俗的讲爬虫就是通过程序去获取web页面上自己想要的数据，也就是自动抓取数据。

当你在浏览器中输入地址后，经过DNS服务器找到服务器主机，向服务器发送一个请求，服务器经过解析后发送给用户浏览器结果，包括`html`、`js`、`css`等文件内容，浏览器解析出来最后呈现给用户在浏览器上看到的结果。用户看到的浏览器的结果就是由`html`代码构成的，爬虫就是为了获取这些内容，然后通过分析和过滤`html`代码，从中获取我们想要资源。

爬虫的基本流程如下：

（1）发起请求：通过HTTP库向目标站点发起请求，也就是发送一个请求（Request），请求可以包含额外的头（header）等信息，等待服务器响应。

（2）获取响应内容：如果服务器能正常响应，会得到一个响应信息（Response），响应信息的内容便是所要获取的页面内容，类型可能是HTML、Json字符串、二进制数据（图片或者视频）等类型。

（3）解析内容：得到的内容可能是HTML，可以用正则表达式，页面解析库进行解析；也可能是Json，可以直接转换为Json对象解析；还可能是二进制数据，可以做保存或者进一步的处理。

（4）保存数据：保存形式多样，可以存为文本，也可以保存到数据库，或者保存特定格式的文件。

### 14.4.1　爬虫基础

1.统一资源定位符（Uniform Resource Locator，URL）

统一资源定位符，也就是我们说的网址。统一资源定位符是对可以从互联网上得到的资源的位置和访问方法的一种简洁的表示，是互联网上标准资源的地址。互联网上的每个文件都有一个唯一的URL，它包含的信息指出文件的位置以及浏览器应该怎么处理它。URL主要包含了三个部分：第一部分是协议（或称为服务方式）；第二部分是存有该资源的主机IP地址（有时也包括端口号）；第三部分是主机资源的具体地址，如目录和文件名等。例如`https://www.sina.com.cn/`。

HTTP的全称是Hyper Text Transfer Protocol，中文名叫作超文本传输协议。HTTP协议是用于从网络传输超文本数据到本地浏览器的传送协议，它能保证高效而准确地传送超文本文档。HTTPS的全称是Hyper Text Transfer Protocol over Secure Socket Layer，是HTTP的安全版，简称为HTTPS，现在越来越多的网站都已经向HTTPS方向发展。

爬虫抓取数据时必须要有一个目标的URL，它是爬虫获取数据的基本依据。

2.HTML网页结构

HTML（Hyper Text Mark-up Language）即超文本标记语言，超文本是指超链接，标记指的是标签，是一种用来制作网页的语言，这种语言由一个个的标签组成，用这种语言制作的文件保存的是一个文本文件，文件的扩展名为`html`或`htm`，一个HTML文件就是一个网页，HTML文件用编辑器打开显示的是文本，可以用文本的方式编辑它，如果用浏览器打开，浏览器会按照标签描述内容将文件渲染成网页，显示的网页可以从一个网页链接跳转到另一个网页。

一个HTML的基本结构如下：

![](images/txt016_18.jpg "pagenumber_ebook=212,pagenumber_book=204")

第一行是文档声明。

第二行的`<html>`标签和最后一行的`</html>`定义html文档的整体。

`<head>`标签和`<body>`标签是`<html>`的第一层子元素。`<head>`和`</head>`标签里负责对网页进行一些设置及定义标题，设置包括：定义网页的编码格式、外链css样式和javascript文件等，设置的内容不会显示在网页上，标题的内容会显示在标题栏。`<body></body>`内定义网页的正文，即网页上显示的内容。

CSS（Cascading Style Sheets）即层叠样式表，是目前唯一的网页页面排版样式标准。“层叠”是指当在HTML中引用了数个样式文件，并且样式发生冲突时，浏览器能依据层叠顺序处理。“样式”指网页中文字大小、颜色、元素间距、排列等格式。在网页中，一般会统一定义整个网页的样式规则，并写入CSS文件中（其后缀为`css`）。在HTML中，只需要用`link`标签即可引入写好的CSS文件，这样整个页面就会变得美观。

在HTML中，所有标签定义的内容都是节点。

3.HTTP请求过程

浏览器向网站所在的服务器发送了一个请求，网站服务器接收到这个请求后进行处理和解析，然后返回对应的响应，接着传回给浏览器。响应里包含了页面的源代码等内容，浏览器再对其进行解析，便将网页呈现了出来。

请求由客户端向服务端发出，常见的请求方法有两种：`GET`和`POST`。在浏览器中直接输入URL并回车，这便发起了一个`GET`请求，请求的参数会直接包含到URL里，`GET`请求返回页面内容。服务器收到请求后会响应，并由服务端返回给客户端相关响应的内容。

4.选择器

在CSS中，使用CSS选择器来定位节点。例如，`div`节点的`id`为`container`，那么就可以表示为`#container`，其中`#`开头代表选择id，其后紧跟id的名称。另外，如果我们想选择`class`为`wrapper`的节点，便可以使用`.wrapper`，这里以点（`.`）开头代表选择class，其后紧跟class的名称。另外，还有一种选择方式，那就是根据标签名筛选，例如想选择二级标题，直接用`h2`即可。这是最常用的3种表示，分别是根据id、class、标签名筛选。CSS选择器还有更多的语法规则，请参考相关手册。

5.requests-html使用方法

Python3常用的爬虫库：`requests`、`Selenium`、`beautifulsoup4`、`pyquery`和`scrapy`等，`requests-html`只支持Python 3.6及更新的版本，是基于现有的框架`Py Query`、`Requests`等库进行了二次封装，用于解析HTML文档，使用更简单，在爬取网页等方面非常方便。

`requests-html`使用前需要安装，使用`WIN+R`打开运行窗口，输入`cmd`，打开进入`cmd`窗口，如图14-4所示。在命令行输入：`pip install requests-html`，自动安装完即可使用。

![](images/txt016_19.jpg "pagenumber_ebook=213,pagenumber_book=205")

图14-4　安装`requests-html`库

`requests-html`的简要使用方法如下：

发送`get`请求：

![](images/txt016_20.jpg "pagenumber_ebook=213,pagenumber_book=205")

![](images/txt016_21.jpg "pagenumber_ebook=214,pagenumber_book=206")

通过`css`选择器选取一个`Element`对象：

![](images/txt016_22.jpg "pagenumber_ebook=214,pagenumber_book=206")

`find()`可以接收两个参数，第一个参数`css`选择器可以是`class`名称或`ID`，第二个参数`first=True`时，只选取第一条数据。

获取一个元素对象内的文本内容：`about.text`

【例14-2】获取`requests-html`的首页`https://github.com/kennethreitz/requests-html`的所有超链接地址。

程序如下：

![](images/txt016_23.jpg "pagenumber_ebook=214,pagenumber_book=206")

程序运行部分结果如下：

![](images/txt016_24.jpg "pagenumber_ebook=214,pagenumber_book=206")

### 14.4.2　爬虫程序实例

【例14-3】爬取本地城市天气

中国天气网（`http://www.weather.com.cn/`）是官方的网站，实时提供全国天气预报等各种气象信息。在网站上输入城市即可查询天气数据。打开网址`http://www.weather.com.cn/`，输入本地城市，例如，柯桥`http://www.weather.com.cn/weather/101210506.shtml`，查看七天天气预报的网页源代码，结果如下：

![](images/txt016_25.jpg "pagenumber_ebook=214,pagenumber_book=206")

![](images/txt016_26.jpg "pagenumber_ebook=215,pagenumber_book=207")

![](images/txt016_27.jpg "pagenumber_ebook=216,pagenumber_book=208")

分析：通过代码可以看到我们需要的数据，全都包裹在`<ul class="t clearfix">`里，用CSS的选择器定位到这里，再筛选数据就得到结果。查看`data`可知只有一个元素的列表，元素的`text`属性即是需要的文本信息，进行适当处理后格式化输出。

程序如下：

![](images/txt016_28.jpg "pagenumber_ebook=216,pagenumber_book=208")

程序运行结果：

![](images/txt016_29.jpg "pagenumber_ebook=216,pagenumber_book=208")

思考：将运行结果保存到文件中，程序如何修改。

【例14-4】爬取编程语言排行榜

TIOBE（The Importance Of Being Earnest）开发语言排行榜每月更新一次，依据的指数是基于世界范围内的资深软件工程师和第三方供应商提供，其结果作为当前业内程序开发语言的流行使用程度的有效指标。
