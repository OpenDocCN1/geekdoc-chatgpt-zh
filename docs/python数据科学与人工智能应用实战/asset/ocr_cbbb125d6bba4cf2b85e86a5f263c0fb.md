# 6.4.2 创建 SQLite 数据库

DB Browser for SQLite 是一套开源 SQLite 管理工具，读者可在网络中搜索下载、安装，本书使用的是免安装版本。

# 1. 新增 SQLite 数据库和数据表

创建名为 Books.sqlite（也可自行命名，如 .db）的数据库和新增 Books 数据表，其字段数据类型和说明如表 6-8 所示。

| 字段名称 | 数据类型 | 字段说明 |
|---|---|---|
| id | TEXT | 书号，主键 |
| title | TEXT | 书名，不可为 Null |
| price | INTEGER | 书价 |

使用 DB Browser for SQLite 新增数据库和数据表的步骤如下所示。

Step1 选择本书配套资源中的 Tools 文件夹，双击 DB Browser for SQLite.exe 命令，即可启动 DB Browser for SQLite，如图 6-8 所示。

![](bbox=[0.17, 0.48, 0.765, 0.775])

Step2 单击上方工具栏中的“新建数据库”按钮，或选择“文件” → “新建数据库”命令，弹出“选择一个文件名保存”对话框，如图 6-9 所示。