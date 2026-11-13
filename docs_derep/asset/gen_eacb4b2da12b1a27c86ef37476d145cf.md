### 1. DrugBank：药物研发的“全能百科”

作为全球最权威的药物信息中枢之一，DrugBank 自 2006 年在阿尔伯塔大学 Wishart 实验室诞生以来，已从面向学术界的结构化数据项目，进化为临床医生、药理学家、药物化学家乃至制药企业人手必备的“瑞士军刀”。它覆盖了 FDA 上市药物与临床试验药物的全生命周期数据，将药物本体、靶标蛋白、作用机制、相互作用等海量信息标准化整合，实现了临床医学视角与化学信息学视角的双轨并行。

**核心数据规模（v5.1.9 版）：** 收录 14,623 个药物条目（含 2,724 个小分子上市药、1,517 个生物制剂、132 个营养保健品、6,677+ 实验性药物），关联 5,269 个非冗余蛋白靶标。单条目超 200 个数据字段，化学数据与靶标数据各占半壁江山。特别增设的 COVID-19 专题库，更是为抗疫研发按下了“加速键”。此外，其整合的 HMDB、T3DB、SMPDB、FooDB 四大子库，构建了代谢组学与化学信息学的完整生态。

**实战检索指南（以伊布替尼 Ibrutinib 为例）：**
首页支持按药物、靶标、通路、适应症四大维度直检。进入详情页，左侧导航栏模块化清晰（药理、相互作用、上市产品、分类、临床试验、理化性质、靶点等），右侧正文区即时呈现背景介绍、适应症/禁忌症、化学结构、靶点网络等核心情报。进阶玩法可点击 `Browse` 或 `Search`，解锁化学结构相似性、分子量范围、药物-食物相互作用、靶标序列比对等高维检索模式。

[PRE0]

图11-6 DrugBank Online主页

[PRE1]

图11-7 药物Ibrutinib在DrugBank中的详细页面

---

### 11.1.3 DGIdb：精准医疗下的“药-基”匹配引擎

2013 年上线、现已迭代至 v4.0 的 DGIdb（Drug-Gene Interaction Database），专注解决一个核心痛点：**给定一组基因（尤其是癌基因），能用什么药？** 它聚合了文献挖掘、专家人工审读、公共数据库导入等多源数据，将“可药用基因组”划分为两大梯队：
1.  **已知交互型**：基因-药物关系已落地文献/数据库，适合“以基因找药”验证假设；
2.  **潜在可药型**：基因属激酶等“可成药家族”成员，虽暂无对应药物，但具结构特征可被靶向，为新靶点挖掘提供线索。

**v4.0 数据看板：** 40,000+ 基因 × 10,000+ 药物 = 100,000+ 交互对，覆盖 42 个潜在可药基因家族。后端基于 Ruby on Rails + PostgreSQL，灵活 schema 兼容异构元数据，支持 API 与 TSV 批量下载。

**双模检索实操（仍以伊布替尼为例）：**
- **Search Drug-Gene Interactions**：输入药物/基因名，右侧多维过滤器极其硬核——临床可行性、药物基因组学分类、耐药机制、数据来源、基因功能分类（如 DNA 修复）、交互类型（抑制剂、诱导剂等）。结果页三板斧：`Summary`（药物档案）、`Interactions`（逐基因打分、机制、直接/间接作用、PMID 溯源）、`Claims`（临床声明）。
- **Search Potential Druggability**：纯输入基因列表，输出其所属可药家族分类，附带示例基因列表，便于快速评估“新靶点”价值。

[PRE2]

图11-8 DGIdb官网首页

[PRE3]

图11-9 Search Drug-Gene Interactions模块搜索页面

[PRE4]

图11-10 Search Potential Druggability模块搜索页面

**伊布替尼 DGIdb 检索结果速览：**

## IBRUTINIB Drug Record

Summary Interactions Claims

| Drug Info: | |

| --- | --- |

| FDA Approval | not approved |

| Drug Class | small molecule |

| Drug Indications | antineoplastic agent |

| FDA Approval | Mantle cell lymphoma... |

| Drug Class | Kinase Inhibitors |

- PC127895
- PC1-27895
- CHEMBL32763
- PC1-2795408
- MBSR1/VICA
- IBRUTINIB
- PC-327895
- MBSR1/VICA

[PRE5] *注：素材中此处标记为 PRE5，对应原图 11-11，此处保留原标记*

图11-11 Ibrutinib在DGIdb中的搜索结果

---

#### 11.1.4 ChEMBL：药物化学家的“结构-活性”金矿

EBI 于 2009 年推出的 ChEMBL，直指药物化学核心——**小分子结构与生物活性的定量关系（SAR）**。数据源极其“硬核”：《J. Med. Chem.》《ACS Med. Chem. Lett.》等顶刊全文深度抽提，叠加 FDA 橙皮书、USAN、ClinicalTrials.gov、BNF、ATC 等权威注册库，并与 PubChem BioAssay、BindingDB 双向同步。v30.0 版收录 14,855 靶点、215 万+ 独特化合物、8.4 万+ 专利，是靶点预测模型训练、选择性评估、药物再创制、靶点可成药性评价的标准训练集。

**四维检索入口：** 药物名称/化合物 ID、手绘/粘贴化学结构、蛋白/核酸序列比对、靶标 ID。

**伊布替尼检索实录：** 名称搜索命中 46 条小分子记录，左侧 `Filters` 面板堪称“数据剖析仪”——分子类型、最高临床阶段、RO5 违规次数、分子量分布……点击任意统计值即可下钻子集。首条即为目标药物，点入详情页可获：命名体系、数据来源、理化指纹、适应症/机制、临床进展全景图。ChEMBL 在药物-靶点矩阵检索上的深度优化，更是结构生物学家与计算化学家的高频刚需。

[PRE5] *注：素材中此处标记为 PRE5，对应原图 11-12，此处保留原标记*

图11-12 ChEMBL官网首页

[PRE6]

图11-13 Ibrutinib在ChEMBL中的搜索结果

[PRE7]

图11-14 Ibrutinib在ChEMBL中的详细信息

---

### 11.1.5 ETCM：中药现代化的“数字化标本馆”

2018 年上线的 ETCM（中医药百科全书），致力于用现代信息学语言重写中药本草。它不止于收录 402 味常用药材、3,959 个经典名方、7,284 个有效成分，更通过**化学指纹相似性匹配**已知药物靶点，为中药成分“预测”潜在靶标基因（2,266 个）及关联疾病（4,323 种），并内置系统药理学网络分析工具，支持“成分-靶点-通路-疾病”多层级网络构建。

**艾叶查询实录：** 界面呈现标准化药材档案——中/英/拉丁三名对照、来源植物、产地分布、采收加工、性状鉴别、规格等级、成分谱（如 α-蒎烯、7-羟基香豆素、甲基桂木酚等），并提供药理作用、交叉引用链接，实现从“本草纲目”到“分子网络”的无缝跳转。

[PRE8]

图11-15 ETCM数据库官网首页

### AI YE

| Field | Value |

|---|---|

| Herb Name in Chinese | 艾叶 |

| Herb Name in Pinyin | AI YE |

| Herb Name in Latin | Artemisiae argyi Leaf et Vent |

| Type | Plant medicine |

| Description in Chinese | 菊科 |

| Description in English | Composite |

| Habitat in Chinese | 我国华北及江南至山东、陕西、甘肃等地有栽培 |

| Habitat in English | SHAN XU SUI HU BEI JIANG NAN HU ZHONG SHAN DONG HE SHAN XI GAN SU DENG DI |

| Collection Time | 春、夏、二季，收叶时，外叶剪去枝梗，晒干或阴干 |

| Appearance | 叶片展平，皱缩，呈羽状，长椭圆形叶片下方为灰绿色绒毛，以叶绿色，质柔软较厚叶片的，出香气者为优等品。上表面灰绿或深绿色带黄，背面灰色绒毛厚而密。下表面灰绿或灰白色，药性温、气香苦，味苦 |

| Specifications | |

| Components | (IR)-Alpha-Pinene, 7-Hydroxycoumarin, Estragole, Unidentified ... |

[PRE9] *注：素材中此处标记为 PRE9，对应原图 11-16，此处保留原标记*

图11-16 ETCM数据库中艾叶的查询结果

---

### 11.2 蛋白质数据库

### 11.2.1 UniProt：蛋白质世界的“身份证中心”

由 EBI、PIR、SIB 组成的 UniProt 联盟，构建了全球最核心的蛋白质知识基础设施，官网集成三大核心模块：
- **UniProtKB（知识库）**：双层架构。`Swiss-Prot`（人工深度注释，含功能、PTM、结构、变异致病、30+ 库交叉引用，仅收直接测序蛋白）；`TrEMBL`（自动注释，海量补充）。
- **UniParc（归档库）**：全公开蛋白序列非冗余存储，唯一稳定标识符 UPI 永不复用，解决跨库同一序列多 ID 混淆。
- **UniRef（参考聚类）**：按 100%/90%/50% 身份聚类，加速大规模序列比对。

**CD47 实战演示：** 搜索即得物种分组表，黄底=Swiss-Prot 人工金标准，蓝底=TrEMBL 自动注释。勾选 `Human` 首条 `Q08722`，详情页左侧全景目录（功能、亚细胞定位、PTM、相互作用、结构、序列、跨库链接），右侧核心功能速读：THBS1 粘附受体调节整合素、海马记忆与突触可塑性参与者。顶部 `BLAST`/`Align`/下载（FASTA/Excel/XML）工具链一应俱全，支持多序列同源性对比推断功能保守性。

[PRE9] *注：素材中此处标记为 PRE9，对应原图 11-17，此处保留原标记*

图11-17 UniProt数据库官网首页

[PRE10]

图11-18 UniProt中蛋白质搜索结果页面

[PRE11]

图11-19 UniProt中CD47蛋白详细信息页面