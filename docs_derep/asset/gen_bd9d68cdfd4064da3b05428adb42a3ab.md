数据库从概念到手搓安装的完整拆解

数据库应用程序（`DataBase Application`）是开发者用编程语言实现的工具，专门用来新建、修改、维护数据库文件与记录，让用户完成建档、录入、编辑、更新、报表输出等操作变得更顺手。应用会把用户的动作翻译成请求交给数据库管理系统执行，后者再去操作实际数据。市面上常见的代表有 `Navicat`、`PhpMyAdmin`、`DBeaver` 等。

数据库管理系统（`Database Management System`，`DBMS`）是面向数据库管理的通用系统软件，承担建库、维护以及向用户或数据库应用提供操作入口的工作。典型产品包括 `MySQL`、`MariaDB`、`Microsoft Access`、`Microsoft SQL Server`、`FileMaker Pro`、`Oracle Database`、`PostgreSQL` 等。常用的访问接口有 `ODBC`、`JDBC`、`ADO.NET`、`PDO`。

数据库（`DataBase`，`DB`），也就是 `RDBMS` 中的 `DB`，是指以电子形式长期保存在计算机内的结构化信息集合，按既定数据结构进行组织、存放和管理，通常由 DBMS 控制。可以理解为按结构归档的数据仓库。

**注**

在企业实践里，因 DBMS 已经包含了数据库，两者常被合并称呼为数据库，例如 `MySQL` 数据库、`Oracle` 数据库。

随着使用场景扩展，数据库品类日益丰富，不同类型适配不同需求。

➢ 关系型数据库：80 年代起成为主流，数据以列行构成的表格呈现。凭借表格模型访问结构化信息效率高且灵活。

➢ `NoSQL` 数据库：即非关系型数据库，擅长存储和处理非结构化或半结构化数据。伴随 Web 应用复杂化，其应用愈发广泛。

➢ 面向对象数据库：以对象形态保存数据，思路与面向对象编程相近。

## 数据库类型概述

➢ 分布式数据库：由分布在不同站点的两个或多个文件构成，可以放在同一物理位置的多台机器上，也可跨网络分散部署。

➢ 数据仓库：作为企业级数据的集中存储区，专为高速查询与分析而优化。

➢ 图形数据库：以实体及其关系为核心组织数据。

➢ OLTP 数据库：面向多用户并发执行大量事务的高速在线交易处理系统。

以上只是几十种现役数据库的冰山一角，还有大量面向科研、财务等特定领域的定制化方案。其中关系型和非关系型在企业里最为常见，覆盖面最广。

关系型数据库基于关系模型构建，目标把复杂结构归纳为简单的二元关系，即二维表形式。关系模型在二维表上叠加一对一、一对多、多对多等关联。简言之，关系型数据库就是由相互关联的多张二维表构成的数据组织方式。其特点如下：

➢ 易于理解与维护。数据以二维表行列存储，读取查询直观方便。

➢ 使用门槛低。采用结构化查询语言（SQL），SQL 已成为各厂商通用的行业标准。

➢ 操作能力强。借助 SQL 可对单表或多表执行复杂查询。

➢ 关系型数据库由关系型数据库管理系统（RDBMS）管理，R 代表 Relational，即表与表之间的关系。代表性产品有 MySQL、MariaDB、Oracle Database、SQL Server、DB2 和 PostgreSQL 等。

非关系型数据库也称 NoSQL 数据库，本意是“Not Only SQL”，与关系型数据库形成对比，具体差异后文详述。

### 18.3 MySQL 和 MariaDB 的两种安装方式

本节演示 MySQL 与 MariaDB 的手动部署。安装途径主要有源码编译和二进制包安装。为兼顾篇幅，这里采用混合方式：用软件包管理器（Yum/DNF）部署 MySQL 二进制包，并手动编译 MariaDB 源码包；同时简单说明用软件包管理器安装 MariaDB 的流程。

演示环境为 Rocky Linux 8，从 MySQL 安装视角看与 CentOS Linux 8 基本相同，可视为换皮版本。

#### 1. 通过软件包管理器（Yum/DNF）安装 MySQL 二进制包

（1）从 MySQL 官网获取官方编译好的二进制包。MySQL 版本众多，通常选用社区版，因其开源免费。下载流程如图 18-5 和图 18-6 所示。

点开“Download”后会提示登录 Oracle 账号，不登录也可直接点“No thanks，just start my download.”。MySQL 采用 C/S 架构，需下载包含客户端工具的 RPM Bundle 二进制包。

[PRE0]

图 18-5 下载 MySQL 社区版本

[PRE1]

图 18-6 选择 MySQL 安装包版本号、对应的操作系统等

（2）新建文件夹，将下载的安装包上传到服务器，解压后执行安装。

[PRE2][PRE3]

**注**

MySQL 服务端命令为 `mysqld`，客户端命令为 `mysql`。

至此 MySQL 已安装并启动。安装完成后会自动创建 `root@localhost` 用户，初始密码随机生成，记录在日志文件 `/var/log/mysqld.log` 中。

[PRE4]

连接数据库服务器的客户端命令格式为

`mysql -h 数据库地址\[-P 端口号\]-u 用户名-p \[数据库\] \[-e"SQL 语句"\]`

参数说明如下：

➢ `-h`：指定服务器地址，可填 IP 或主机名，本地建议用 `localhost`。

➢ `-P`：指定端口，默认 3306，也是 MySQL 与 MariaDB 的默认端口。

➢ `-u`：指定用户名。

➢ `-p`：提示输入密码，参数后留空，回车后再输入，避免明文。

➢ `\[数据库\]`：指定要登录的具体库，未指定则进入系统库。

➢ `[-e "SQL 语句"]`：登录后直接执行指定 SQL 并退出。

（3）登录服务器并重置 root 临时密码。

[PRE5]

**注**

命令行中不要直接写密码。`-p` 后空一格，回车后再输入。

退出客户端输入 `exit` 或 `quit` 即可。

通过 DNF 安装后主要文件位置如下：

➢ `/usr/lib/systemd/system/mysqld.service`：服务启动单元，可用 `systemctl start mysqld` 启动。

➢ `/usr/sbin/mysqld`：服务器二进制程序。

➢ `/usr/bin/mysql`：客户端二进制程序。

➢ `/etc/my.cnf`：主配置文件。

➢ `/var/lib/mysql`：默认数据目录。

**注**

可用 `rpm -ql mysql-community-server` 查看完整文件清单。

也可直接执行 `dnf install mysql mysql-server` 完成安装。

#### 2. 通过软件包管理器（Yum/DNF）安装 MariaDB 源码包

从 CentOS Linux 7 起默认自带 MariaDB，Rocky Linux 同理。安装时可勾选 MariaDB，或已装系统后执行：

`dnf install mariadb mariadb-server`

Linux 下 MySQL 与 MariaDB 互斥，只能其一。先停止卸载刚才的 MySQL，再安装 MariaDB。

[PRE6]

MySQL 已卸载，开始安装 MariaDB。

[PRE7]

#### 3. 手动编译 MariaDB 源码包

手动编译主要是为了自定义功能与安装路径。

（1）先用 DNF 准备编译依赖。

[PRE8]

（2）上传 MariaDB 源码包并解压，创建安装目录及对应用户组。

[PRE9]

（3）预编译以检查平台兼容性。常用编译选项如下：

➢ `-DCMAKE_INSTALL_PREFIX=`：指定安装目录。

➢ `-DMYSQL_DATADIR=`：指定数据目录。

➢ `-DMYSQL_USER=`：指定运行用户。

➢ `-DWITH_INNOBASE_STORAGE_ENGINE=1`：启用 INNOBASE 引擎。

➢ `-DWITH_ARCHIVE_STORAGE_ENGINE=1`：启用 ARCHIVE 引擎。

➢ `-DWITH_BLACKHOLE_STORAGE_ENGINE=1`：启用 BLACKHOLE 引擎。

➢ `-DWITH_PARTITION_STORAGE_ENGINE=1`：启用分区引擎。

➢ `-DWITHOUT_MROONGA_STORAGE_ENGINE=1`：不安装 MROONGA 引擎。

➢ `-DWITH_DEBUG=0`：关闭 DEBUG。

➢ `-DWITH_READLINE=1`：启用可编辑命令行支持。

➢ `-WITH_SSL=system`：使用系统 SSL 库。

➢ `-DWITH_ZLIB=system`：使用系统 ZLIB 库。

➢ `-DWITH_LIBWRAP=0`：禁用 LIBWRAP。

➢ `-DENABLED_LOCAL_INFILE=1`：允许本地导入。

➢ `-DMYSQL_UNIX_ADDR=`：指定 sock 文件路径。

➢ `-DDEFAULT_CHARSET=`：设定默认字符集。

➢ `-DDEFAULT_COLLATION=`：设定默认排序规则。

开始预编译。

[PRE10][PRE11]

（4）执行 `make && make install` 编译并安装。

[PRE12]

（5）安装后进行基础配置：设置环境变量、更改目录属主属组为 `mysql`、初始化数据、创建主配置文件、准备启动脚本等。

配置环境变量并调整权限。

[PRE13][PRE14]

用初始化脚本完成数据库初始化。

[PRE15]

准备主配置文件 `my.cnf`。老版本会自带模板，当前需手动创建。

[PRE16]

将 MariaDB 注册为系统服务并设为开机自启。

[PRE17][PRE18]

**注**

此处使用 `systemctl` 管理服务。

（6）启动服务并检查状态。

[PRE19]

（7）运行自带的安全脚本，设定 root 密码、远程登录权限、删除测试库等。

[PRE20][PRE21]

（8）用客户端登录验证。

[PRE22][PRE23]

源码编译方式到此完成，步骤比二进制安装更繁琐。

二进制安装本质是官方已编译完成，直接解压使用，类似 Windows 下的绿色软件。

有人会问为何要学编译方式。技术学习重在理解底层，二进制安装把编译和部署都替你完成了，学习空间有限；掌握编译过程才能真正吃透技术，因此建议把最难的部分也学透。

### 18.4 主配置文件

MySQL 与 MariaDB 的主配置文件互相兼容，可共用同一份 `my.cnf`，尽管两者不能共存。默认查找顺序为

`/etc/my.cnf` → `/etc/mysql/my.cnf` → `~/.my.cnf`

优先级最高的是 `/etc/my.cnf`，找到后不再继续查找。也可执行命令查看：

[PRE24]

配置文件以方括号划分作用域，官方称选项组。MariaDB 可从多个选项组读取配置。常用组如下：

➢ `[client]`：客户端程序通用选项。

➢ `[mysqld]`：服务器端通用选项。

➢ `[mariadb]`：MariaDB 服务器专属选项。

➢ `[mariadbd]`：MariaDB 服务器选项，10.4.6 及以后可用。

➢ `[mysqld_safe]`：`mysqld_safe` 启动脚本相关选项。

`[mysqld]` 组中常用的配置项有：

➢ `port=`：监听端口，默认 3306。

➢ `socket=`：本地通信使用的套接字文件。

➢ `user=`：启动服务的系统用户。

➢ `basedir=`：安装目录。

➢ `datadir=`：数据文件目录。

➢ `log_error=`：错误日志路径与名称。

➢ `pid-file=`：PID 文件。

➢ `skip-external-locking`：关闭外部锁定，提升稳定性。

➢ `key_buffer_size=`：索引缓存大小，影响索引处理速度。

➢ `max_allowed_packet=`：单次数据包最大传输尺寸。

➢ `sort_buffer_size=`：每个连接首次使用时的排序缓冲区。

➢ `net_buffer_length=`：客户端线程缓存初始容量。

➢ `myisam_sort_buffer_size=`：MyISAM 索引排序时使用的缓存。

[PRE25]