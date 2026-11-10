百度地图 API 调用与 Python 爬虫效率提升实战

图 5-4 展示的是百度 API 配额管理面板的概览。面板中可见三类服务的使用情况：覆盖物定位当前使用率为 0%，日上限 100 万次，可提升；流量包未购买，余额 0.00 元，当前支持 0.00 万次，支持购买充值。地点检索当前使用率为 0%，日上限 3 万次，可提升；流量包未购买，余额 0.00 元，支持 0.00 万次，支持购买充值。路线规划当前使用率为 0%，日上限 3 万次，可提升；流量包未购买，余额 0.00 元，支持 0.00 万次，支持购买充值。
▲图 5-4 百度 API 配额管理界面

在了解配额情况后，下面以例 5-1 为例演示如何调用百度地图开放平台 Web 服务中的地点检索接口，并把结果写入 CSV。

## 【例 5-1】调取百度地图 Web 服务 API

输入：

[PRE0]

[PRE1]

输出：

[PRE2]

把返回的结果保存到 baidu_api_data.csv 文件中。

输入：

[PRE3]python

#打开文件

with open("baidu_api_data.csv","w") as apifile:

    #提取 output 中 results 部分字段并写入

    for i in range(len(api_rdata["results"])):

        #写入文件，以 "\t" 为分割符

        apifile.write(api_rdata["results"][i]["name"]+"\t")

        apifile.write(api_rdata["results"][i]["city"]+"\t")

        apifile.write(api_rdata["results"][i]["area"]+"\n")

[PRE4]

输出：

产出一个名为 baidu_api_data 的 csv 文件，内部结构如图 5-5 所示。
▲ 图 5-5 baidu_api_data.csv 内容

| A | B | C |
|---|---|---|
| 1 中国工商银行ATM(蟹岛路) | 北京市 | 朝阳区 |
| 2 中国农业银行ATM(酒仙桥东路) | 北京市 | 朝阳区 |
| 3 中国农业银行ATM(G1辅路) | 北京市 | 朝阳区 |
| 4 中国工商银行ATM(北京华侨城西 南) | 北京市 | 朝阳区 |
| 5 招商银行ATM(北京常惠营业厅) | 北京市 | 朝阳区 |
| 6 招商银行ATM(姚家园支行) | 北京市 | 朝阳区 |
| 7 北京银行ATM(上地支行) | 北京市 | 海淀区 |
| 8 中国工商银行ATM(房学路) | 北京市 | 昌平区 |
| 9 北京银行ATM(南绥路支行) | 北京市 | 西城区 |
| 10 招商银行ATM(星科大厦) | 北京市 | 朝阳区 |

对例 5-1 的说明如下。

### （1）请求形式

例 5-1 中使用 Requests 发起 GET 请求，也可改用参数传参的方式，示意如下：

[PRE5]python

params={"query":"ATM机","tag":"银行","region":"北京","output":"json","ak":"ak"}

res=requests.get("http://api.map.baidu.com/place/v2/search",params=params)

[PRE6]

注意：例 5-1 里 ak 为必传参数，本书提供的 ak 有效期有限，建议读者自行申请 ak① 后再动手实践。

其中 http 部分为百度地点检索接口的基础地址，params 集合以 key-value 形式携带请求参数，此种写法与例 5-1 的传参方式等价。

### （2）数据提取

接口返回的 api_rdata 本质是一个字典，可用 type(api_rdata) 检查类型，其结构可参考例 5-1 第一次输出。key 为 results 的 value 是一个长列表，列表内部再嵌套字典。例 5-1 第二次输入做的正是从这种复杂结构中抽取所需字段并写进 csv。

① ak 申请地址: 请读者进入“百度地图开放平台”，获取 “Web 服务 API”。

## 5.2.2 爬虫实战

为更直观体现 Python 在爬虫场景的优势，本节用少量代码实现一个简单的静态页面爬虫。相比传统方案，Python 爬虫语言简洁、入门快，且生态成熟，开发效率高。

本次演示的目标站点为中医药网，出于数据安全考虑仅抓取单个页面，若需更大规模数据可循环遍历。需求是获取页面中的药材名称与生长分布信息，如图 5-6 所示。
[PRE7]
▲图 5-6 网页信息

实现思路是先用 requests.get 获取目标 URL 的页面内容，再借助 BeautifulSoup 解析 HTML/XML 并筛选规则数据。BeautifulSoup 专注于 HTML/XML 解析，操作简便，适合提取指定标记。相关代码见例 5-2。

## 【例 5-2】爬取数据

输入：

[PRE8]

输出：

[PRE9]

从例 5-2 的输出可看出目标数据已成功获取。该例未使用复杂爬虫框架，若要处理更复杂的场景，可在此基础上扩展。

## 5.3 爬虫进阶——高效率爬虫

具备一定网络基础和 Python 编程能力后，完成一个基础爬虫并不困难。但在大数据环境下，所需数据量往往远超预期。例如爬取北京朝阳区朝外街道周边饭店相对容易，若要覆盖全国饭店，请求量可能达到百万乃至千万级，这时就需要高效率的爬虫方案。本章将介绍几种常见的高效实现方式。

## 5.3.1 多进程

Python 官方提供的 multiprocessing 包使用门槛很低，只需定义好任务函数，其余调度工作由包自动完成。该包支持子进程管理、进程间通信与共享数据，核心组件包括 Process、Queue、Pipe、Lock 等。

multiprocessing 下 Process 类构造函数的常见参数为：
[PRE10]
- group：进程组，基本不用。
- target：要执行的函数对象。
- args：位置参数元组。
- name：进程别名。
- kwargs：关键字参数字典。

常用方法见表 5-1。

## 表 5-1 常用方法表

| 方法 | 说明 |
|---|---|
| start() | 让进程就绪，等待 CPU 调度 |
| setName() | 为进程设置名称 |
| getName() | 获取进程名称 |
| setDaemon(True) | 将进程设为守护进程 |
| join() | 依次等待各进程结束后再继续执行 |
| run() | 进程被调度后自动运行，直接重写该方法即可自定义进程行为 |

## 【例 5-3】 开启进程

[PRE11]python

def run(name):

    #获取当前进程的名字

    tname = multiprocessing.current_process().name

    print("{} is starting,worker is {}".format(tname,name))

    return

if __name__ == '__main__':

    numList = []

    for i in range(5):

        p = multiprocessing.Process(target=run, args=(i,))

        numList.append(p)

        p.start()

        p.join()

[PRE12]

## 5.3.2 多线程

爬虫需要下载大量网页，单线程模式下每个请求都要等待服务器响应，总体耗时是各次等待之和，效率偏低。多线程可并行发起请求，减少空闲等待。

多进程与多线程都能实现并发，线程属于进程的一部分，可共享内存和变量，资源开销更小，但同步与加锁的处理相对繁琐。Python 通过 threading 包创建线程，构造参数如下：
[PRE13]
- group：线程组，基本不用。
- target：要执行的函数对象。
- name：线程别名。
- args/kwargs：传递给目标函数 的参数。

## 【例5-4】开启多线程

[PRE14]

请读者在上述例子的 run 函数中加入 time.sleep()，观察执行行为的变化，具体形式如下：