均方根差值公式速算

给出一道计算需求：对两组变量做逐项相减，得到差值的平方再累加起来，除以样本总数，最后开平方。下面是对应的数学表达。

[PRE0]

这里的 n 代表参与计算的变量对数，x_i 与 y_i 分别表示两组数据中第 i 个取值。

如果需要在网页编辑器里预览 LaTeX 渲染效果，可参考图 8-5 的展示方式。

[PRE1]

## 8.15 用代码间接画图

ChatGPT 并不能直接输出图形，但可以配合外部工具实现绘图。常见做法有两类。

- 1）借助生成式对抗网络。GAN 这类深度模型能够产出逼真的图像。将自然语言描述送入 GAN 后，模型会据此合成对应画面。ChatGPT 负责撰写描述，再把描述喂给 GAN 完成成图。
- 2）借助 Python 绘图库。Matplotlib、Pillow 等库可以把数学表达式或描述转成图片。ChatGPT 先给出公式或描述，再由这些库渲染成折线图、散点图等，并导出为图片格式。

要注意，ChatGPT 生成的描述或公式可能存在歧义或不够精确，导致成图出现偏差。同时 GAN 或图形库的调用需要技术环境和数据支撑，使用时要结合具体场景谨慎选择。

下面以 Python 间接绘图为例。

请画一棵樱花树

以下是用 turtle 模块实现樱花树的示例程序。

[PRE2]

pen = turtle.Turtle()

pen.speed(0)

pen.hideturtle()

### 绘制樱花树

def draw_cherry_blossom_tree(pen, size):

    # 绘制树干

    pen.penup()

    pen.goto(0, -300*size)

    pen.pendown()

    pen.pensize(50*size)

    pen.setheading(90)

    pen.color("brown")

    pen.forward(200*size)

    # 绘制樱花

    for i in range(10):

        x = random.randint(-200*size, 200*size)

        y = random.randint(-200*size, 0)

        pen.penup()

        pen.goto(x, y)

        pen.pendown()

        pen.color("pink")

        pen.begin_fill()

        pen.circle(20*size)

        pen.end_fill()

### 主函数

def main():

    # 设置树的参数

    size = 1

    # 绘制樱花树

    draw_cherry_blossom_tree(pen, size)

    # 隐藏画笔

    pen.hideturtle()

    # 显示画布

    canvas.mainloop()

if __name__ == "__main__":

    main()

该程序调用 turtle 进行图形绘制，通过随机数决定樱花出现位置，循环绘制粉色花瓣。把代码粘贴到任意 Python 环境即可得到图案，图 8-6 为 IDLE 环境下生成的效果。

[PRE3]

## 8.16 快速生成数据表

ChatGPT 可以先输出包含表格信息的文本，再经清理和格式化后转为数据表。生成后可另存为文本，通过 Excel 导入功能读取为表格。

例如把下列信息整理成含产品名称、销售量 kg、销售金额 元 的表格：产品名称为苹果、香蕉、橙子、葡萄，销售量分别为 500、300、200、1000 kg，单价分别为 3、2、4、5 元/kg。

据此计算销售额，得到如下 Markdown 表格：

| 产品名称 | 销售量（kg） | 单价（元/kg） | 销售金额（元） |

| :--- | :--- | :--- | :--- |

| 苹果 | 500 | 3 | 1500 |

| 香蕉 | 300 | 2 | 600 |

| 橙子 | 200 | 4 | 800 |

| 葡萄 | 1000 | 5 | 5000 |

销售额 = 销售量 × 单价。整理成表格后便于查阅分析。Markdown 形式可直接在支持的编辑器中渲染，也能导入 Excel 或 Google Sheets 进行后续处理。导入效果见图 8-7。

[PRE4]

## 8.17 自动产出数据库说明

ChatGPT 能根据 SQL 语句生成数据库文档，包含结构和元数据，省去手动编写。涉及表、视图、存储过程、触发器、索引等定义均可覆盖。

示例 CREATE TABLE 命令：CREATE TABLE tpch.orders (O_ORDERKEY BIGINT, O_CUSTKEY INT, O_ORDERSTATUS CHAR(1), O_TOTALPRICE DECIMAL(15,2), O_ORDERDATE DATE, O_ORDERPRIORITY CHAR(15), O_CLERK CHAR(15), O_SHIPPRIORITY INTEGER, O_COMMENT VARCHAR(79), dummy text); ALTER TABLE tpch.orders ADD PRIMARY KEY (o_orderkey);

表名为 tpch.orders，对应的元数据如下：

| 列 名 | 数据类型 | 大 小 | 说 明 |

| :--- | :--- | :--- | :--- |

| O_ORDERKEY | BIGINT | | 订单键 |

| O_CUSTKEY | INT | | 客户键 |

| O_ORDERSTATUS | CHAR(1) | 1 byte | 订单状态：P = 未支付；F = 已完成；O = 订单被抛弃 |

| O_TOTALPRICE | DECIMAL(15,2) | 8 bytes | 订单总价 |

| O_ORDERDATE | DATE | 4 bytes | 下订单的日期 |

| O_ORDERPRIORITY | CHAR(15) | 15 byte | 订单优先级，最多 15 个字符 |

| O_CLERK | CHAR(15) | 15 byte | 处理订单的店员名称，最多 15 个字符 |

| O_SHIPPRIORITY | INTEGER | 4 bytes | 订单的发货优先级 |

| O_COMMENT | VARCHAR(79) | | 订单备注，最多 79 个字符 |

| dummy | TEXT | | 没有实际意义的列，用于兼容某些 SQL 语法，但无法插入任何数据 |

## 8.18 一键生成 SQL 语句

根据任务描述和数据需求，ChatGPT 可自动输出 SQL 代码，助力开发和数据分析提效。

需要提供任务说明及数据要素，如表名、列名、筛选条件、排序方式等。模型据此产出 SELECT、INSERT、UPDATE、DELETE 等语句，包含必要的表列和语法。

例如三个 PostgreSQL 表：Employee(id, name, department_id)、Department(id, name, address)、Salary_Payments(id, employee_id, amount, date)，请写出查询近三个月内薪资发放次数不超过 10 次的员工名单。

下面给出一个实现思路的查询：

[PRE5]

该查询先通过 JOIN 将 Employee 与 Salary_Payments 连接，限定最近三个月的记录，再按员工分组，用 HAVING 筛选发放次数 ≤10 的人群，最后按姓名排序输出。

## 8.19 跨库 SQL 方言转换

SQL 虽为标准，但不同 DBMS 在类型、方言、函数、操作符等方面存在差异。

常见方言：
- MySQL：支持存储过程、触发器，独有 LIMIT、DESCRIBE、SHOW 等。
- Oracle：支持存储过程、触发器，独有 ROWNUM、DUAL、SEQUENCE 等。
- SQL Server：支持存储过程、触发器，独有 TOP、IDENTITY、XML 类型等。
- PostgreSQL：支持存储过程、触发器，独有 ARRAY、FULL OUTER JOIN、ILIKE 等。
- SQLite：轻量型，独有 LIMIT、AUTOINCREMENT、BLOB 等。

函数与操作符差异：
- 字符串：MySQL 用 CONCAT()，Oracle 用 “||”。
- 数值：Oracle、SQL Server 的 ROUND 可控制小数位，MySQL 的 ROUND 多用于取整。
- 日期时间：Oracle、MySQL 提供 NOW()，SQL Server 用 GETDATE()。
- 聚合：Oracle、SQL Server 用 STDEV，MySQL 用 STD。
- 限制结果：MySQL、SQL Server 用 LIMIT，Oracle 通过 ROWNUM 实现。

举例把 PostgreSQL 语句转为 Oracle：select l_returnflag, l_linestatus, sum(l_quantity) as sum_qty, sum(l_extendedprice) as sum_base_price, sum(l_extendedprice * (1 - l_discount)) as sum_disc_price, sum(l_extendedprice * (1 - l_discount) * (1 + l_tax)) as sum_charge, avg(l_quantity) as avg_qty, avg(l_extendedprice) as avg_price, avg(l_discount) as avg_disc, count(*) as count_order from lineitem where l_shipdate <= date '1998-12-01' - interval '90 days' group by l_returnflag, l_linestatus order by l_returnflag, l_linestatus;

在 Oracle 中日期间隔写法不同，需使用 interval 关键字。转换后语句如下：

[PRE6]

其中日期间隔写作 '90' DAY，而非 '90 days'。

## 8.20 文本关键词抽取

ChatGPT 能解析输入文本并提取关键词，帮助理解用户意图。

借助自然语言处理和机器学习，如命名实体识别、关键词抽取、句法分析等，模型可识别人名、地名、时间、事件、主题等要素，从而给出更精准的回复。

示例文本：本文基于 PLC 的自动点胶机控制系统设计旨在通过现代化的技术手段提高点胶机的自动化程度和控制精度，提高生产效率和质量。在本文中，我们首先介绍了点胶机的工作原理和常用的点胶方法，然后分析了目前自动化控制系统中存在的问题和改进空间，并提出了基于 PLC 的自动点胶机控制系统的设计方案。接着，我们详细地讨论了控制系统的硬件和软件设计，并对其进行了验证和测试。最后，我们通过实验结果证明了本设计方案的可行性和有效性，能够满足点胶机自动化控制的要求，具有很高的实用性和推广价值。本文的主要工作量包括点胶机的工作原理和常用方法的调研、自动化控制系统的现状分析、基于 PLC 的自动点胶机控制系统的设计和实验验证等。整个过程需要进行大量的实验和数据处理，所需的时间和精力较为充分。预计本设计方案的实际应用将能够提高点胶机的自动化程度和控制精度，进而提高生产效率和质量。

抽取关键词：PLC、自动点胶机、控制系统、自动化程度、控制精度、生产效率、质量、工作原理、常用方法、现状分析、设计、实验验证、实用性、推广价值、数据处理。

## 8.21 取名