[PRE0]

### 9.5 AI 赋能 CI 性能测试实战

#### 9.5.2 源码托管准备

本文用到的 500 并发用户登录 WebTours 的 JMeter 脚本及配套数据，已推送到 GitHub，仓库概览如图 9-41 所示。对应代码可从文末提供的资源包中获取。

[PRE1]

#### 9.5.3 JMeter 输出格式设置

进入 JMeter 安装目录下的 bin 文件夹，打开 user.properties，在文件末尾追加一行 `jmeter.save.saveservice.output_format=xml` 后保存，设置位置如图 9-42 所示。

[PRE2]

#### 9.5.4 Jenkins 自动化搭建

按下面流程完成 Jenkins 的相关设置。

### 1）插件与工具部署

需要安装的插件有：
- Performance。

需要准备的软件有：
- JMeter。

### 2）任务创建与关联

新建一个自由风格的 Jenkins 任务，如图 9-43 所示。将任务名称填写为 “JmeterTest”，选择 “Freestyle project” 类型，点击 “确定” 按钮。

[PRE3]

图 9-43 新建自由风格项目界面

GitHub 仓库的关联配置见图 9-44。

[PRE4]

图 9-44 GitHub 仓库配置界面

在凭证处选用此前已创建的 GitHub 登录凭证，并指定对应的分支，如图 9-45 所示。

[PRE5]