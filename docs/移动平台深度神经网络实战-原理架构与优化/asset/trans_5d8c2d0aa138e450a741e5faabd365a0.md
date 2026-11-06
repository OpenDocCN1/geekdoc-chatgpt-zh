

开发爬虫的时候总是会遇到一些特例，比如现在的百度图片，搜索结果并不是通过HTML直接返回，或者说HTML只负责返回第一页的搜索结果，而HTML加载完成后，再通过JavaScript脚本向服务器发送异步请求（AJAX），获取后续的图片信息。

目前大多数异步请求可能都是采用了JSON作为请求体和响应体的格式，因此解析AJAX请求的时候其实也就是根据抓取网站的请求包分析哪些请求是获取数据，然后使用代码模拟这些行为，一般来说，其实就是直接对AJAX地址进行`post`或`get`然后返回JSON数据。

通过代码调用浏览器或者类似于浏览器的引擎，直接通过代码自动化填写登录信息完成登录，然后通过代码模拟鼠标点击网页中的翻页按钮。每次翻页之后通过代码获取当前浏览器中显示的HTML并进行解析处理。这样就能解决一切JavaScript异步渲染的问题。

常用的引擎有`Selenium`、`WebDriver`、`Puppeteer`、`Cypress`、`dom4js`等。其中，`Puppeteer`和`Cypress`都是安装了一个Headless的`Chrome`/`Chromium`，然后直接通过浏览器执行操作。然而，我们仍然需要在产品研发和工程实践过程中，确保拥有合法版权，而对于企业研发产品而言，获取合法且高质量的训练数据是开发AI产品的重要一环。

## 10.2　图片数据爬虫实现

10.1节主要介绍了和爬虫相关的原理与概念，本节将会介绍爬虫的代码实现。由于我们关注的是爬虫的性能，因此需要开发一套分布式爬虫。分布式爬虫会采用`Hurricane`作为任务调度系统。

整个方案架构如图10-1所示。

![000](OEBPS/Images/317-1.jpg)

图10-1　Topology架构图

首先，控制单元将爬虫任务发送到消息队列中，然后`Hurricane`的`Spout`从消息队列中获取爬虫任务，接着将任务预处理成`Tuple`发送给`Bolt`，`Bolt`根据`Tuple`访问目标网站并爬取相应信息。爬虫如果发现网页中包含更多的任务，也会将抓取任务发布到消息队列中。爬取到的图片索引信息会记录到同一个数据库中，然后将图片放到分布式文件系统中。

百闻不如一见，现在就开始编程实战。

### 10.2.1　获取任务

先编写`SpiderTaskSpout.h`，用来从Redis中获取任务信息并发布给真正的计算节点，如代码清单10-1所示。

代码清单10-1　`SpiderTaskSpout.h`

```
 1 #pragma once
 2 
 3 #include "hurricane/spout/ISpout.h"
 4 #include <vector>
 5 #include <memory>
 6 
 7 namespace redox {
 8     class Subscriber;
 9 }
10 
11 class SpiderTaskSpout : public hurricane::spout::ISpout {
12 public:
13     virtual hurricane::spout::ISpout* Clone() override {
14         return new SpiderTaskSpout(*this);
15     }
16     virtual void Prepare(std::shared_ptr<hurricane::collector::OutputColle-ctor> outputCollector) override;
17     virtual void Cleanup() override;
18     virtual std::vector<std::string> DeclareFields() override;
19     virtual void NextTuple() override;
20 
21 private:
22     std::shared_ptr<hurricane::collector::OutputCollector> _outputCollector;
23     std::shared_ptr<redox::Subscriber> _subscriber;
24 };
```

第3行，包含了`ISpout.h`，该头文件中定义了`Hurricane`的`ISpout`接口类，该接口类定义了`Spout`所需要实现的接口。

第8行，声明了`redox::Subscriber`类，避免在C++中直接包含redox的头文件。

第11行，定义了`SpiderTaskSpout`类，该类继承自`ISpout`类，也就是实现了`Spout`类的必要接口。

第13～15行，定义了`Clone()`成员函数，该成员函数主要用于复制生成新的`Spout`对象。这里我们直接调用`SpiderTaskSpout`的复制构造函数，创建新的对象。

第16行，声明了`Prepare()`成员函数，该成员函数用于完成`Spout`对象的初始化工作。

第17行，声明了`Cleanup()`成员函数，该成员函数用于完成`Spout`对象的清理工作。

第18行，声明了`DeclareFields()`成员函数，该成员函数用于声明`Spout`的输出字段。

第19行，声明了`NextTuple()`成员函数，该成员函数用于生成数据元组。元组会输入到`Topology`中。

第22行，定义了`OutputCollector`指针，用于记录`Hurricane`的输出数据收集器指针，会在初始化的时候设置该指针。

第23行，定义了`redox::Subscriber`对象指针，用于记录Redis的事件监听器，负责订阅消息队列。

抓取任务的实际代码在`SpiderTaskSpout.cpp`中，具体实现如代码清单10-2所示。

代码清单10-2　`SpiderTaskSpout.cpp`

```
 1 #include "SpiderTaskSpout.h"
 2 #include "hurricane/util/StringUtil.h"
 3 #include <thread>
 4 #include <chrono>
 5 #include <redox.hpp>
 6 
 7 const std::string REDIS_HOST="localhost";
 8 const int32_t REDIS_PORT = 6379;
 9 
10 void HelloWorldSpout::Prepare(std::shared_ptr<hurricane::collector::Output-Collector> outputCollector) {
11     _outputCollector = outputCollector;
12     _subscriber = std::make_shared<redox::Subscriber>(REDIS_HOST, REDIS_PORT);
13 }
14 
15 void HelloWorldSpout::Cleanup() {
16     if (!_subscriber.get()) {
17         return;
18     }
19 
20     _subscriber->disconnect();
21     delete _redox;
22 }
23 
24 std::vector<std::string> HelloWorldSpout::DeclareFields() {
25     return { "url" };
26 }
27 
28 void HelloWorldSpout::NextTuple() {
29     sub.subscribe("spide", [](const string& topic, const string& msg) {
30         _outputCollector->Emit({ msg });
31     });
32 }
```

第8～9行，定义了两个和Redis连接相关的常量，`REDIS_HOST`是Redis的服务主机名，`REDIS_PORT`是Redis的服务端口号。

第10～13行，定义了`Prepare()`成员函数，该函数的参数是`OutputCollector`对象指针，这个对象是`Hurricane`创建的用于收集输出数据的对象，我们只负责引用。函数中我们先存储`OutputCollector`指针，然后创建Redis的`Subscriber`对象。

第15～22行，定义了`Cleanup()`成员函数，首先判定是否初始化了`Subscriber`对象，如果没有初始化直接返回，否则调用`Subscriber`的`disconnect()`成员函数断开与Redis服务器的连接，然后使用`delete`释放Redis对象。

第24～26行，定义了`DeclareFields()`成员函数。该`Spout`只输出一个`URL`字段，因此返回一个包含`URL`字符串的数组。

第28～31行，定义了`NextTuple()`成员函数，该函数调用了`Subscriber`对象的`subscribe()`成员函数，监听`spide`队列，当数据到来的时候会触发回调函数。回调函数包含两个参数——`topic`是数据主题，`msg`是消息参数，也就是需要抓取的网页的`URL`。我们将`url`通过`OutputCollector`发送出去。这样就完成了用来读取数据源并发送数据的`SpiderTaskSpout`的实现。

### 10.2.2　解析图片

处理节点接收任务后需要从网页里将所有的图片地址全部提取出来，如果是非图片链接还需要将链接推送到消息队列中。处理节点类声明代码在`UrlParseBolt.h`中，如代码清单10-3所示。

代码清单10-3　`UrlParseBolt.h`

```
 1 #pragma once
 2 
 3 #include "hurricane/bolt/IBolt.h"
 4 
 5 #include <string>
 6 #include <cstdint>
 7 
 8 class UrlParseBolt : public hurricane::bolt::IBolt {
 9 public:
10     virtual hurricane::bolt::IBolt* Clone() override {
11         return new UrlParseBolt(*this);
12     }
13     virtual void Prepare(std::shared_ptr<hurricane::collector::OutputCollector> outputCollector) override;
14     virtual void Cleanup() override;
15     virtual std::vector<std::string> DeclareFields() override;
16     virtual void Execute(const hurricane::base::Tuple& tuple) override;
17 
18 private:
19     void PerConnect(const std::string& url);
20     void PutImageToSet(
21             std::vector<std::string>& photoUrls,
22             std::vector<std::string>& comUrls)
23     void StoreImage(const std::string& imageUrl);
24 
25     std::shared_ptr<hurricane::collector::OutputCollector> _outputCollector;
26     int _socketFd;
27 };
```

第3行包含了头文件`IBolt.h`，该头文件中定义了`Hurricane`的`IBolt`接口类，该接口类定义了`Bolt`所需要实现的接口。

第8行，定义了`UrlParseBolt`类，该类继承自`IBolt`类，也就是实现了`Bolt`类的必要接口。

第10～12行，定义了`Clone()`成员函数，该成员函数主要用于复制生成新的`Bolt`对象。这里我们直接调用`UrlParseBolt`的复制构造函数，创建新的对象。

第13行，声明了`Prepare()`成员函数，该成员函数用于完成`Bolt`对象的初始化工作。

第14行，声明了`Cleanup()`成员函数，该成员函数用于完成`Bolt`对象的清理工作。

第15行，声明了`DeclareFields()`成员函数，该成员函数用于声明`Spout`的输出字段。

第16行，声明了`Execute()`成员函数，该成员函数用于接收前一个节点的元组，并处理后生成新的元组。元组会输入到`Topology`中。

第19行，定义了`PerConnect()`函数，用于创建到指定`URL`的HTTP连接。

第20～22行，定义了`PutImageToSet()`函数，用于将下载好的图片添加到爬取的网页集合中。

第23行，定义了`StoreImage()`函数，用于将指定`URL`的图片保存在本地。

第25行，定义了`OutputCollector`指针，用于记录`Hurricane`的输出数据收集器指针，会在初始化的时候设置该指针。

第26行，定义了`_socketFd`属性，定义了用于连接HTTP服务器的`Socket`连接。

处理节点类实现代码在`UrlParseBolt.cpp`中，如代码清单10-4所示。

代码清单10-4　`UrlParseBolt.cpp`

```
```
