- **max_data_tokens**：用于限定模型可处理的上下文总量。取值建议参照目标模型的最大 token 上限，例如模型为 8K 时可设为 5000 左右留出缓冲。该数值直接决定全局检索时能塞进上下文窗口的数据 token 上限。
- **map_llm_params** 与 **reduce_llm_params**：分别向映射阶段和归约阶段的 LLM 传入配置，前文已拆解过这两组参数。它们会左右模型在不同阶段的生成行为，比如 max_tokens、temperature 等细节。
- **allow_general_knowledge**：开关是否让模型在回答中混入通用外部知识。打开后模型会尝试把常识补进查询结果，适合需要宽泛背景的任务，但也容易引入幻觉，所以默认关闭为 False。
- **json_mode**：控制返回样式。设为 True 时输出结构化 JSON，设为 False 时给出自然的自由文本。若下游要做结构化处理，通常选用 JSON。
- **context_builder_params**：传给上下文构建器的额外配置，用于细化查询上下文的组装方式，比如是否启用社区摘要、是否加入社区排序等，具体细节前文代码分析中已说明。
- **concurrent_coroutines**：并发协程上限。全局搜索引擎支持并行处理多路查询，提升并发量可同时承接更多请求，例如设为 32 表示最多 32 个查询并行运行。
- **response_type**：描述模型应生成的响应形态与格式，采用自由文本说明。这里的 “multiple paragraphs” 即要求输出多段落、适合长篇解释或报告的文本。

（5）全局检索问答实操
运行下面示例代码，系统会自动从社区报告中抽取与“ID3”相关的片段，结合图谱实体做结构化抽取与重写，最终产出自然语言回答。

[PRE0]

输出结果如下。

**ID3 算法核心介绍**
ID3 属于经典的决策树方法，常用于分类场景。其关键在于对连续变量如年龄、收入进行离散化处理，以降低数据集的不纯度，这一操作是决策树建模的重要环节 [Data: Reports (1)]。该策略有助于提升模型精度与效率。

**与 CART 等算法的对比**
ID3 经常拿来与 CART 树做对照。两者目标都是减少不纯度，但在分割策略上存在差异。对比有助于在不同数据科学任务中权衡各自优劣 [Data: Reports (1)]。

**数值计算依赖**
在 ID3 流程里，Numpy 是核心的科学计算库，负责信息熵的计算。由此可见 Numpy 对支撑决策树所需的复杂数值运算的重要性 [Data: Reports (1)]。

**Sklearn 兼容性说明**
ID3 无法直接基于 Python 主流机器学习库 Sklearn 实现。这种不兼容意味着依赖 Sklearn 的用户需要寻找替代实现或做适配调整 [Data: Reports (1)]。

**模块化设计思路**
ML_basic_function 模块很可能收纳了 ID3 建模过程中常用的基础机器学习函数。其存在体现了模块化思路，便于代码复用与灵活改造 [Data: Reports (1)]。

（6）查看本次检索引用的社区报告
可执行以下代码查看问答过程中被引用的社区摘要。

[PRE1]

返回结果包含社区 id、标题、排名、内容摘要、出现权重等字段。

运行下面代码可查看模型生成时使用的完整上下文。

[PRE2]

输出结果如下。

[PRE3]

此外还能统计本次问答中大模型的调用次数与 token 消耗。

[PRE4]

输出结果如下。

[PRE5]

#### 4.3.5 GraphRAG 知识图谱可视化
可视化图谱结构有助于理解 GraphRAG 的运行机制、关系网络分布以及实体社区归属。本节介绍借助 yfiles-jupyter-graphs 在 Jupyter Notebook 中对 GraphRAG 知识图谱进行交互式展示的方法。

##### 1. yfiles-jupyter-graphs 简介与部署
yfiles-jupyter-graphs 是专为 Jupyter Notebook 设计的交互式图形可视化 Python 库，用于图形数据的展示、交互与分析。它基于功能强大的 yFiles 绘图引擎，可生成、布局并呈现复杂网络结构，方便在 Notebook 中创建和分析网络图、知识图谱、社交网络等。

执行如下命令安装该库。

[PRE6]

安装完成后，在 Jupyter 环境中导入库。

[PRE7]

##### 2. 实体关系转 yfiles 可读格式
使用 GraphWidget 渲染前，需把 GraphRAG 输出的实体 Entity 与关系 Relationship 转成标准的节点-边结构。先定义节点转换函数。

[PRE8]

该函数把实体 DataFrame 转为 yfiles-jupyter-graphs 可用的字典格式，每个实体被映射为包含属性的字典。主要流程如下。

1. `df.iterrows()`：逐行遍历实体 DataFrame，每行代表一个实体记录。
2. `node_id = row["title"]`：以 title 列作为节点唯一标识。
3. `nodes_dict[node_id]`：用 title 作为键，将整行属性通过 `row.to_dict()` 存入字典。
4. `return list(nodes_dict.values())`：返回所有节点字典组成的列表。

再定义边转换函数。

[PRE9]

该函数把关系 DataFrame 转为 yfiles 格式的关系字典列表。

1. `df.iterrows()`：逐行遍历关系 DataFrame，每行代表一条关系。
2. `relationships.append(...)`：对每行构造包含起始实体、结束实体及属性的字典。字段包括 **start** 取自 source、**end** 取自 target、**properties** 为整行转成的字典。
3. `return relationships`：返回所有关系字典的列表。

运行下面代码查看本地的实体-关系数据。

[PRE10]

输出内容如图4.27所示。

| | id | human_readable_id | source | target | description | weight | combined_degree | text_unit_ids |
|---|---|---|---|---|---|---|---|---|
| 0 | 813887b9c3f54445832a00a8d1b47264 | 0 | ID3 | C4.5 | C4.5 is an improved version of the ID3 algorit... | 16.0 | 11 | [0653a697b64dd8c029503ffc22af9ec3, 08530861b09...] |
| 1 | 16f369775666497b8ece38456683313e | 1 | ID3 | CART TREE | ID3 and CART Tree both aim to reduce dataset i... | 7.0 | 8 | [0653a697b64dd8c029503ffc22af9ec3] |

图4.27 实体-关系数据（部分）

对首条数据使用上述函数转换，代码如下。

[PRE11]

##### 3. 构建 GraphWidget 组件
执行如下代码创建可视化组件。

[PRE12]

可按实体所属社区为节点着色，并依据节点重要程度调节大小，实现更直观的图谱展示。