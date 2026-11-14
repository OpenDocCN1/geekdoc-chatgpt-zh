每个具体角色网络标识都是唯一的，即分布在不同 IP 的机器上（或者同一个机但不同的端口）。

在实际运行中，各个角色的网络构建部分代码必须 100% 的相同。三者的分工如下：

+   - 服务端作为一个多方协调者，等待各个运算终端来连接。
+   - chief supervisors 会在启动时统一管理全局的学习参数，进行初始化或从模型载入。
+   - 其他的运算终端只是负责得到其对应的任务并进行计算，并不会保存检查点，用于 TensorBoard 可视化中的 summary 日志等任何参数信息。

整个过程都是通过 RPC 协议来通信的。

# 4.5.2 分布部署 TensorFlow 的具体方法

配置过程中，首先需要建立一个 server，在 server 中会将 ps 及所有 worker 的 IP 端口准备好。接着，使用 tf.train.Supervisor 中的 managed_session 来管理一个打开的 session。session 中只是负责运算，而通信协调的事情就都交给 supervisor 来管理了。

# 4.5.3 实例 20：使用 TensorFlow 实现分布式部署训练

下面开始实现一个分布式训练的网络模型。本例以“代码 4-8 线性回归的 TensorBoard 可视化.py”为原型，在其中添加代码将其改成分布式。

# 实例描述

在本机通过 3 个端口来建立 3 个终端，分别是一个 ps，两个 worker，实现 TensorFlow 的分布式运算。

具体步骤如下。

+   1. 为每个角色添加IP地址和端口，创建server

在一台机器上开 3 个不同的端口，分别代表 ps、chief supervisors 和 worker。角色的名称用 strjob_name 表示。以 ps 为例，代码如下：

代码4-15 ps

```
01 ......
02 #定义 IP 和端口
03 strps_hosts="localhost:1681"
04 strworker_hosts="localhost:1682,localhost:1683"
05
06 #定义角色名称
07 strjob_name = "ps"
08 task_index = 0
09 #将字符串转成数组
10 ps_hosts = strps_hosts.split(',')
11 worker_hosts = strworker_hosts.split(',')
```