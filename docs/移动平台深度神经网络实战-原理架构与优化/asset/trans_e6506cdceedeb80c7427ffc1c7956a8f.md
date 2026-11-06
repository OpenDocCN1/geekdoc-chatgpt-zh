

## DataReaderSpout 类定义

第11行，定义了`DataReaderSpout`类，该类继承自`ISpout`类，即实现了`Spout`类的必要接口。

第13～15行，定义了`Clone()`成员函数，该成员函数主要用于复制生成新的`Spout`对象。这里直接调用`SpiderTaskSpout`的复制构造函数，创建新的对象。

第16行，声明了`Prepare()`成员函数，该成员函数用于完成`Spout`对象的初始化工作。

第17行，声明了`Cleanup()`成员函数，该成员函数用于完成`Spout`对象的清理工作。

第18行，声明了`DeclareFields()`成员函数，该成员函数用于声明`Spout`的输出字段。

第19行，声明了`NextTuple()`成员函数，该成员函数用于生成数据元组。元组会输入到`Topology`中。

第22行，定义了`OutputCollector`指针，用于记录`Hurricane`的输出数据收集器指针，会在初始化的时候设置该指针。

第23行，定义了`redox::Subscriber`对象指针，用于记录`Redis`的事件监听器，负责订阅消息队列。

接着编写`DataReaderSpout.cpp`，用于实现`DataReaderSpout`类，如代码清单7-2所示。

### 代码清单7-2 `DataReaderSpout.cpp`
```
 1 #include "SpiderTaskSpout.h"
 2 #include "hurricane/util/StringUtil.h"
 3 #include <thread>
 4 #include <chrono>
 5 #include <redox.hpp>
 6 #include <fstream>
 7 
 8 const std::string REDIS_HOST="localhost";
 9 const int32_t REDIS_PORT = 6379;
10 
11 void DataReaderSpout::Prepare(std::shared_ptr<hurricane::collector::OutputCollector> outputCollector) {
12     _outputCollector = outputCollector;
13     _subscriber = std::make_shared<redox::Subscriber>(REDIS_HOST, REDIS_PORT);
14 }
15 
16 void DataReaderSpout::Cleanup() {
17     if (!_subscriber.get()) {
18         return;
19     }
20 
21     _subscriber->disconnect();
22     delete _redox;
23 }
24 
25 std::vector<std::string> DataReaderSpout::DeclareFields() {
26     return { "record" };
27 }
28 
29 void DataReaderSpout::NextTuple() {
30     sub.subscribe("data", [](const string& topic, const string& fileName) {
31         std::ifstream inputFile(fileName.c_str());
32         std::vector<double> record;
33 
34         while (inputFile) {
35             double element;
36 
37             inputFile >> element;
38             record.push_back(element);
39         }
40 
41         _outputCollector->Emit({ record });
42     });
43 }
```

第8～9行，定义了两个和`Redis`连接相关的常量，`REDIS_HOST`是`Redis`的服务主机名，`REDIS_PORT`是`Redis`的服务端口号。

第11～14行，定义了`Prepare()`成员函数，该函数的参数是`OutputCollector`对象指针，这个对象是`Hurricane`创建的用于收集输出数据的对象，我们只负责引用。函数中我们先存储`OutputCollector`指针，然后创建`Redis`的`Subscriber`对象。

第16～23行，定义了`Cleanup()`成员函数，首先判定是否初始化了`Subscriber`对象，如果没有初始化则直接返回，或者调用`subscriber`的`disconnect()`成员函数断开与`Redis`服务器的连接，然后使用`delete`释放`Redis`对象。

第25～27行，定义了`DeclareFields()`成员函数。该`Spout`只输出一个`record`字段，因此返回一个包含`record`字符串的数组。

第29～43行，定义了`NextTuple()`成员函数，该函数调用了`Subscriber`对象的`subscribe()`成员函数，监听`data`队列，当数据到来的时候会触发回调函数。回调函数包含两个参数：`topic`是数据主题，`fileName`是数据参数，即需要读取的文件名。首先创建`ifstream`对象，打开`fileName`对应的文件。然后创建`double`类型的`vector`对象，用来存储文件中的数据。接着不断读取文件，直到读取到文件末尾，每次读入一个浮点数，并添加到`record`数组中。最后将`record`数组通过`OutputCollector`发送出去。这样就完成了用来读取数据源，发送数据的`DataReaderSpout`。

## PCABolt 类定义

接下来编写`PCABolt.h`，用于定义`PCABolt`类，如代码清单7-3所示。

### 代码清单7-3 `PCABolt.h`
```
 1 #pragma once
 2 
 3 #include "pca.h"
 4 #include "hurricane/bolt/IBolt.h"
 5 
 6 #include <string>
 7 #include <cstdint>
 8 
 9 class PCABolt : public hurricane::bolt::IBolt {
10 public:
11     virtual hurricane::bolt::IBolt* Clone() override {
12         return new UrlParseBolt(*this);
13     }
14     virtual void Prepare(std::shared_ptr<hurricane::collector::OutputCollector> outputCollector) override;
15     virtual void Cleanup() override;
16     virtual std::vector<std::string> DeclareFields() override;
17     virtual void Execute(const hurricane::base::Tuple& tuple) override;
18 
19 private:
20     std::shared_ptr<hurricane::collector::OutputCollector> _outputCollector;
21     int _size;
22     pca _pca;
23 };
```

第3行，包含了头文件`pca.h`，其中定义了`PCA`的算法实现，我们将在后面介绍如何实现`PCA`算法。

第4行，包含了头文件`IBolt.h`，其中定义了`Hurricane`的`IBolt`接口类，该接口类定义了`Bolt`所需实现的接口。

第9行，定义了`PCABolt`类，该类继承自`IBolt`类，也就是实现了`Bolt`类的必要接口。

第11～13行，定义了`Clone()`成员函数，该成员函数主要用于复制生成新的`Bolt`对象。这里我们直接调用`PCABolt`的复制构造函数，创建新的对象。

第14行，声明了`Prepare()`成员函数，该成员函数用于完成`Bolt`对象的初始化工作。

第15行，声明了`Cleanup()`成员函数，该成员函数用于完成`Bolt`对象的清理工作。

第16行，声明了`DeclareFields()`成员函数，该成员函数用于声明`Spout`的输出字段。

第17行，声明了`Execute()`成员函数，该成员函数用于接收前一个节点的元组，并在处理后生成新的元组。元组会输入到`Topology`中。

第20行，定义了`OutputCollector`指针，用于记录`Hurricane`的输出数据收集器指针，并会在初始化的时候设置该指针。

第21行，定义了`_size`属性，表示每个`PCA`样本的数据长度。

第22行，定义了`_pca`属性，用于进行`PCA`处理的算法对象。

接下来编写`PCABolt.cpp`，用于实现`PCABolt`类，如代码清单7-4所示。

### 代码清单7-4 `PCABolt.cpp`
```
 1 #include "PCABolt.h"
 2 #include "hurricane/util/StringUtil.h"
 3 
 4 #include <iostream>
 5 #include <sstream>
 6 #include <cstring>
 7 
 8 const int MAX_SIZE = 1024;
 9 const std::string PCA_MODEL_FILE = "sample"
10 
11 void PCABolt::Prepare(std::shared_ptr<hurricane::collector::OutputCollector> outputCollector) {
12     _outputCollector = outputCollector;
13     _size = 0;
14 }
15 
16 void PCABolt::Cleanup() {
17 }
18 
19 std::vector<std::string> PCABolt::DeclareFields() {
20     return { "modelFile" };
21 }
22 
23 void PCABolt::Execute(const hurricane::base::Tuple& tuple) {
24     std::vector<double> record = tuple[0].GetValue<std::vector<double>>();
25     _pca.add_record(record);
26     _size ++;
27 
28     if (_size >= MAX_SIZE) {
29         _outputCollector->Emit({ _size });
30         _size = 0;
31         _pca.save(PCA_MODEL_FILE);
32         _pca = pca();
33     }
34 }
```

第8～9行，定义了两个常量，`MAX_SIZE`表示每一组样本的最大长度，`PCA_MODEL_FILE`是`PCA`模型文件的文件名。

第11～14行，定义了`Prepare()`成员函数，该函数的参数是`OutputCollector`对象指针，这个对象是`Hurricane`创建的用于收集输出数据的对象，我们只负责引用。函数先存储`OutputCollector`指针，然后将`_size`设置为0。

第16～17行，定义了`Cleanup()`成员函数，这里`PCABolt`不需要做清理工作，因此这里定义一个空函数即可。

第19～21行，定义了`DeclareFields()`成员函数。该`Bolt`只输出一个`modelFile`字段，因此返回一个包含`modelFile`字符串的数组。

第23～34行，定义了`Execute()`成员函数，该函数首先从输入的元组中获取第一个元素，并将该元素转换成`double`数组。然后将数组中的元素添加到`PCA`算法对象中，准备进行数据处理，并且将`_size`加上记录数量。

第28～33行，如果`_size`超过`PCA`要求的最低数据量，可以调用`pca`的`save()`函数来完成一批数据的`PCA`计算，并将`PCA`计算结果保存到模型文件中，最后创建新的`pca`对象，准备下一轮`PCA`计算。

## PCATopology 构建

接着编写`PCATopology.h`头文件，如代码清单7-5所示。

### 代码清单7-5 `PCATopology.h`
```
 1 #pragma once
 2 
 3 namespace hurricane {
 4     namespace topology {
 5         class Topology;
 6     }
 7 }
 8 
 9 #include "hurricane/base/externc.h"
10 
11 BEGIN_EXTERN_C
12 hurricane::topology::Topology* GetTopology();
13 END_EXTERN_C
```

第5行，声明了`Topology`类型，这里主要是前向声明。

第12行，声明了`GetTopology()`函数，这里使用`BEGIN_EXTERN_C`和`END_EXTERN_C`将其包围起来，确保符号导出时以`C`符号形式导出。

最后编写`PCATopology.cpp`，构建拓扑网络，具体实现见代码清单7-6。

### 代码清单7-6 `PCATopology.cpp`
```
 1 #include "PCATopology.h"
 2 #include "DataReaderSpout.h"
 3 #include "PCABolt.h"
 4 
 5 #include "hurricane/topology/Topology.h"
 6 
 7 hurricane::topology::Topology* GetTopology() {
 8     hurricane::topology::Topology* topology = new hurricane::topology::Topo_logy("pca-topology");
 9 
10     topology->SetSpout("data-reader-spout", new DataReaderSpout)
11         .ParallismHint(1);
12 
13     topology->SetBolt("pca-bolt", new PCABolt)
14         .Random("data-reader-spout")
15         .ParallismHint(3);
16 
17     return topology;
18 }
```

第5行，包含了头文件`Topology.h`，其定义了`Topology`类。

第7行，定义了`GetTopology()`函数，该函数会构建并返回`Topology`对象，定义了整个计算网络。

第8行，调用`Topology`的构造函数创建一个`Topology`对象，`Topology`的名称是`pca-topology`。

第10～11行，创建`DataReaderSpout`对象，调用`topology`的`SetSpout()`构造函数，并将该`Spout`加入到网络中，将其命名为`data-reader-spout`，然后调用`ParallismHint()`将其并行度设置为1。

第13～15行，创建`PCABolt`对象，调用`topology`的`SetBolt()`成员函数将`Bolt`加入到网络中，将其命名为`pca-bolt`，使用`Random()`将其接到`data-reader-spout`后，然后调用`ParallismHint()`将其并行度设置为3。

第17行，返回创建好的`Topology`对象指针，这样就完成了`Topology`的编写。整个`Topology`的结构图如图7-10所示。

![000](OEBPS/Images/179-1.jpg)

图7-10　`PCATolology`结构示意图

## 7.4.3 实现PCA

`Hurricane`是作为`PCA`的调度框架，接下来要实现实际的`PCA`算法。

首先编写`pca.h`，这是`PCA`类的头文件，如代码清单7-7所示。

### 代码清单7-7 `pca.h`

