#### 3.3.2.2 Cooke网络模型

J. E. Hunt 与 D. E. Cooke [20] 创建了一种人工免疫网络系统，并将其引入机器学习领域。该系统融合了 B 细胞动态、刺激度计算、免疫网络理论、基因库机制、变异策略以及适配分类任务的抗体编码串等核心免疫原理。

系统架构呈现为一个 B 细胞构成的网络拓扑。当任意两个 B 细胞间的亲和力跨越预设阈值，便会建立起双向连接。每个 B 细胞个体包含四大核心组件：匹配规则、基因库（负责生成抗体，即 DNA 序列）、多条中间 DNA 序列，以及该细胞专属的刺激度数值。系统中的抗原对应着待学习 DNA 串的表达形式。初始化阶段，系统随机抽取部分训练样本构建 B 细胞网络，剩余数据则组成抗体训练集。

算法执行流程如下：

- (1) 获取抗原群体，随机初始化 B 细胞群体，织就初始网络。
- (2) 从抗原池中随机抽取一个抗原，并将其随机植入网络的某个节点位置。
- (3) 按比例筛选一批 B 细胞作为“接触细胞”，逐一判定其能否与抗原结合，并量化相应的刺激度。
- (4) 若某 B 细胞的刺激度突破阈值，触发克隆机制：
  - ① 若无细胞能与抗原结合，则新生成一个 B 细胞以建立连接；
  - ② 按刺激度对细胞排序，淘汰最末端 5% 的弱势细胞；
  - ③ 重新生成数量为群体 25% 的新 B 细胞（记为 N 个），再从中甄选出占群体 5% 的 M 个细胞接入网络。

依循上述循环，持续向网络投喂训练数据。一旦满足终止判据，即冻结训练并保存最终网络结构。B 细胞间的连接准则如前所述。变异操作手段丰富，涵盖模拟基因选择、折叠重组、嵌入插入、随机点突变等。新生成的 B 细胞若能接入网络则保留，否则剔除。算法中模拟抗体受激刺激度的计算公式如下：

[PRE0]

公式中，首项量化抗原与 B 细胞（抗体）的匹配程度；次项体现抗体间的相互抑制；第三项反映抗体间的协同刺激；末项为抗体的自然衰减率。

该模型借鉴了 B 细胞、免疫网络、克隆扩增与变异等机制，旨在构建多元化的候选解空间。其核心逻辑在于：维持一个承载免疫记忆的 B 细胞网络，在训练数据及邻居节点的双重刺激下，施行克隆与变异操作。该自组织网络不仅能表征学习数据的内在结构，更具备对非训练样本进行分类泛化的能力。

#### 3.3.2.3 资源受限人工免疫系统（RLAIS）

J. Timmis 提出的资源受限人工免疫系统 [21-25] 专注于数据分析场景。模型引入“人工识别球”（ARB）作为 B 细胞的代理。ARB 依据刺激水平动态管理 B 细胞：当某 ARB 失去维持 B 细胞的必要性时，该 ARB 将被移除，从而实现种群规模的高效控制。通过聚合相似的 ARB，RLAIS 能从数据中自动挖掘模式并完成聚类分析。其免疫建模要素包括：

- (1) **抗原与初始网络构建**：将待分析数据二分，一部分作为抗原驱动网络训练，另一部分化身为 ARB 编织初始网络。
- (2) **ARB 三元结构**：由匹配规则、数据项、刺激度组成。单个 ARB 代表一簇同类 B 细胞，网络总细胞数预设固定值。ARB 映射免疫形态空间，数据项是抗体的数据投影，刺激度则度量 ARB 与抗原、ARB 间的亲和关系。
- (3) **网络拓扑生成规则**：预设亲和度阈值 NAT。两个 ARB 亲和力低于 NAT 时建立连接，反之则隔离。
- (4) **刺激度度量**：采用欧氏距离，综合考量 ARB-抗原及 ARB-ARB 间的交互刺激。
- (5) **收敛判据**：网络拓扑趋于稳定结构时，终止训练。

#### 3.3.2.4 aiNET 免疫网络

D. E. Castro 等人设计的 aiNET 网络 [26] 旨在解决无标签数据集的聚类与噪声过滤难题。

- (1) **模型极简化**：模糊 B 细胞与抗体的界限，网络仅保留抗体与抗原两类实体。
- (2) **加权图拓扑**：网络非全连通，节点为抗体，边集赋予权重表征连接强度。
- (3) **竞争性识别机制**：抗原输入触发节点竞争，仅识别成功的抗体能激活网络。
- (4) **动态种群调控**：胜出抗体经历克隆扩增与选择，败者清退。双阈值策略控制规模：以抗体-抗体亲和力为准，设抑制阈值清除冗余低亲和力细胞；以抗原-抗体亲和力为准，设阈值决定克隆应答概率。
- (5) **数据结构表征力**：网络天然具备描述数据拓扑特征的能力。
- (6) **克隆选择与亲和力成熟理论基石**：种群规模动态自适应，支持多目标优化搜索，并能长久保存优质解。

#### 3.3.2.5 其他免疫网络挖掘变体

**E. Hart 与 P. Ross [27]** 洞察免疫记忆与稀疏分布式存储器（SDM）在联想记忆上的同构性，提出自组织稀疏分布存储模型（SOSDM）。其技术路线包含：融合人工免疫与松散分布记忆模型，视免疫记忆为自组织机制；引入协同进化遗传算法驱动系统演化；网络语义映射为“抗原=数据片段”、“抗体=聚类描述”、“同类抗体簇=刺激球定义聚类规模”；初始化模拟自然分布式特性，随机布局分布式数据单元以映射数据空间本质。

**T. Knight 与 J. Timmis [28]** 构建多层免疫网络挖掘框架，将系统分层为自由抗体层、B 细胞层、记忆细胞层，层间交织成网。语义对应为：抗原=待分析数据，自由抗体=初始模式，B 细胞=中间模式，记忆细胞=最终模式。

**T. Sawomir 等 [29]** 实现的网络直接以 n 维实值向量编码抗体/抗原/数据项，亲和度阈值取平均距离，达成数据聚类功能。

**Urszula Kuzelewska [30]** 的自组织免疫网络聚焦聚类挖掘，亮点在于亲和度阈值 NAT 随迭代动态计算，摒弃外部人工干预，从而收敛出稳健的网络结构。

### 3.3.3 人工免疫数据挖掘：特质画像与方法论内核 [31]

纵览全局，基于人工免疫系统的数据分析与挖掘实现框架，可拆解为八大支柱维度：

- （1） **数据项映射**：如何将挖掘问题/数据投射为抗原、抗体或免疫器官。
- （2） **节点与拓扑定义**：节点/连接的语义内涵，刻画网络骨架。
- （3） **网络冷启动**：初始拓扑构建、训练/测试数据向抗原抗体的转化分配。
- （4） **刺激动力学演化**：抗原驱动、邻域协同刺激与抑制的量化机制。
- （5） **刺激驱动的网络进化控制**：基于刺激水平的网络规模调节策略。
- （6） **克隆策略与速率设定**：单节点克隆模式与频率的决策逻辑。
- （7） **种群全生命周期管理**：训练期与测试期的边界划分与种群调控。
- （8） **收敛判据与语义解码**：停机条件选取及结果网络的业务解释。

此外，阈值体系的精细化设计至关重要：修剪时机、节点合并触发点、控制参数的丰度与自组织特性的博弈平衡。

#### 3.3.3.1 核心特质：为何选免疫？

人工免疫系统自带高性能信息处理基因，彰显五大鲜明特质：

- （1） **并行分布式计算范式**：抗体-抗原相互作用网络天然支持高并发、分布式信息速处理。
- （2） **网络化知识表征**：以网络拓扑承载学习过程与结果的双重表达。
- （3） **小规模架构处理大规模数据**：精简组织单元高效存储海量模式知识。
- （4） **动态网络赋能持续学习**：记忆保持与快速响应耦合，支撑终身学习。
- （5） **模式驱动的处理范式**：天然契合分类、聚类、关联分析等模式识别核心任务。

#### 3.3.3.2 方法论基本范式：怎么用免疫？

将免疫原理落地数据挖掘，通常遵循七步走战略：

- (1) **编码先行**：选取适配编码方式重述问题信息，降低识别处理复杂度。
- (2) **柔性匹配驱动学习**：引入互补匹配、不完全匹配等机制，拓宽识别边界。
- (3) **刺激度调控识别流**：利用刺激度机制作为“阀门”，筛选提炼泛化模式。
- (4) **克隆变异双引擎**：克隆固化优质模式，变异注入多样性源头活水。
- (5) **网络拓扑赋能分布式智能**：节点间交互编织网络，落地分布式处理与存储。
- (6) **动态拓扑支撑增量演化**：网络随知識更新重构，陈旧无刺激模式自动淘汰，实现知识新陈代谢。
- (7) **免疫记忆加速冷启动**：利用先验知识优化初始网络，引导后续学习收敛。

## 3.4 章末综述

本章系统梳理了人工免疫系统的仿生根基，深度剖析了经典模型谱系，厘清了生物免疫机理向计算模型的迁移路径与方法论。基于对生物免疫信息处理特性的洞见，细致考察了典型人工免疫系统在数据挖掘落地中的应用图景与核心范式。提炼出的 AIS 基础框架及其挖掘方法基本范式，为后续研究铺就基石。

**AIS 基础框架范式**：在抗体刺激度引导下，抗体反复与抗原博弈，历经亲和力成熟迭代，终获能表征问题解特征的优质抗体种群。

**免疫数据挖掘方法范式**：立足网络拓扑，施行并行分布式计算；以网络形态表达结果；以精简架构驾驭海量信息；以动态网络机制践行可持续学习。

# 参考文献

- [1] CASTRO D E, ZUBEN V. Artificial Immune Systems: Part I-Basic Theory and Applications[R/OL]. Technical Report-RT DCA 01/99. http://www.dca.fee.unicamp.br.

- [2] 林学颜，张玲. 现代细胞与分子免疫学[M]. 北京：科学出版社，1999.

- [3] JERNE N K. The immune system[J]. Scientific American，1973，229(1)：51-60.

- [4] DASGUPTA D, Yu S, MAJUMDAR N S. MILA—Multilevel Immune Learning Algorithm[C]. In the proceedings of the Genetic and Evolutionary Computation Conference ( GECCO ), Chicago，July 12-16，2003；468-474.

- [5] JERNE N K. Towards a network theory of the immune system[J]. Annual Immunology，1974，125C：373-389.

- [6] PERELSON A. Immune network theory [J]. Immunological Review，1989，110：5-36.

- [7] FARMER J D, PACKARD N H, PERELSON A S. The immune system，adaptation and machine learning[J]. Physica D，1986，22：87-204.

- [8] FORRET S，HOFMEYR S A. Immunology as information processing [M]. In：Segel and Cohen eds. Design Principles for the Immune System and other Distributed Autonomous Systems. USA：Oxford University Press，2000.

- [9] OPREA M. Antibody Repertoires and Pathogen Recognition：The role of germline diversity and somatic hypermutation[D]. Univ. of New Mexico. Albuquerque, NM. 1999.

- [10] HIGHTOWER R, FORREST S, PERELSON A. The Baldwin effect in the immune system:learning by somatic hypermutation[M]. In Mitchell M, Belew R (eds) Adaptive Individuals Evolving Populations, Models and Algorithms, Addison-Wesley, 1996.

- [11] PERELSON A S, OSTER G F. Theoretical Studies of Clonal Selection : Minimal Antibody Repertoire Size and Reliability of Self-Nonself Discrimination [J]. Theor. Biol. , 1979(81) :645-670.

- [12] SMITH D J, FORREST S, HIGHTOWER R, et al. Deriving Shape Space Parameters from Immunological Data [J]. Theor. Biol. , 1997, 189 :141-150.

- [13] TIMMIS J. Visualising artificial immune networks[A]. Technical Report UWA-DCS-00-034, University of Wales and Aberystwyth, 2000.

- [14] FORREST S, HOFMEYR S A, SOMAYAJI A . Computer immunology [J]. Communications of the ACM, 1997, 40(10) :88-96.

- [15] 高坚. 用基于免疫机制的单亲遗传算法求解数据聚类优化问题[J]. 计算机工程与应用, 2003, 39(12) :89-90.

- [16] 刘静, 钟伟才, 刘芳, 等. 免疫进化聚类算法[J]. 电子学报, 2001, 29 (12A) :1897-1901.

- [17] 刘韬, 王耀才, 王致杰. 一种基于人工免疫系统的聚类算法[J]. 计算机工程与应用, 2005, 40(19) :182-184.

- [18] 行小帅, 潘进, 焦李成. 免疫规划 + K 均值混合聚类算法[J]. 计算机科学, 2003, 30(4) :150-152, 158.

- [19] 行小帅, 潘进, 焦李成. 基于免疫规划的 k-means 聚类算法[J]. 计算机学报, 2003, 26(5) :605-610.

- [20] HUNT J E, COOKE D E. Learning using an artificial immune system [J]. Journal of Network and Computer Applications, 1996, 19 (2) : 189-212.

- [21] TIMMIS J, NEAL M. A resource limited artificial immune system for data analysis[J]. Knowledge Based Systems, 2001, 14(3-4) :121-130.

- [22] TIMMIS J, NEAL M, HUNT J. Data analysis with artificial immune systems and cluster analysis and kohonen networks; Some comparisons [C]. In Proc. of Int. Conf. Systems and Man and Cybernetics, pages 922-927, Tokyo, Japan, 1999. IEEE.

- [23] TIMMIS J. On parameter adjustment of the immune inspired machine learning algorithm AINE[R]. Technical Report 12-00, Computing Laboratory, University of Kent at Canterbury, Canterbury, Kent. CT2 7NF. , November 2000.

- [24] TIMMIS J, NEAL M. Investigating the evolution and stability of a resource limited artificial immune system[M]. In Wu A S, editor, Special Workshop on Artificial Immune Systems, Genetic and Evolutionary Computation Conference (GECCO) 2000, Workshop Program, pages 40-41, Las Vegas, Nevada, U.S.A. , July 2000. AAAI, AAAI Press.