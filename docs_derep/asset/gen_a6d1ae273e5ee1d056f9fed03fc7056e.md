# TTD：治疗靶点数据库深度解析

TTD（Therapeutic Target Database，治疗靶点数据库）官网界面如图11-29所示。该资源库专注于汇总已知及探索性治疗靶点（蛋白质/核酸）、关联疾病、通路详情及对应药物信息。TTD以“靶点”为核心枢纽，辐射药物数据、调控分子、生物通路等多维内容。靶点按开发成熟度划分为四级：成功靶点（已有获批药物）、临床试验靶点（仅有临床药物）、专利记录靶点（见于专利文献）、文献报告靶点。

以2020版本为例，数据库收录药物超3.8万种，涵盖2797个获批药、10831个临床试验药及20123个实验阶段药物；靶点总数3578个，其中成功靶点498个、临床试验靶点1342个、专利记录靶点185个。

## Therapeutic Target Database

- Home
- Advanced Search
- Target Group
- Drug Group
- Patient Data
- Model & Study
- Download

Search Whole Database

### Search for Targets:

Examples: EGFR; Vascular endothelial growth factor; Peramivir; Renal cell carcinoma; EGFR_HUMAN ...

### Therapeutic Target & Drug Data for Coronavirus (COVID-19, MERS-CoV, SARS-CoV)

A comprehensive collection of anti-coronavirus drugs (small molecular drugs, monoclonal antibodies, protein-peptide drugs, combination drugs, vaccines, etc.) together with their corresponding therapeutic targets data from previous and recent coronavirus researches [Click to Expand All Drugs]

[PRE0]

Jump to the Star Target & Star Drug for COVID-19 of This Week

### Search for Drugs:

Examples: Cabocantinib; Ebola virus infection; Oseltamivir ...

### Search Drugs and Targets by Disease or ICD Identifier:

Examples: Influenza, Alzheimer; ICD8: 331; ICD10: G30 ...

### Search for Biomarkers:

Examples: 5-HT 2A receptor; Rheumatic fever; Candidiasis; Dengue; HIV infection ...

图11-29 TTD数据库官网首页

### 2. TTD的应用场景

用户可通过靶点、药物、疾病、生物标志物四大维度检索，亦可借助药物相似性工具反推无靶点信息化合物的潜在靶点。高级搜索模块专设COVID-19药物与疗效靶点专区。

官网首页提供检索示范（如图11-29）。以EGFR（表皮生长因子受体，HER家族成员）为例，检索结果见图11-30。结果表格呈现靶点名、类型、适应症及对应药物，并支持深入查看药物详情。

## Therapeutic Target Database

Home Advanced Search Target Group Drug Group Patient Data Model & Study Download

## Search for targets

You are searching for: EGFR

| ID | Target Name | Target type | Disease | Drugs |
|---|---|---|---|---|
| T59328 | Epidermal growth factor receptor (EGFR) | Successful Target | Angina pectoris, HER2/NEU overexpressing breast cancer; | Osimertinib |
| T48069 | Insulin-like growth factor 1 receptor (IGF1R) | Successful Target | Growth failure; Hormone deficiency; | Treprotumumab |
| T86250 | ErbB3 tyrosine kinase receptor (ErbB-3) | Clinical Trial Target | Colorectal cancer, Head and neck cancer; | CDK-3379 |
| T41560 | EGFR messenger RNA (EGFR mRNA) | Clinical Trial Target | Head and neck cancer; Non-small-cell lung cancer; | ACD010 |
| T64969 | Epidermal growth factor receptor variant III (EGFR vIII) | Clinical Trial Target | Brain cancer; Malignant glioma; | |

图11-30 靶点EGFR在TTD中的搜索结果

---

#### 11.3.2 BindingDB

### 1. BindingDB概览

BindingDB于2000年上线，是首个面向公众开放的结合亲和力测量数据库。其核心聚焦于药物靶点（或候选靶点）蛋白与小分子的相互作用强度。数据来源广泛，整合了PDB相关文献、专利文档、PubChem BioAssays子库及ChEMBL数据。亲和力测定手段涵盖酶抑制/动力学、等温滴定量热法（ITC）、核磁共振、放射性配体竞争实验等，输出指标包括$K_i$、IC$_{50}$、$K_d$、EC$_{50}$等。

BindingDB坚持周更机制。检索入口支持靶点名/序列、药物名/结构、通路等多维度，并提供下载服务与API接口，便于程序化调用。

截至2022年3月19日，库内收录41,296个条目（均含DOI），囊括8,689个蛋白靶点与1,047,667个小分子间的243万余条结合数据。针对100%序列一致的蛋白，有5,988个具备亲和力实测值；放宽至85%序列一致性时，蛋白-配体晶体结构达11,442个。

### 2. BindingDB实战指南

官网首页见图11-31。点击左侧“Target”栏下的“Name”进入检索页，仍以EGFR为例，搜索列表见图11-32，第4条为目标靶点。点击进入详情页（图11-33），可获靶点名称、小分子BDBM编号、化学结构、英文名、SMILES等核心字段，以及关联文献引用。

### The Binding Database

Please try our new beta-test BindingDB site! • Responsive design for better display on tablets and phones • New tool to filter search results by e.g. target, article, affinity, x-tal structure • Simplified new look. Access coronavirus binding data on our covid page here.

BindingDB is a public, web-accessible database of measured binding affinities, focusing chiefly on the interactions of protein considered to be drug-targets with small, drug-like molecules. As of March 19, 2022, BindingDB contains 41,296 Entities, each with a DOI, containing 2,437,529 binding data for 8,689 protein targets and 1,047,667 small molecules.

There are 5,988 protein-ligand crystal structures with BindingDB affinity measurements for proteins with 100% sequence identity, and 11,442 crystal structures allowing proteins to 85% sequence identity.

You can also use BindingDB data through the Registry of Open Data on AWS. https://registry.opendata.aws/binding-db.

- myBDB logged

- o Search and Browse

- Target
- Sequence
- Name &
- Ki IC50 Kd EC50
- Rate constants
- ΔG° ΔH° -TΔS°
- pH (Enzymatic Assay)
- pH (ITC)
- Substrate or Competitor
- Compound Mol. Wt.
- Chemical Structure
- Pathways
- Source Organism
- Number of Compounds
- Monomer List in cov
- Het List in SDF
- Compound
- FDA Drugs
- Important Compounds
- Chemical Structure
- Name
- SMILES
- Number of Data / Targets
- Special tools
- 3D Structure Series
- Find My Compound's Targets
- Find Compounds for My Targets
- Do Virtual Screening
- SCOP
- Citation
- Author
- Journal/Citation
- Institution
- PubMed
- PubChem BioAssay
- US Patent
- Special Data Sets
- C-circularism Data
- Simple Search
- Article Titles, Authors, Assays, Compound Names, Target Names
- Advanced Search
- Combine multiple search criteria, such as chemical structures, target names, and numerical affinities; restrict searches by data source, such as BindingDB, ChEMBL, PubChem, and Patents.
- Recently added Targets
- Serine protease HTRA2, mitochondrial. Human. O43464 (Curated 03/13/22). HTRA2: 6 subμM ligands
- Genome polyprotein. HRV-1B, P12916 (Curated 03/13/22). 3 subμM ligands
- Genome polyprotein. DENV-1, P17763 (Curated 03/13/22). 5 subμM ligands
- NEDD8-conjugating enzyme Ubc12. Human. P61081 (Curated 08/17/20). UBE2M: 100 subμM ligands
- N-acetylglucosamine-1-phosphotransferase subunits alpha/beta. Human. Q3T906 (Curated 03/04/22). GNPTAB: 126 subμM ligands
- Glucokinase 1, putative. Trypanosoma cruzi (strain CL Brener). Q4E4E1 (Curated 02/02/22). 8 subμM ligands

BindingDB has accelerated collection of COVID related data. You can find the result here: We are excited to share our new browser extension, BDBfind. Once installed in your browser, BDBfind automatically lets you know when BindingDB has the data from an article. PubMed Entry, or US Patent you are looking at online and provides direct links to view or download the data. Get BDBfind by searching for it in the Chrome webstore or in Firefox extensions or by following these links: https://chrome.google.com/webstore/search/bdbfind (Once installed, click on the "Jigsaw puzzle" icon to keep the BDB icon displayed.) https://addons.mozilla.org/addon/bdbfind/ Watch BDBfind in action in this 1.5 minute video.

图11-31 BindingDB官网首页

### The Binding Database

| Target Name | Commercially Available Compounds | Ki | IC50 | Kd | EC50 | Kd/Ki | other ITC | Other Articles | UniprotKB Swiss-Prot/TrEMBL | SDF | TSV | Add to myBDB |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| dMNEGFR | <0 0 | 0 | 3 | 0 | 0 | 0 | 0 | 0 | 1 | 3 | 20 30 TSV | ☐ |
| DNA-PK-EGFR | <0 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 1 | 2 | 20 30 TSV | ☐ |
| DNA-PK-VEGFR2 | <0 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 1 | 2 | 20 30 TSV | ☐ |
| EGFR | 432 | 237 | 14033 | 1515 | 226 | 0 | 3 | 0 | 0 | 862 | 16 | 20 30 TSV | ☐ |
| EGFR (0346-750-T73M | 0 | 0 | 2 | 0 | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 20 30 TSV | ☐ |
| EGFR (the 164-760 | 0 | 0 | 13 | 3 | 0 | 0 | 0 | 0 | 0 | 3 | 0 | 20 30 TSV | ☐ |
| EGFR (G719S | 9 | 9 | 2 | 1 | 0 | 0 | 0 | 0 | 0 | 2 | 0 | 20 30 TSV | ☐ |
| EGFR (L858R, T790M, C797S | 9 | 0 | 118 | 0 | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 20 30 TSV | ☐ |
| EGFR (L747T | 9 | 0 | 2 | 1 | 0 | 0 | 0 | 0 | 0 | 2 | 0 | 20 30 TSV | ☐ |
| EGFR Data 9574(675) | 9 | 0 | 26 | 0 | 0 | 0 | 3 | 0 | 0 | 1 | 0 | 20 30 TSV | ☐ |
| EGFR Data 9574(675) T790M | 9 | 0 | 26 | 0 | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 20 30 TSV | ☐ |