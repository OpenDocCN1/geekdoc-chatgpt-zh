本部分额外引入了知识图谱的可视化工具，帮助你直观理解实体与关系网络的语义构成。掌握完本节内容后，既能把 GraphRAG 在本地完整部署并调试，也能将这套方法灵活应用到大规模私有知识问答系统的搭建中。

#### 4.3.1 GraphRAG 安装与项目初始化

##### 1. Python 环境准备与版本要求

微软开源的 GraphRAG 仓库内置了一套 CLI 命令行工具，通过终端指令即可完成构建与检索。本次实操采用 Anaconda 来管理虚拟环境，Conda 在创建环境时可以指定 Python 版本。官方推荐使用 3.10 至 3.12 区间。接下来新建一个指定版本的虚拟环境，执行如下命令。

[PRE0]

name 用来命名虚拟环境，python=3.11 用于锁定 Python 版本。环境创建完成后，需要激活进入，执行如下命令。

[PRE1]

##### 2. 安装 Microsoft GraphRAG 的 CLI 工具

进入虚拟环境后即可安装 CLI，执行如下命令。

[PRE2]

输出结果如图4.7所示。

##### 3. 验证 Microsoft GraphRAG 的 CLI 工具是否安装成功

安装完毕后，先确认工具是否可用。执行如下命令。

[PRE3]

##### 4. Microsoft GraphRAG 的 CLI 工具的基本使用方法

命令行工具必然包含多个参数，可通过如下命令查看帮助。

[PRE4]

输出结果如图4.9所示。

[PRE5]

图4.9 CLI命令参数

通过 graphrag --help 返回的信息主要围绕构建与检索，说明如下。

- init: 初始化 GraphRAG 配置文件。
- index: 构建 GraphRAG 索引，即索引过程。
- query: 执行 GraphRAG 查询，即检索过程。
- update: 对现有索引做增量更新。

##### 5. 创建检索项目文件夹并上传数据集

先执行如下命令新建项目目录。

[PRE6]

创建完成后如图4.10所示。

[PRE7]

随后把数据集文件放到该目录下，如图4.11所示。

[PRE8]

##### 6. 初始化 GraphRAG 项目文件

GraphRAG 的初始化本质是生成一套默认配置文件，后续可据此控制索引流程。因此第一步是执行 graphrag init 生成默认配置，执行如下命令。

[PRE9]

其中 --root 用来指定配置文件存放位置，./ 表示当前目录。

执行后会生成两份配置文件和一个文件夹，如图4.12所示，具体如下。

- settings.yaml: 主配置文件，涵盖从索引构建到检索的各项设置。
- .env: 环境变量文件，主要存放 API_KEY 等敏感信息，并在 settings.yaml 中引用。
- prompts/: 提示词文件夹，存放 GraphRAG 默认使用的提示。

[PRE10]

打开 .env 文件填入 OpenAI API-KEY，填写效果如图4.13所示。

[PRE11]

如需更换模型或切换代理地址，可打开 setting.yaml 修改 model 和 api_base 参数。修改完成后建议做一次基础对话测试，确保大语言模型能正常运行并正确响应。

#### 4.3.2 GraphRAG 索引构建实操

完成 .env 与 settings.yaml 的配置并准备好数据文件后，即可启动索引构建。通过 CLI 执行如下命令。

[PRE12]

执行过程中可以看到索引构建流程逐步推进，如图4.14所示，最终会显示 All workflows completed successfully 的提示。

[PRE13]

构建完成后会在根目录下生成 cache、logs 和 output 三个文件夹，分别存放缓存、日志和输出文件，如图4.15所示。

[PRE14]

其中 output 目录下会产生多个 xxx.parquet 文件，它们就是 GraphRAG 的索引文件。Parquet 内部保存了知识图谱的核心要素，如实体、关系、社区信息及文本单元，共同构成完整图谱。如图4.16所示。

[PRE15]

索引阶段的主要输出内容如表4.2所示。

表4.2 索引阶段的主要输出内容

| 表名 | 文件名 | 内容 | 包含信息 |

| :--- | :--- | :--- | :--- |

| 实体表 (Nodes Table) | create_final_nodes.parquet | 知识图谱中的实体节点（如人、地点、组织） | 实体的名称（如 "John Doe"）、实体的类别（如 "PERSON"、"ORGANIZATION"、"LOCATION"）、社区相关的信息（如实体所属的社区）、实体的度数 (degree) |

| 关系表 (Relationships Table) | create_final_relationships.parquet | 知识图谱中实体之间的关系（即图谱的边） | 两个实体之间的关系描述（例如 "works for"、"lives in"）、关系的强度（数值化，用于衡量关系的显著性或重要性） |

| 实体嵌入表 (Entity Embedding Table) | create_final_entities.parquet | 实体的语义嵌入，用于表示实体的语义信息 | 支持语义搜索（通过嵌入计算实体之间的相似度） |

| 社区报告表 (Community Reports Table) | create_final_community_reports.parquet | 社区的摘要信息 | 支持全局搜索（通过社区信息回答关于数据集整体的问题） |

| 文本单元表 (Text Units Table) | create_final_text_units.parquet | 被切分的原始文本单元 (TextUnit) | 将知识图谱和原始文本结合，为 LLM 提供上下文支持 |

| 社区表 (Community Table) | create_final_communities.parquet | 每个社区基本情况 | 社区成员列表、社区内实体的关系、社区特征等 |

| 文件表 (Documents Table) | create_final_documents.parquet | 用于记录所有参与知识图谱构建的文件情况 | 文件 ID、文件标题、文本内容等 |

> **说明**
>
> 为什么用Parquet格式保存知识图谱？
>
> (1) 高效存储。
>
> * 知识图谱中的数据通常是结构化的，包含大量的实体、关系、嵌入等。
> * Parquet的列式存储能够显著减少磁盘占用，同时提高读取效率。
>
> (2) 快速读取。
>
> * 查询阶段需要快速加载实体、关系、嵌入等数据到内存中。
> * Parquet支持按需加载所需的列，避免了不必要的数据读取。
>
> (3) 兼容性好。
>
> * Parquet是一个开放的标准，广泛支持各种数据处理工具（如Pandas、Spark、Hadoop）。
> * GraphRAG可以在Python中使用Pandas或其他工具轻松读取这些文件。

#### 4.3.3 知识图谱表格的读取与查阅

借助 Pandas 可以加载并浏览这些关键表格。以下介绍各表的核心内容与示例代码。

##### 1. 文件表

文件表记录了原始文档的基础信息及对应的文本单元 id，是构建图谱的起点。运行如下代码。

[PRE16]

输出结果如图4.17所示。

##### 2. 文本单元表

文本单元表展示切分后的文本片段，每条记录通常对应一个语义独立的知识单元，包含文本内容、长度、所属文档及关联实体与关系。运行如下代码。

[PRE17]

| | id | human_readable_id | title | text | text_unit_ids |

|---|---|---|---|---|---|

| 0 | c546fa97b72c8b72b7efb0c1ac45cb1d | 1 | D3、C4.5决策树的建模流程.txt | Lesson 8.3 ID3、C4.5决策树的建模流程 \nID3和C4.5作为的经典决策树算... | [0653a697b64dd8c029503ffc22af9ec3, b1fce21a45f... |

图4.17 文件表查看结果

输出结果如图4.18所示。

| | id | human_readable_id | text | n_tokens | document_ids | entity_ids | relationship_ids |

|---|---|---|---|---|---|---|---|

| 0 | 0653a697b64dd8c029503ffc22af9ec3 | 1 | Lesson 8.3 ID3、C4.5决策树的建模流程\nID3和C4.5作为的经典决策树算... | 1200 | [c546fa97b72c8b72b7efb0c1ac45cb1d] | [22113170e6c742ba940314e15c401dc1, 23e8813975c... | [813887b9c3f54445832a00a8d1b47264, 16f36977566... |

| 1 | b1fce21a45f01c5ef14bafc9fe3bab1d | 2 | 8961919\n然后即可算出按照如此规则进行数据集划分，最终能够减少的不纯度数值：\n# ... | 1200 | [c546fa97b72c8b72b7efb0c1ac45cb1d] | None | None |

图4.18 文本单元表查看结果

##### 3. 实体嵌入表

实体嵌入表记录了从文本中抽取的实体及其描述、类型和向量嵌入，是构建图谱节点的基础。运行如下代码。

[PRE18]

输出结果如图4.19所示。

##### 4. 实体表

实体表呈现实体在图谱中的网络属性，如社区归属、节点度等，便于后续可视化与聚类分析。运行如下代码。

[PRE19]

输出结果如图4.20所示。

| | id | human_readable_id | title | type | description | text_unit_ids |

|---|---|---|---|---|---|---|

| 0 | 22113170e6c742ba940314e15c401dc1 | 0 | ID3 | EVENT | ID3 is a classic decision tree algorithm used ... | [0653a697b64dd8c029503ffc22af9ec3, 08530861b09 ...] |

| 1 | 23e8813975cc43d7921375da3c781c3c | 1 | C4.5 | EVENT | C4.5 is an improved version of the ID3 decisio... | [0653a697b64dd8c029503ffc22af9ec3, 08530861b09 ...] |

图4.19 实体嵌入表查看结果

| | id | human_readable_id | title | community | level | degree | x | y |

|---|---|---|---|---|---|---|---|---|

| 0 | 22113170e6c742ba940314e15c401dc1 | 0 | ID3 | 0 | 0 | 7 | 0 | 0 |

| 1 | 23e8813975cc43d7921375da3c781c3c | 1 | C4.5 | 0 | 0 | 4 | 0 | 0 |

图4.20 实体表查看结果

##### 5. 关系表

关系表定义了实体间的语义连接，如“ID3是C4.5的前身”，每条边记录来源、目标、关系描述及关联文本单元。运行如下代码。

[PRE20]

输出结果如图4.21所示。

##### 6. 社区表

通过社区检测算法得到语义社区，社区内部实体与关系构成高内聚的知识集群，可作为检索阶段的重要组织单元。运行如下代码。

[PRE21]

输出结果如图4.22所示。

| id | human_readable_id | community | level | title | entity_ids | relationship_ids | text_unit_ids | period | size |

|---|---|---|---|---|---|---|---|---|---|

| 17bfead0-7ccc-438a-922b-5c06deea39ea | 0 | 0 | 0 | Community 0 | [221113170e6c742ba940314e15c401dc1, 23e8813975c...] | [813887b9c3f54445832a00a8d1b47264, 16f36977566...] | [0653a697b64dd8c029503ffc22af9ec3, 08530861b090...] | 2024-11-28 | 11 |