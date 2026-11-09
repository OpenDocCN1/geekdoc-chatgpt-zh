1. 使用系统默认账号 dba 登录。
2. 在主菜单中依次点开【Linked Data】/【Quad Store Upload】，进入 RDF 上传界面。
3. 在 File 区域指定 music_1000_triples.nt，Named Graph IRI 填入 http://localhost:8890/DAV/music，随后按下【Upload】完成导入。
4. 切换菜单到【Linked Data】/【SPARQL】，打开查询页面。
5. 将 Default Graph IRI 设为 http://localhost:8890/DAV/music。
6. 在 Query 编辑区输入待执行的 SPARQL，单击【Execute】运行，效果参见图 3-21。

[PRE0]
#5. AllegroGraph：强调推理的商业图存储
AllegroGraph 由 Franz 公司推出，是一款成熟的 RDF 图存储产品。Franz 在人工智能方向积累深厚，早期专注 Common Lisp 与 Prolog 的工具开发，这使得 AllegroGraph 的语义推理能力较强。除基础存储外，还内置动态物化 RDFS++ 推理机、OWL2 RL 推理机、Prolog 规则推理、时空推理模块、社会网络分析库以及可视化 RDF 浏览器。同时提供 Java、Python、C#、Ruby、Clojure/Scala、Lisp 等多种语言的访问接口。图 3-22 展示了其整体架构。

[PAGE 122]

# 108 | 知识图谱：方法、实践与应用

除上述功能外，AllegroGraph 还支持动态物化的 RDFS++ 推理机、OWL2 RL 推理机、Prolog 规则推理系统、时空推理机制、社会网络分析库、可视化 RDF 图浏览器等。同时，AllegroGraph 支持 Java、Python、C#、Ruby、Clojure/Scala、Lisp 等多种语言的编程访问接口。图 3-22 所示为 AllegroGraph 的系统架构。

[PRE1]

下面演示 AllegroGraph 的安装、知识图谱导入与 SPARQL 查询。从官网下载服务器免费版，免费版对 RDF 三元组总数上限为 500 万条，且要求 64 位 Linux 环境。

+   步骤 1。解压安装包：
tar zxf agraph-6.4.1-linuxamd64.64.tar.gz。
解压后得到目录 agraph-6.4.1。

+   步骤 2 。进入 agraph-6.4.1 目录，执行安装脚本：install-agraph/home/xinwang/agraph。
目标路径为 /home/xinwang/agraph。安装过程中会询问系统配置，做为测试只需输入两次管理员 super 的密码，其余项直接回车采用默认值。

+   步骤 3 。执行脚本启动服务：
/home/xinwang/agraph/bin/agraph-control--config/home/xinwang/agraph/lib/agraph.cfg start
启动成功输出：
Daemonizing...
Server started normally: Running with free license of 5,000,000 triples; no-expiration.

[PAGE 123]

第3章 知识存储 | 109

Access AGWebView at http://127.0.0.1:10035

步骤 4。浏览器打开 http://127.0.0.1:10035，进入 AGWebView 管理界面，使用 super 登录。
步骤 5。新建仓库。在 “Create new repository” 区的 Name 框输入 testds，点击【Create】创建，如图 3-23 所示。

[PRE2]
# 图 3-23 AllegroGraph 创建数据库
步骤 6。导入知识图谱。在 Repository testds 页面选择【Load and Delete Data】/【Import RDF】/【from an uploaded file】，上方出现文件选择对话框，在 File 字段选择 music_1000_triples.nt，点击右下方【OK】。
步骤 7。查询图谱。点顶部菜单【Query】/【New】，进入 Edit query 页面输入 SPARQL，点击下方【Execute】执行，如图 3-24，查询结果如图 3-25（a）。点结果中首个实体 track_00001，可查看以其为主题的全部三元组详情，如图 3-25（b）。

[PRE3]
# 图 3-24 AllegroGraph 执行 SPARQL 查询

[PAGE 124]

# 110 | 知识图谱：方法、实践与应用

[PRE4]
步骤 8。执行脚本停止服务：

[PRE5]
#6. GraphDB：遵循 W3C 标准的 RDF 图库
GraphDB 是一款 RDF 三元组存储，其前身 OWLIM 长期是 W3C 语义网标准的主流实现。目前提供社区免费版、标准版和企业版，企业版可做多机集群分布式部署。
高层架构见图 3-26。

[PRE6]

自上向下介绍各组件：
(1) Workbench 是 GraphDB 的 Web 管理工具；
(2) Engine 为查询处理与推理核心，由查询优化器 Query Optimiser、推理机 Reasoner、存储层构成。

[PAGE 125]

# 第 3 章 知识存储 | 111

(2) Engine 是查询处理和推理引擎，由查询优化器（Query Optimiser）、推理机（Reasoner）、存储

# 112 | 知识图谱：方法、实践与应用

步骤 2。在命令行中执行 `java -server -Xmx4g -jar bigdata-bundled.jar`。

步骤 3。在浏览器打开 `http://localhost:9999`，进入 Blazegraph 的 Web 用户界面。

在 Blazegraph Web 用户界面中执行 SPARQL 查询并返回结果的效果，如图 3-27 所示。

[PRE7]
# 图 3-27 Blazegraph 的 Web 用户界面

#8. Stardog：多模型融合的知识图谱平台
Stardog 由美国 Stardog Union 公司开发，首个公开版本 Stardog 0.9 于 2012 年 2 月发布。该系统支持 RDF 图模型、SPARQL 查询、属性图模型、Gremlin 遍历语言、OWL2 标准、用户自定义推理与数据分析规则、虚拟图、地理空间查询及多种编程语言与网络接口。尽管发布较晚，其 OWL2 推理支持完善，同时提供全文检索、GraphQL 查询、路径查询及机器学习融合等能力，可作为知识图谱存储与查询平台。
产品分为企业版与社区版，社区版可免费用于非商业用途。以下为社区版安装演示。需在官网提交信息后通过邮件获取许可证文件 stardog-license-key.bin。推荐在 Linux 下部署。

# 步骤 1。使用如下命令在 CentOS Linux 系统上安装 Stardog:
[PRE8]

默认安装位置为 `/var/opt/stardog`。

# 步骤 2。设置 STARDOG_HOME 和 PATH 环境变量:
[PRE9]

# 步骤 3。将许可证文件复制到 STARDOG_HOME 目录:
[PRE10]

# 步骤 4。启动 Stardog 服务器，默认端口为 HTTP 5820:
[PRE11]

# 步骤 5。创建数据库并导入 RDF 文件:
[PRE12]

输出结果如下:
[PRE13]

表示创建数据库并导入 RDF 数据成功。

# 步骤 6。执行 SPARQL 查询，运行命令:
[PRE14]

返回结果为:
[PRE15]