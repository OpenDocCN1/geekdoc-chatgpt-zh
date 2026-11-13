理完一个物件后，就放上传送带，该机器设备接着处理到达的下一个物件；被送上传送带的物件传送一段时间后停止传送，到达下一个机器设备。

# 4.6.4 实验步骤

# 1. SimPy 安装

SimPy 可以同时在 Python2（要求 2.7 版本及以上）及 Python3（要求 3.2 版本及以上）上运行，可以通过 pip 命令轻松安装。

```
$ pip install simpy
```

如果安装了 pytest 包，在 SimPy 的安装路径下执行下列命令行，可以选择性地运行 SimPy 测试文件来了解软件是否可行。

```
$ pytest --pyargs simpy
```

# 2. SimPy 核心概念

SimPy 是离散事件驱动的仿真库。所有活动部件，如车辆、顾客，即便是信息，都可以用 process（进程）来模拟。这些 process 存放在 environment（环境）中。所有 process 之间及与 environment 之间的互动，都通过 event（事件）来进行。process 表达为 generators（生成器），构建 event（事件）并通过 yield 语句抛出事件。

当一个进程抛出事件时，进程会被暂停，直到事件被激活。多个进程可以等待同一个事件。SimPy 会按照这些进程抛出事件激活的先后来恢复进程。

其实进程中最重要的一类事件是 Timeout，这类事件允许一段时间后再被激活，用来表达一个进程休眠或将当前的状态持续指定的一段时间。这类事件通过 Environment.timeout 来调用。

+   Environment 决定仿真的起点/终点，管理仿真元素之间的关联，主要 API 有以下几种。
+   simpy.Environment.process: 添加仿真进程。
+   simpy.Environment.event: 创建事件。
+   simpy.Environment.timeout: 提供延时事件。
+   simpy.Environment.until: 仿真结束的条件（时间或事件）。
+   simpy.Environment.run: 仿真启动。

Resource/Store 是另一类重要的核心概念，但凡仿真中涉及的人力资源及工艺上的物料消耗都会抽象用 Resource 来表达，主要的方法是 request。Store 处理各种优先级的队列问题，通过方法 get/put 存放 item。

+   Store（抽象队列）: simpy.Store 表示存取 item 遵循仿真时间上的先来后到。
+   Resource（抽象资源）: simpy.Resource 表示人力资源或某种限制条件，例如，某个工序可调用的工人数、可以调用的机器数。

# 3. 实验环境

本实验的实验环境为 Python 3 的环境，代码如下。

```
# python 3.6 with SimPy
nnn
```