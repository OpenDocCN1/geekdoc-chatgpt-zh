当目的地址指向本机之外的设备时，流量先进入防火墙，被`PREROUTING`链初次处理，之后内核依据路由表决定走向，把报文交给`FORWARD`链做转发与否的判断，再由`POSTROUTING`链检查是否需要改写地址等信息，最后才送出。

（3）本机发出的流向（`OUTPUT`→`POSTROUTING`）。

本地应用向外发包同样要经过本机防火墙，先在`OUTPUT`链接受检查，随后按路由表选路，再进入`POSTROUTING`链决定是否修改地址头，最后真正发出。

这就是`Netfilter`定义的五条处理链，简称为五链。只要报文与本机相关，必定会触及其中至少一条链。

下面说四表。`Iptables`内置四张表：`Filter`、`Nat`、`Mangle`、`Raw`，各有分工，所有自定义规则的能力都被限定在这四张表之内。

➢ `Filter`表：主要负责放行或丢弃报文。
➢ `Nat`表：负责地址转换，可改写源 IP、目的 IP，也能改端口。
➢ `Mangle`表：对报文头做精细操作，拆解、修改后重新封装。
➢ `Raw`表：控制报文是否进入状态跟踪机制。

**注**
以上解释针对的是`Iptables`工具，`Firewalld`采用的逻辑并不相同。

可以这样想，表是规则的仓库。报文到达某条链时，`Netfilter`会先去关联的表里查找匹配的规则，再决定放行、丢弃、转发还是改写。为了更好理解四表五链的关系，用塔防游戏做个类比。

游戏开始！整张地图只有 5 个关卡，对应五链，所有敌人无论走哪条路都至少经过其中一个关卡。关卡本身不会攻击，所以给玩家 4 种能力卡片，对应四表，分别对应陆地、空中、海面、地下四类敌人。关卡可以插入卡片，插入什么卡片就获得什么攻击能力，一个关卡可以插多张卡，也就是能叠加多种能力。

汇总一下，五条关卡各插了哪些卡片，也就是各链能使用哪些表。

➢ `PREROUTING`链：`Raw`表、`Mangle`表、`Nat`表。
➢ `INPUT`链：`Mangle`表、`Nat`表、`Filter`表。
➢ `FORWARD`链：`Mangle`表、`Filter`表。
➢ `OUTPUT`链：`Raw`表、`Mangle`表、`Nat`表、`Filter`表。
➢ `POSTROUTING`链：`Mangle`表、`Nat`表。

**注**
`CentOS`/`RHEL 7`的`INPUT`链包含`Nat`表，`CentOS`/`RHEL 6`则没有。

反过来，四张表能被哪些链调用？

➢ `Filter`表：`INPUT`链、`FORWARD`链、`OUTPUT`链。
➢ `Nat`表：`PREROUTING`链、`INPUT`链、`OUTPUT`链、`POSTROUTING`链。
➢ `Raw`表：`PREROUTING`链、`OUTPUT`链。
➢ `Mangle`表：`PREROUTING`链、`INPUT`链、`FORWARD`链、`OUTPUT`链、`POSTROUTING`链。

把上述对应关系整理成一张完整的“四表五链”示意图，如图 10-6 所示。

[PRE0]

图 10-6 “四表五链”关系图

再看每条链上的匹配顺序。规则是从上往下依次检查，一旦命中就停止继续，匹配不到就执行链的默认策略。默认策略由管理员设定，白名单模式下默认丢弃，黑名单模式下默认放行。生产环境通常采用白名单，越严谨越好。

**注**
白名单把所有人都视为“坏人”，只放行明确标记为“好人”；黑名单则相反，把所有人当“好人”，只拒绝明确标记为“坏人”。

最后补两个实操知识点。

第一个知识点，防火墙匹配报文时常用的条件见表 10-1。

表 10-1 常用的匹配条件

[PRE1]

（续表）

[PRE2]

第二个知识点，匹配成功后常见的处理动作见表 10-2。

表 10-2 处理操作

[PRE3]

下面介绍两款 Linux 防火墙工具的用法。之所以讲两款，是因为`CentOS`/`RHEL 7`向`CentOS 8`/`RHEL 8`/`Rocky`过渡带来的变化。

### 10.4 Iptables 管理工具
# Iptables 与 Firewalld 管理工具

## Iptables 发展历程
`Iptables`的前身是`Ipfw`，该工具移植自`FreeBSD`，能在`Linux`内核中检测数据包，但操作繁琐。`Linux`内核 2 系列时演进为`Ipchains`，支持多条规则串联。内核 2.4 时`Ipchains`重构为列表式、四表结构的`Iptables`，实现更细致的访问控制。管理工具演进见表 10-3。

表 10-3 管理工具的发展历程

[PRE4]

## CentOS/RHEL 中的 Iptables 使用
`CentOS`/`RHEL 7`及更早版本默认用`Iptables`配置防火墙。虽然从 7 开始`Firewalld`已可用，但多数企业仍沿用`Iptables`。新工具初期不成熟、Bug 多，企业习惯等其稳定后再迁移，`Firewalld`当时正处于这个阶段。

## Iptables 语法与常用选项
语法格式：
`iptables [-t 表名] 选项 [规则链名] [条件匹配] [-j 采取的动作]`

常用选项见表 10-4。

表 10-4 `Iptables`的常用选项

[PRE5]

`-L`查看规则时可配合辅助选项，见表 10-5。

表 10-5 `-L`选项的常用辅助选项

[PRE6]

## 条件匹配与动作
条件匹配见表 10-6。

表 10-6 条件匹配

[PRE7]

（续表）

[PRE8]

处理动作见表 10-2。

注
（1）规则链名必须大写：`INPUT`|`OUTPUT`|`FORWARD`|`PREROUTING`|`POSTROUTING`
（2）表名必须小写：`filter`|`nat`|`mangle`|`raw`
（3）动作必须大写：`ACCEPT`|`DROP`|`SNAT`|`DNAT`|`MASQUERADE`|...
（4）匹配选项必须小写：`-s`|`-d`|`-m`|`-p`|...

## `iptables -L` 选项详解
`-L`用于查看表中的链及规则，输出信息量大，容易混淆。下面以`Filter`表为例说明各区域含义。

使用`Iptables`的`-L`查看`Filter`表所有链及规则，结果如图 10-7。

[PRE9]

图 10-7 `Filter`表中所有的规则链及规则

各区域说明见表 10-7。

表 10-7 `-L`选项显示内容含义

[PRE10]

加`-nvL`会多出几列：
➢ `pkts`列：该规则匹配到的报文数。
➢ `bytes`列：匹配报文的总字节数。
➢ `in`列：报文流入的网卡。
➢ `out`列：报文流出的网卡。

## 实践案例
下面通过递进案例把知识串起来，勤练才能内化。

案例
查看现有防火墙规则并全部清空。

[PRE11][PRE12]

注
本部分案例做成一条完整流程，从易到难走完即可掌握常用配置。

尝试把各链默认策略改为拒绝，把`INPUT`链默认改为拒绝。注意链上拒绝动作只能用`DROP`，不能用`REJECT`。

[PRE13]

此时只要目标为本机，默认都会被拒，因为链上无规则，只能走默认拒绝策略。

在`INPUT`链头部加一条规则，允许`ICMP`通过，这样可被`Ping`探测到主机活跃。

[PRE14][PRE15]

删除刚才允许`ICMP`的规则。

[PRE16]

再把`INPUT`链默认策略改回放行`ACCEPT`。

[PRE17]

添加限制端口的规则，仅允许指定网段访问本机 22 端口。22 端口为`SSH`，控制着远程登录权限。

[PRE18][PRE19]

添加拒绝规则，禁止任何主机用`TCP`或`UDP`访问本机 3306 端口。

[PRE20]

添加基于`MAC`地址的规则，禁止接收`MAC`为`00:0C:29:27:55:3F`的主机报文。

[PRE21]

开放本机一段连续端口。

[PRE22][PRE23]

特别提醒：`Iptables`规则不保存则重启失效。永久保存需执行“`service iptables save`”，写入`/etc/sysconfig/iptables`。

### 10.5 Firewalld 管理工具
从`CentOS`/`RHEL 7`起，`Firewalld`成为默认管理工具，`Iptables`虽保留但已退居次要。7 系列可视为向`Firewalld`过渡的版本。

### Firewalld 概述
`Firewalld`是动态防火墙管理工具，`Iptables`是静态的。静态模式把规则写入`/etc/sysconfig/iptables`，修改需整表重载，容易在高并发场景丢包或耗资源。动态模式可增删单条规则，不破坏已有会话，无需重启服务。

### Firewalld 与 Iptables 的区别
相同点：两者本身不做报文过滤，均依赖内核`Netfilter`；`Firewalld` v0.6.0 后规则交由`Nftables`处理。不同点：`Iptables`用四表五链，`Firewalld`引入区域`Zone`概念，按可信级别划分网络，简化配置。

初始化提供 9 个区域，见表 10-8。

表 10-8 `Firewalld`的 9 个初始化区域

[PRE24]

注
默认使用`public`区域为默认区。

另提供 9 个配置文件：`block.xml`、`dmz.xml`、`drop.xml`、`external.xml`、`home.xml`、`internal.xml`、`public.xml`、`trusted.xml`和`work.xml`，位于“`/usr/lib/firewalld/zones/`”。

报文处理优先级：
（1）源地址绑定到特定区域则执行该区域规则。
（2）未绑定则使用入站网卡对应的区域规则。
（3）网卡也未绑定则使用默认区域规则。
优先级：源地址绑定区域 > 网卡绑定区域 > 默认区域。

### 配置方式
`Firewalld`提供三种配置方式：`Firewall-config`图形界面、`Firewall-cmd`命令行，以及直接编辑`/etc/firewalld/`下的`XML`文件。图形界面适合有桌面的环境。

[PRE25]

图 10-8 图形化管理工具`Firewall-config`

[PRE26]

图 10-9 命令行管理工具`Firewall-cmd`

[PRE27]

图 10-10 `XML`配置文件