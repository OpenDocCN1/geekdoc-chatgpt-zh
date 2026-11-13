

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

图11-11 Ibrutinib在DGIdb中的搜索结果

## 11.1.4 ChEMBL

### 1. ChEMBL 的介绍

ChEMBL数据库是欧洲生物信息研究所（European Bioinformatics Institute）于2009年开发的一个靶点与生物活性药物数据库，旨在捕获药物研究和开发过程中的药物化学数据和知识。ChEMBL 官网首页如图11-12所示。

![](img/df1b219b2c6176235f1ea4f385724826_255_0.png)

图11-12 ChEMBL官网首页

ChEMBL 数据库的内容是关于小分子及其生物活性的信息，它们主要从Journal of Medicinal Chemistry、ACS Medicinal Chemistry Letters、European Journal of Medicinal Chemistry、Journal of Natural Products等核心药物化学期刊的全文文章中提取，并结合已批准的药物和临床开发候选药物的数据，如作用机制和治疗适应证。生物活性数据也与其他数据库交换，如PubChem中的子数据库BioAssay和BindingDB，允许用户从更大的信息体中受益。产生的数据库具有广泛的实际应用，包括识别感兴趣目标的化学工具、评估化合物选择性、训练机器学习模型（例如，用于目标预测）、协助生成药物再利用假设、评估靶标可处理性和与其他药物发现资源的整合。

临床开发中的化合物主要来自美国采用名称（United States Adopted Name，USAN）申请和美国临床试验数据库（ClinicalTrials.gov）。已批准的药物主要从FDA橙皮书数据库（the FDA Orange Book database）和FDA新药批准年度清单（the annual list of FDA New Drug Approvals）中获取，信息也从英国国家处方（British National Formulary，BNF）和ATC分类中提取。临床试验药物的适应证从美国临床试验数据库获得，并通过手动和自动方法的组合映射到医学主题标题（Mesh）和实际因素本体论（Experimental Factor Ontology）中的疾病ID。已批准药物的适应证从每日医疗（DailyMed）的药物包装标签和ATC分类中获得。批准药物和临床试验药物的治疗靶标都是通过科学文献、药品包装标签和制药公司的pipeline信息等参考来源人工获取的。

在最新的版本ChEMBL 30.0中，该数据库共收集了14,855个靶点和 2,157,379个不同的化合物以及 84,092项专利。

### 2. ChEMBL 数据库的检索应用

在该数据库里搜索药物信息有4种方式，分别是药物名称（化合物）、绘制化合物结构、输入序列以及分子或者靶标ID。本例以药物伊布替尼（Ibrutinib）为例，按名称搜索，结果如图11-13所示。在该页面中，左边的Filters窗口是筛选器，列出了对这46个小分子的各种特性的统计，包括分子类型（Type）、最大临床试验期（Max Phase）、违反五条规则的次数（#RO5 violations）、分子量（Molecular Weight）等，并且允许用户通过单击相应的数值浏览给定范围内的原始数据子集。

![](img/df1b219b2c6176235f1ea4f385724826_256_0.png)

图11-13 Ibrutinib在ChEMBL中的搜索结果

可以看出，第一个显示的就是我们查找的药物伊布替尼，点进去之后可以查看详细信息，如图11-14所示。该药物的名称分类、来源、分子特征、药物适应证、药物机制以及临床信息等都可以查到。

![](img/df1b219b2c6176235f1ea4f385724826_256_1.png)

图11-14 Ibrutinib在ChEMBL中的详细信息

此外，ChEMBL针对药物-靶点的数据搜索最常用也最实用。

## 11.1.5 ETCM

### 1. ETCM 数据库的介绍

ETCM即The Encyclopedia of Traditional Chinese Medicine，于2018年上线。ETCM数据库官网首页如图11-15所示。ETCM包括常用草药和中药配方及其成分的全面和标准化信息。ETCM提供的草药基本属性和质量控制标准、配方组成、成分药物相似性以及许多其他信息可以作为方便用户获取有关草药或配方的详细信息的资源。为了促进中药的功能和机理研究，ETCM根据中药成分和已知药物之间的化学指纹相似性，提供中药成分、草药和配方的预测目标基因。ETCM还开发了系统分析功能，允许用户探索中药、配方、成分、基因靶点和相关路径或疾病之间的关系或建立网络。

![](img/df1b219b2c6176235f1ea4f385724826_257_0.png)

图11-15 ETCM数据库官网首页

目前，数据库收录了402种中草药、3959个药方、7284种有效成分，此外还收录了中草药有效成分可能靶向的基因靶标2266个，及其相对应的4323种疾病。

### 2. ETCM 数据库的应用

以中药艾叶为例，右侧是艾叶的信息目录，包括基础信息、鉴定信息、特性、中医属性、药理学、交叉参考等。

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

图11-16 ETCM数据库中艾叶的查询结果

## 11.2 蛋白质数据库

### 11.2.1 UniProt数据库

### 1. UniProt数据库的介绍

UniProt数据库，即UniProt Protein Resource，其官网首页如图11-17所示，是由欧洲生物信息研究所（European Bioinformatics Institute，EBI）、美国蛋白质信息资源（Protein Information Resource，PIR）以及瑞士生物信息研究所（Swiss Institute of Bioinformatics，SIB）等机构共同组成的UniProt协会（UniProt Consortium）编辑、制作的，提供了全面、高质量和免费访问的蛋白质序列和功能信息资源。其中包括蛋白质知识库UniProtKB、蛋白质序列归档库UniParc以及蛋白质序列参考UniRef三部分，目前是国际知名蛋白质数据库之一。

![](img/df1b219b2c6176235f1ea4f385724826_259_0.png)

图11-17 UniProt数据库官网首页

Swiss-Prot是经过注释的蛋白质序列数据库，由EBI维护。数据库由蛋白质序列条目构成，每个条目包含蛋白质序列、引用文献信息、分类学信息、注释等，注释中包括蛋白质的功能、转录后修饰、特殊位点和区域、二级结构、四级结构、与其他序列的相似性、序列残缺与疾病的关系、序列变异体和冲突等信息。Swiss-Prot尽可能减少了冗余序列，并与其他30多个数据库建立了交叉引用，其中包括核酸序列库、蛋白质序列库和蛋白质结构库等。利用序列提取系统（SRS）可以方便地检索Swiss-Prot和其他EBI的数据库。Swiss-Prot只接受直接测序获得的蛋白质序列，序列提交可以在其Web页面上完成。

蛋白质序列归档库UniParc是一个全面的非冗余数据库，包含世界上大多数公开可用的蛋白质序列。蛋白质可能存在于不同的源数据库中，也可能存在于同一数据库中的多个副本中。UniParc通过仅存储每个唯一序列一次并为其提供稳定且唯一的标识符（UPI）来避免这种冗余，从而可以从不同的源数据库中识别相同的蛋白质。UPI永远不会被删除、更改或重新分配。

蛋白质序列参考UniRef分为3个数据集，分别为UniRef100、UniRef90和UniRef50，其数据主要来自知识库UniProtKB，同时也包括归档库UniParc中部分条目。

### 2. UniProt数据库的应用

在UniProt数据库中不仅可以进行单个蛋白质的信息查询，也可以对批量的蛋白质进行信息查询。以CD47蛋白质为例，查询它的相关信息，结果如图11-18所示。左侧的Filter by对Swiss-Prot和TrEMBL进行筛选，在右侧表格中有黄色的就是Swiss-Prot数据库中有人工标注的，蓝色的则是TrEMBL数据库中未经标注的。Popular organisms中可以根据种属对结果进行分类，在本例中，有Human、Mouse、Bovine、Rat等分类。右侧的表格中显示的则是蛋白质的标号、蛋白质和基因名称、种属以及长度。在表格的最上方有BLAST、Align工具，BLAST可以对感兴趣的蛋白质序列进行BLAST分析；Align可以对多个序列进行相似性比对，当勾选其中两项及以上时，即可对这些序列进行比对，比较不同组序列的同源性，当两组序列同源性较高时，这两组序列的功能可能相似；同时，也可以下载FASTA、Excel、XML等格式的蛋白质序列信息。

![](img/df1b219b2c6176235f1ea4f385724826_260_0.png)

图11-18 UniProt中蛋白质搜索结果页面

我们选择“Human”分组中的第一个，标号为“Q08722”，结果如图11-19所示，页面的左边是整个网页的目录，里面包含了这个蛋白质所有的信息，如功能、细胞定位、PTM、相互作用、高级结构、序列以及其他数据库关于这个蛋白的链接。页面右边详细介绍了CD47蛋白质，它的功能：通过充当血小板上THBS1的黏附受体，在细胞黏附中起作用，并在整合素的调节中起作用；在海马体的记忆形成和突触可塑性中起重要作用（通过相似性）等。

![](img/df1b219b2c6176235f1ea4f385724826_260_1.png)

图11-19 CD47在UniProt中的详细信息

### 11.2.2 PDB

### 1. PDB的介绍

蛋白质数据库于1971年由美国Brookhaven国家实验室建立，当时只有7个X射线晶体结构，是第一个开放获取的数字生物数据资源，如今也是国际上唯一存储生物大分子三维结构资料的数据库，这些生物大分子除了蛋白质还包括核酸以及核酸和蛋白质的复合物。PDB收集的数据来源于X射线晶体学和核磁共振（NMR）的数据，并由世界蛋白质数据库（worldwide Protein Data Bank，wwPDB）监管、审核与注释。目前，PDB每周更新一次，PDB的维护由结构生物信息学研究合作组织（Research Collaboratory for Structural Bioinformatics，RCSB）负责。

目前，除了三维原子坐标，每个PDB数据都包含描述分子模型和实验细节的实验数据和元数据。元数据包括蛋白质名称、序列、原生物体、小分子信息（如化学名称、结构和配方）、数据收集信息（如仪器和数据处理）和结构确定信息（如模型构建、细化和验证方法及统计）。此外，wwPDB还提供了增值注释，如二级结构、四级结构描述和有关配体结合位点的信息。PDB的数据和资源用于基础研究和应用研究，包括科学和教育、实验和计算方法开发、药物发现等。

### 2. PDB的使用

PDB允许用户用各种方式以及布尔逻辑组合进行检索，可检索的字段包括功能类别、PDB代码、名称、作者、空间群、分辨率、来源、入库时间、分子式、参考文献、生物来源等项，数据库官网首页如图11-20所示。

![](img/df1b219b2c6176235f1ea4f385724826_263_0.png)

图11-20 PDB官网首页

### 11.2.3 NCBI数据库

### 1. NCBI数据库的介绍

NCBI的全称是National Center for Biotechnology Information，即国家生物技术信息中心，隶属美国国家卫生研究所的美国国家医学图书馆（United States National Library of Medicine，NLM）的分部，成立于1988年，旨在开发分子生物学信息系统。该数据库为生物信息和数据提供了大量的在线资源，主要包含基因、DNA、RNA、蛋白质序列、文献索引以及化合物数据库等，官网首页如图11-21所示。

NCBI提供给用户可使用的功能主要有存放数据、下载数据、学习教程、提供应用程序接口（Application Programming Interface，API），便于程序开发、提供各种生物物数据分析工具和分子生物学等基本问题的研究。NCBI拥有包括PubMed、PubMed Central和GenBank在内的大约40个在线文献和分子生物学数据库。PubMed生物实验数据库是小分子和siRNA生物活性实验试剂的公开数据库，其主要目的是提供快捷免费地获取数据的途径，并提供直观的数据分析工具，包括检索文献、生物活性数据、生物试验记录以及分子靶标等。GenBank数据库是NCBI在1992年建立的，主要内容包括基因、人类在线孟德尔遗传、分子模型数据库、单核苷酸多态性数据库、人类基因组等，并与美国国家癌症研究所（National Cancer Institute，NCI）的肿瘤基因解剖计划合作。

![](img/df1b219b2c6176235f1ea4f385724826_264_0.png)

图11-21 NCBI数据库官网首页

除了建有GenBank核酸序列数据库（该数据库的数据资源来自全球几大DNA数据库，其中包括日本DNA数据库DDBJ、欧洲分子生物学实验室数据库EMBL以及其他几个知名科研机构），NCBI还提供众多功能强大的数据检索与分析工具。目前，NCBI提供1000 Genomes Browser、Batch Entrez、BLAST Link（Blink）、CDTree、Genome BLAST、PubChem Structure Search、Sequence
