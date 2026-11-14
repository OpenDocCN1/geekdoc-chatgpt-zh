参考文献

- [37] YAMAGUCHI Z, TASHIMA K, et al. Multiple-valued immune network model and its simulations [C]. In: Proc 27th International Symposium on Multiple-Valued Logic, Antigonish, Nova Scotia, Canada, 1997: 519-524.
- [38] CASTRO D E, ZUBER V, DEUS G A. The Construction of a Boolean competitive neural network using ideas from immunology[J]. Neurocomputing, 2003(50): 51-85.
- [39] VERTOSICK F T, KELLY R H. The immune system as a neural network: a multi-epitope approach[J]. J. Theor. Biol. 1991(155): 675-689.
- [40] FORREST S, HOFMEYR S A, SOMAYAJI A. Computer immunology [J]. Communications of the ACM, 1997, 40(10): 88-96.
- [41] POTTER M A, DE JONG K A. The convolution of antibodies for concept learning[C]. In: Eiben A E, Back T. Proc of the Fifth Int Conf on Problem Solving from Nature (PPSN-98). Berlin; Springer-Verlag, 1998.
- [42] FORREST S, HOFMEYR S A. Immunology as information processing [M]. In: Segel and Cohen eds. Design Principles for the Immune System and other Distributed Autonomous Systems. USA; Oxford University Press, 2000.
- [43] CASTRO D E, ZUBEN V. The Clonal Selection Algorithm with Engineering Applications[C]. (Full version, pre-print). In Proceedings of GECCO'00 (Workshop Proceedings): 36-37.
- [44] MITSUMOTO N, FUKUDA T, SHIMOJIMA K, et al. Autonomous robotic system and biologically inspired immune swarm strategy as a multi-agent robotic system[C]. In: Proc IEEE International Conference on Robotics and Automation, Minneapolis, Minnesota, USA, 1995: 2187-2192.
- [45] MITSUMOTO N, HATTORI T, IDOGAKI T, et al. Self-organizing micro robotic system[C]. In: Proc 6th International Symposium on Micro Machine and Human Science, Nagoya, Japan, 1995: 261-270.
- [46] WOLFRAM S. Approach to complexity engineering [J]. Physica 22D, 1986: 385-399.
- [47] FORREST S, JAVORNIK B, SMITH R E, et al. Using Genetic Algorithms to Explore Pattern Recognition in the Immune System[J]. Evolutionary Computation, 1993, 1(3): 191-211.
- [48] ANDREW WATKINS, LOIS BOGGESS. A New Classifier Based on Resource Limited Artificial Immune Systems [C]. In Proceedings of Congress on Evolutionary Computation, Part of the 2002 IEEE World Congress on Computational Intelligence held in Honolulu, HI, USA, May 12-17, 2002: 1546-1551. IEEE, May 2002.
- [49] CASTRO D E, TIMMIS J. Artificial Immune Systems; A Novel Approach to Pattern Recognition[M]. In Alonso L, Corchado J and Fyfe C, editors, Artificial Neural Networks in Pattern Recognition, 67-84. University of Paisley, January 2002.
- [50] HUNG C, COLEMAN T L. Artificial Immune Systems for Soil Data Classification[C]. Proceedings of the 15th International Conference on Computers And Their Applications (CATA-2000), March 29-31, New Orleans, LA, 2000: 358-360.
- [51] TIMMIS J, NEAL M, HUNT J. Data analysis with artificial immune systems and cluster analysis and kohonen networks; Some comparisons [C]. In: Proc. of Int. Conf. Systems, Man and Cybernetics: 922-927, Tokyo, Japan, 1999. IEEE.
- [52] TIMMIS J. Visualising artificial immune networks[A]. Technical Report UWA-DCS-00-034, University of Wales and Aberystwyth, 2000.
- [53] CASTRO D E, ZUBEN V. AiNet: An Artificial Immune Network for Data Analysis[M]. (Full version, pre-print). Book Chapter in Data Mining: A Heuristic Approach, Hussein A. Abbass, Ruhul A. Sarker, and Charles S. Newton (Eds.), Idea Group Publishing, USA.
- [54] HART E, ROSS P. Clustering Moving Data with a Modified Immune Algorithm[J/OL]. http://www4.wiwiss.fu-berlin.de/dblp/resource/record/conf/evoW/HartR01.
- [55] GONZALEZ F, DASGUPTA D. The Fuzzy Artificial Immune System; Motivations, Basic Concepts, and Application to Clustering and Web Profiling[C]. Published at IEEE International Conference on Fuzzy Systems. In the Proceedings of the IEEE World Congress on Computational Intelligence, Hawaii, May 12-17, 2002.
- [56] NEAL M. An artificial immune system for continuous analysis of time-varying data[C]. In: 1st International Conference on Artificial Immune System, Canterbury, UK, Berlin Heidelberg: Springer-Verlag, 2002.
- [57] WIERZCHON S, KUZELEWSKA U. Stable Clusters Formation in an Artificial Immune System[C]. In: 1st International Conference on Artificial Immune Systems (ICARIS), University of Kent at Canterbury, UK, September 9th-11th, 2002.
- [58] HART E, ROSS P. Exploiting the analogy between immunology and sparse distributed memories: a system for clustering non-stationary data [C]. In the proceedings of 1st International Conference on Artificial Immune Systems (ICARIS-2002), University of Kent at Canterbury, UK, September 9th-11th, 2002.
- [59] GONZALEZ F, et al. A Scalable Artificial Immune System Model for Dynamic Unsupervised Learning [C]. In: Cantu Paz ed. GECCO 2003, Berlin Heidelberg: Springer-Verlag, 2003: 674-681.
- [60] 莫宏伟. 人工免疫系统原理与应用 [M]. 哈尔滨: 哈尔滨工业大学出版社, 2002.
- [61] 李涛. 计算机免疫学 [M]. 北京: 电子工业出版社, 2004.

## 第 2 章 生物免疫系统：生命的智能防御堡垒

### 2.1 免疫系统概览

在自然界的生命体系中，免疫的本质是机体在历经病毒侵袭并痊愈后，获得针对该病原体的抵抗力，从而避免再次发病或被传染的能力。免疫系统由免疫活性分子、免疫细胞、免疫组织及器官共同构筑，其核心在于一套精准的“辨识机制”：它能清晰区分“自体”成分（自体分子）与“非自体”入侵者（如病原体、异体组织、外源性元素等）。在这一进程中，效应细胞作为主力军，既负责猎捕外来病原体及其毒素，也能锁定因基因突变产生的内源性肿瘤细胞，共同维护机体内环境的稳态。

该系统的识别谱极其广泛，理论上能应对无限种类的外源性抗原。正常的免疫功能表现为效应细胞产生恰当应答并高效清除靶标；一旦应答失衡——过度引发过敏性疾病，不足导致严重感染，或误伤自体诱发自身免疫病——均属免疫失调范畴，皆对机体有害。此外，免疫系统具备“免疫记忆”特性，能够档案式存储每一种感染源信息，使得二次遇袭时反应更迅猛、处理更高效。

### 2.2 免疫系统的架构组成

#### 2.2.1 系统的两大核心板块

作为一个高度复杂的协作网络，免疫系统由分子、细胞、组织器官层层叠加而成。本节重点剖析其两大功能支柱——淋巴系统与补体系统。

##### 2.2.1.1 淋巴系统：免疫细胞的“孵化器”与“主战场”

淋巴系统遍布全身，由淋巴器官及组织构成，是免疫细胞诞生、成熟与实战演练的核心场所。白细胞（特别是淋巴细胞）是执行免疫任务的主力军。按功能划分，淋巴器官分为“生产基地”（中枢淋巴器官：骨髓、胸腺）与“作战前线”（外周淋巴器官：淋巴结、脾脏、扁桃体等）。造血干细胞在中枢器官完成分化成熟，随后迁徙至外周器官执行应答任务。单核与淋巴细胞借助血液与淋巴循环往返穿梭，将免疫网络动态串联：既能将细胞部署至入侵前沿，又能将抗原信息回运至淋巴器官激活特异性应答。器官内，淋巴细胞与非淋巴细胞在成熟期及应答期通过互作共同完成免疫使命。

核心器官功能速览：

*   **淋巴管**：铺设全身的输送网络，运载淋巴液（含淋巴细胞与外源抗原）抵达免疫器官及血液循环。
*   **骨髓**：长骨腔内的造血软组织，构建微环境支持多能造血干细胞（HSC）增殖分化，产出粒细胞、单核、红细胞、血小板及B细胞。自胚胎晚期至全生命周期，骨髓都是功能性B细胞诞生的唯一“摇篮”。
*   **淋巴结**：淋巴管的汇聚节点，储存B、T等免疫细胞，是适应性免疫反应的主战场。
*   **胸腺**：骨髓来源的前体细胞汇聚于此增殖分化，最终化身为具备应答能力的T细胞。胸腺基质细胞（TSC）与胸腺细胞的互作，是功能性T细胞成熟的关键。
*   **脾脏**：人体最大的外周淋巴器官，兼具血液过滤与免疫应答功能。血液携带病原体入脾，经巨噬细胞处理成抗原片段后，激活T/B细胞发起特异性攻势。
*   **黏膜相关淋巴组织（MALT）**：含扁桃体、阑尾等。扁桃体守护呼吸道，阑尾护卫消化道。它们富含B、浆细胞、巨噬、T细胞，局部遇袭即刻激活，除履行固有免疫外，更能驱动B细胞分化为浆细胞实施局部特异性防御。

##### 2.2.1.2 补体系统：固有免疫的“先锋放大器”

补体系统由三十余种溶解性及膜结合蛋白组成，并非单一分子。它们潜伏于血清与组织液中，经激活后展现酶活性。作为抗体介导溶菌作用的必需“搭档”，补体广泛参与抗微生物防御、免疫调节，亦可介导免疫病理损伤，是机体主要的效应与信号放大系统。

补体提供最早期的固有应答：当补体分子识别结合特定细菌，便通过**溶解**（击穿菌膜致死）与**调理作用**（给细菌“贴标签”便于巨噬细胞识别吞噬）两大路径清除入侵者。自体细胞表面携带特异性蛋白“护身符”，可阻断补体结合，避免误伤。补体与巨噬细胞的协同，多发生在感染初期。

#### 2.2.2 免疫防御的多层次立体结构

免疫防线呈现立体分层架构，由不同层级的防御模块叠加而成：

1.  **物理屏障（第一道防线）**：以皮肤为主，构筑物理壁垒阻隔“外侵者”。皮肤黏膜的机械阻隔、分泌抑菌物质的化学杀伤，以及呼吸消化道黏膜巨噬细胞、抗体、纤毛摆动、咳嗽喷嚏等排出机制，均属此层。
2.  **生理/化学屏障（第二道防线）**：唾液、泪液、汗液中的溶菌酶，胃酸的强酸环境，以及正常体温、pH值等非特异性条件，均能灭活或抑制病原体生存。
3.  **固有免疫系统（第三道防线）**：由游走的吞噬细胞（中性粒、巨噬细胞等）组成，提供快速、非特异性的细胞级清除。
4.  **适应性免疫系统（第四道防线）**：以淋巴细胞为主力，具备高度特异性与记忆力，能“量身定制”针对性攻击策略，高效铲除病原体。

### 2.2.3 免疫细胞家族谱系

#### 2.2.3.1 造血干细胞：万细胞之源

血液与淋巴液中循环着形态各异、分工明确的细胞群：红细胞、粒细胞、单核细胞、淋巴细胞、血小板。它们寿命差异巨大：红细胞约120天，粒细胞仅20-62小时，血小板5-10天，单核细胞骨髓期约50天、血液期超200天，而淋巴细胞可存活数月至数年。细胞生灭更替维持动态平衡，数量质量异常将诱发血液或免疫疾病。溯源而言，上述所有细胞均源自同一始祖——**多能造血干细胞（HSC）**。