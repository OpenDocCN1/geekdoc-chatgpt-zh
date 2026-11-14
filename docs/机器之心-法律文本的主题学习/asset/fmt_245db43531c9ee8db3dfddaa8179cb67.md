
# Python标准库与第三方扩展库

## 标准库概述

标准库是完成最基本功能的函数或者模块集合，包括基本输入输出处理、字符串处理、存储管理、CPU管理、文件操作、数值计算、数据库管理、网络传输以及图形界面等。提供一个庞大且强大的标准库是`Python`语言的一个显著特点，也是当下`Python`备受欢迎的原因之一。强大的`Python`标准库（`Python Standard Library`）提供了文件处理、文本处理、操作系统支持、网络传输与安全通信管理以及数学和函数式编程支持等功能。

## 标准库分类

### 文件处理

`Python`用于文件处理的标准库有`os`、`file`、`glob`、`pathlib`、`tempfile`、`linecache`、`csv`、`configparser`、`pickle`、`sqlite3`和`shutil`等模块，包括文件读写操作、临时文件创建、文件路径处理、文件压缩与配置、`csv`文件处理、数据持久化、文件读取优化缓存、文件名正则查询、数据库管理支持以及文件状态查看等相关函数。

### 文本处理

用于文本处理的标准库有`re`、`string`、`textwrap`、`difflib`、`base64`、`codecs`等模块，包括字符串处理、格式化输出文本、正则表达式模糊匹配、文本序列差异计算、数据编码和解码、二进制数据转换、`Unicode`编码支持等工具和函数。

### 操作系统交互管理

用于操作系统交互管理的标准库有`os`、`time`、`io`、`logging`、`getpass`、`threading`、`multiprocessing`、`subprocess`、`queue`、`concurrent`、`platform`、`sys`、`builtins`、`inspect`、`contextlib`等模块，包括多线程与进程并发执行支持、内存读写`IO`和复用、日期时间和计时支持、日志生成与处理、同步队列支持、进程间通信与调用支持、运行时服务、系统环境交互、获取对象信息、管理上下文等函数。

### 网络传输和通信管理

用于网络传输和通信管理的标准库有`json`、`base64`、`webbrowser`、`wsgiref`、`uuid`、`ftplib`、`poplib`、`imaplib`、`smtplib`、`requests`、`scrapy`、`http`、`beautifulsoup`、`ssl`、`hashlib`、`secrets`、`paramiko`、`pycrypto`等模块，包括浏览器管理与操纵、通用唯一标识码、`http`请求、连接池支持、`cookies`会话支持、`http`代理、文件传输协议、`pop`协议、简单邮件传输协议、`html`解析、网络套接字支持、`W3C`格式支持、网络安全通信、`ssh`客户端管理、哈希摘要支持、加密解密处理等函数。

### 数学和函数式编程

用于数学和函数式编程的标准库有`random`、`math`、`fractions`、`statistics`、`itertools`、`functools`、`operator`等模块，包括随机数产生、数学函数、分数函数、统计函数处理、迭代工具管理、函数工具和基本运算符等函数。

## 常用标准库

标准库中比较常用的有与操作系统交互的`os`模块，与`Python`解释器和运行环境交互的`sys`模块，与文件管理操作有关的`codecs`模块，与模糊正则表达式匹配的`re`模块，与网络传输和应用请求有关的`requests`模块，以及与数学函数和公式有关的`math`模块，等等。

## 第三方扩展库

`Python`社区为用户提供了多达几十万个第三方扩展库，不同扩展库之间可以互相引用，使用方法与标准库一样。`Python`第三方扩展库的功能非常强大，覆盖了科学计算、数据可视化、数据分析、文本处理、图像处理、`Web`开发、数据库管理、`GUI`图形界面、网络爬虫、虚拟现实、密码学、网络应用开发、机器学习和自然语言处理等诸多领域。`Python`社区的项目`PyPI`（`Python Package Index`）是维护`Python`全球生态的主站，其网址为：`https://pypi.org`，可以使用`pip`下载安装`Python`库和模块。可以使用`Python`语言或者`C`语言来编写第三方扩展库。如果用`C`语言编写第三方扩展库，常用`Python`扩展工具`SIP`和`SWIG`将`C`语言程序模块转换为`Python`程序模块。`Python`扩展工具`Boost.Python`支持`Python`程序和`C++`程序互相调用。因此，`Python`对第三方扩展库的支持非常友好，容易实现各种接口库之间的灵活转换。

# Python科学计算和机器学习

## 科学计算库

`Python`常用于科学计算和数据分析的第三方扩展库有`Numpy`、`Scipy`和`Pandas`。

- **Numpy**：提供最基本的数值计算功能，包括高维数组、矩阵运算、矢量计算、广播函数、傅里叶变换、随机数生成以及线性代数等。`C`语言实现的`numpy`库计算速度快，性能优异，为`Scipy`、`Pandas`和`Sklearn`等其他库提供支持。
- **Scipy**：提供大量工程数值计算和数学算法，是比较高端的科学计算包，包括稀疏矩阵运算、线性代数计算、算法优化、快速傅里叶变换、信号处理、常微分方程求解、图像处理、稀疏图压缩、微积分、数值拟合、插值处理、数学物理常数、特殊函数等。`Scipy`库在`Numpy`基础上增加了很多工程数学上的常用函数，依赖于`Numpy`库。
- **Pandas**：也是基于`Numpy`的数据分析工具，建立数据类型和索引关系，实现从对索引的操作转换到对数据的操作，为快速操作大型数据提供了便利。
- **Matplotlib**：提供数据分析的二维可视化视图操作，是`Python`最主要的数据可视化功能库，有数百种的可视化显示效果，与商业化程序设计语言`Matlab`较为相似。

## 机器学习库

`Python`机器学习库有`scikit_learn`、`tensorflow`和`pytorch`等。

- **scikit_learn**（或称`sklearn`）：在机器学习任务中，当前最为流行的是`scikit_learn`，是最基本的`Python`第三方扩展库，广泛采用`Numpy`、`Scipy`以及`Pandas`进行高性能的数组处理、矩阵运算、线性代数和微积分运算，提供分类、逻辑回归、聚类、主成分分析、支持向量机、神经网络和强化学习等常用机器学习模型。`scikit_learn`可以很容易与其他`Python`库集成在一起应用，例如，用`Numpy`进行数组矢量化操作，用`Scipy`进行稀疏矩阵处理，用`Pandas`进行数据帧多维度索引访问，用`Matplotlib`进行数据可视化操作。
- **Tensorflow**：是谷歌大脑团队推出的基于`Python`的开源机器学习解决方案框架，是一种以数据图为基础、用图节点表示计算、用边代表张量的机器学习的应用方式。
- **PyTorch**：是由`Facebook`人工智能研究院推出的开源机器学习库，具有强大的张量计算能力，提供自动求导的深度神经网络功能，可以看作是一个加入了`GPU`（`Graphics Processing Unit`）的`Numpy`，可用于自然语言处理等应用。

# Python自然语言处理

## 主要NLP库

`Python`最著名的自然语言处理库是`NLTK`（`Natural Language Toolkit`），提供了大量简单易用的自然语言文本处理功能，包括语料库获取、字符串处理、中文分词、词性标记、语义解析、文本分类、搭配发现、概率估计、情感分析、文本相似度计算、评测指标、`WordNet`应用和统计机器学习等。

`Python`自然语言处理库还包括`Jieba`、`Pattern`、`TextBlob`、`Polyglot`、`Spacy`、`Gensim`、`Snownlp`等。

- **Jieba**：一个面向中文语言处理的`Python`库，可以进行中文分词、繁体分词、自定义词典、关键词抽取和词频统计等，支持精确模式、搜索引擎模式和全模式。
- **Pattern**：一个网络数据分析和挖掘工具，支持词性标注、`N`元搜索、文本分类、情感分析、可视化呈现和`WordNet`，同时也支持用于机器学习的向量空间模型、支持向量机、潜在语义分析、聚类和主题模型。
- **TextBlob**：一个用来进行自然语言处理和文本数据分析的`Python`库，主要包括词性标注、名词短语提取、社会情感分析、文本分类、文本翻译等功能。
- **Polyglot**：支持多语言环境下的词性标注、专业名词标记、语言嵌入、情感分析、形态分析和语言翻译。
- **Spacy**：商业开源领域中的最为快速的自然语言处理软件，提供文本分词、词性标注、命名实体识别、名词短语抽取等功能。
- **Gensim**：能够在大于内存的语料库上进行主题建模的自然语言处理工具，提供文件索引、相似度检索和语义建模等功能，可以说是在纯文本无监督的主题建模中的最为健壮的`Python`库。
- **Snownlp**：与`Jieba`类似，也是一个中文自然语言处理的`Python`扩展库，支持`unicode`编码的中文文本的分词、断句、词性标注、情绪判断、拼音、繁体转简体、关键词提取、概况总结文章、信息度量、文本相似度计算等功能。

# Python安装机器学习和自然语言处理类库

## Python安装

目前三大主流操作系统，即`Windows`、`macOS`、`Linux`，都支持`Python`的安装。安装程序和相关文档都可以从官方网站（`https://www.python.org`）下载。推荐使用最新的`Python`版本，当前的版本为`Python 3.x.y`，虽然大部分`Python`程序代码可以兼容`Python 2.x.y`，但是`Python 2`和`Python 3`还是存在一些区别，两者的区别可以浏览`https://wiki.python.org/moin/python2orpython3`。

### Windows安装

在`Windows`操作系统下安装`Python`，需要通过`Windows Installer`来配置和管理软件和应用服务。先要确定`Windows`操作系统启用`Windows Installer`安装服务，然后在官方上的`Python Releases for Windows`下载安装包。`Python 2`的安装包为`Windows XXX MSI Installer`，`Python 3`的安装包为`Windows XXX Executable Installer`。`XXX`为`x86`表明是32位计算机系统，`XXX`为`x86-64`表明是64位计算机系统。根据具体计算机系统情况，选择合适的安装包，按照提示进行默认安装，安装成功后进入命令行窗口，输入`python`，能够返回`Python`的版本信息，这表明已经成功安装了`Python`。

### Linux安装

目前较为流行的`Linux`操作系统是`ubuntu`，在`ubuntu`系统下安装`Python`有三种方法：

**第一种方法**：通过`ubuntu`操作系统官方提供的安装工具包。如果需要安装`Python 2`或者`Python 3`，在终端执行：

```
sudo apt-get install python2
sudo apt-get install python3
```

`Python 2`和`Python 3`中的`2`和`3`是主版本号，主版本号不同的软件说明进行了大量重写，无法实现向后兼容。`x`是次版本号，两个软件主版本号相同而次版本号不同，说明对软件进行了部分修改，但保持了完全向后兼容。安装成功后在终端输入命令进行确认：

```
python2 --version
python3 --version
```

`y`是内部版本号，主版本号和次版本号相同而内部版本号不同的软件表明来自同一个源代码，但是经过重新编译以适合更改处理器或平台情况。安装时必须提供主版本号和次版本号，内部版本号是可选的。

**第二种方法**：使用个人软件包存档`PPA`（`Personal Package Archive`）来安装，先要设置将要安装应用的来源，然后按照第一种方法进行安装。但是受到安装源的限制，很多时候不能获得更新的安装包。

**第三种方法**：对源代码进行编译安装。首先从官网下载相应版本的源代码并解压文件，然后设置编译和链接选项，最后执行编译操作。正确安装后，可以在终端中输入`python`命令进行验证。

## 包管理工具

### Pip

`Python`第三方扩展库的安装一般通过`Pip`（`Python Install Package`）进行管理，`Pip`安装工具是一个应用广泛的`Python`包管理工具，提供`Python`第三方扩展库的搜索、下载、安装、配置和卸载等功能。`Pip`的安装可以在终端中输入：

```
python -m ensurepip --upgrade
```

`Python 3.3`以后的版本都自动包含了`Pip`，作为标准库提供给用户使用。`Pip`安装和管理`Python`第三方扩展库非常方便，使用方法如下：

```
pip install numpy scipy pandas
pip install scikit-learn
pip install nltk jieba
pip install matplotlib
```

上述命令分别安装一些数值运算和工程数据处理的包、机器学习的包、自然语言处理的包和数据可视化的包。如果需要确认是否安装成功，可以在终端中输入`python`，进入`Python`环境，通过能否导入包操作来确定包安装的成功。

```
python -c "import numpy; print(numpy.__version__)"
```

已经安装的`Python`第三方扩展库可以列表显示出来，也可以显示指定包的安装情况和详细信息，查看可以升级的包已经进行包更新操作，对不再需要的`Python`包可以删除。在新的版本发布后，可以通过包管理工具`Pip`进行更新操作，如果需要更新`Python`包，操作如下：

```
pip list
pip show numpy
pip list --outdated
pip install --upgrade numpy
pip uninstall numpy
```

### Conda/Anaconda

在很多`Python`应用环境中，尤其各种应用包的版本更新迭代非常快，在这种情况下包管理和环境管理变得非常困难。作为数据分析的环境管理和包管理器，`Anaconda`被强烈推荐使用。`Anaconda`是一个侧重于科学计算的开源免费的`Python`发行版本，将所有数据科学、数学和工程的`Python`包捆绑在一起，并提供`Python`的标准库、几百个数据科学包及其依赖项。`Anaconda`通过`Conda`来管理包的安装、卸载以及更新操作，并且可以为每一个第三方包版本创建一个环境，将应用部署到相关的环境中。因此，`Conda`可以很容易实现不同版本的`Python`包共存，为不同的应用创建各自的运行环境。`Anaconda`的安装包或者安装程序可以浏览站点`http://continuum.io/downloads`，如果需要进一步了解`Anaconda`的使用方法，快速入门指南可以访问站点`https://conda.io/docs/testdrive.html`。在成功安装了`Anaconda`之后，安装任何`Python`第三方扩展库可以在终端中输入：

```
conda install numpy scipy pandas scikit-learn nltk jieba matplotlib
```

`Conda`的包管理功能和操作方法与`Pip`相似，可以查看已经安装的包，可以更新已经安装的包，也可以删除已经安装的包。

```
conda list
conda update numpy
conda remove numpy
```

## Polyglot安装详解

`Polyglot`是能够支持`Pipeline`功能的自然语言处理的`Python`第三方类库，可以实现分句、分词、词性标注、命名实体识别、情感分析、词嵌入和翻译等功能。安装`Polyglot`之前需要先安装它的一些依赖项`libicu-dev`、`pycld2`、`morfessor`、`morph2.en`等。使用`Pip`安装经常会遇到镜像源无法下载的情况，解决办法就是重新指定下载镜像源。选项“`-i`”是输入镜像地址，国内几个镜像源都可以尝试安装。

```
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple polyglot
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple pycld2 morfessor
```

上述安装完成后，可能还无法导入`Polyglot`类库，原因在于一个与`libicui18n`有关的动态链接库无法查找到，此时需要设置系统环境变量`LD_LIBRARY_PATH`。程序在运行时可能调用一些系统提供的或者第三方提供的共享动态链接库，一般情况下会在系统默认路径下查找，如果找不到所需文件，再去`LD_LIBRARY_PATH`环境变量指定的位置去找。可以在终端的`Shell`中直接为系统环境变量添加路径，然后更新设置。找到该动态链接库的位置，进入到相应目录，修改文件访问权限。

```
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:~/anaconda3/lib
source ~/.bashrc
```

这种属于临时修改环境变量，重新启动系统或者打开另一个终端，又恢复到修改之前的状态。如果要永久地修改环境变量`LD_LIBRARY_PATH`，可以在环境配置文件`~/.bashrc`中修改。用文本编辑程序打开环境配置文件，在`LD_LIBRARY_PATH`部分的后面添加“`: ~/anaconda3/lib`”，保存`~/.bashrc`文件并退出，在终端`Shell`中使用`source`更新设置。

```
echo 'export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:~/anaconda3/lib' >> ~/.bashrc
source ~/.bashrc
```

# 爬虫获取语料

## 网络爬虫介绍

随着计算机网络尤其互联网的迅速发展，每天增长的数据量从过去以`TB`（约一万亿字节）为计算单位发展到现在以`ZB`（约一百亿亿字节）来计算，其中很多是文本数据，它们大部分嵌入在网页结构当中，需要经过处理才能使用。从网页中获取数据并进行分析的过程称为网络爬虫，是获得机器学习和自然语言处理的数据集的有效途径之一。

网络爬虫的使用在法律上还存在很多争议，在涉及知识产权、个人隐私、反垄断和不正当竞争方面都有合法性的质疑。目前，网络爬虫的哪些行为是合法的还处于探索阶段，在很多国家，允许使用网络爬虫技术进行科学研究和教育学习。如果将网络抓取的数据用于商业盈利，那么数据来源和类型对网络爬虫合法性使用的判断将产生决定性的影响。例如，用爬虫从各个公司或机构官网抓取公开的联系人电话清单可能是被允许的，这些电话清单是客观真实的，网络爬虫程序在获取网页数据时，就性质而言相当于比较高效的人工访问用户。在抓取一些明显带有知识产权标记的内容时，传播和转载这类数据将是不合法的。在抓取层次比较浅的数据时，可以适用公开高效访问规则，但是在抓取层次较高的深度链接时，通过交叉分析可能涉及侵犯商业机密或者个人隐私。无论出于什么目的，使用网络爬虫程序都应以合法合规为前提，合理使用抓取的数据。

## 网络爬取过程

用户访问网站返回的结果为网页，通过程序代码自动下载所需网页，称为爬取。在`Python`中的第三方扩展`urllib`库支持网络请求和访问相关操作的函数，定义网页下载的函数：

```
import urllib.request

def download_page(url):
    response = urllib.request.urlopen(url)
    return response.read().decode('utf-8')
```
