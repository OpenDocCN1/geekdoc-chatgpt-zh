前已经拥有一百万个实体以及超过五百万条关系事实数据。YAGO 基于 WordNet 的知识体系，将 Wikipedia 中的类别与 WordNet 中的同义词集进行关联，同时将 Wikipedia 中的条目挂载到 WordNet 的体系下，这样的优势在于既自动扩充了知识库，同时又对海量的知识进行了组织和整理，参见图 1.6。YAGO 的实体关系数据都依赖人工严格定义的规则从 Wikipedia 中自动抽取产生，包括实体之间的上下位（Is-A）和实体属性等关系。目前在 YAGO 中，关系实例数据的准确率超过 95%。整个知识库使用 RDFs（RDF Schema）语言与 OWL（Ontology Web Language）语言描述，构成一个具有清晰完整逻辑定义的知识系统。值得一提的是，YAGO2（YAGO 的第二个版本）在 2017 年人工智能国际顶级学术会议 IJCAI2017 上获得由 Artificial Intelligence Journal（AIJ）颁发的卓越论文奖（Prominent Paper Award），表彰其对于人工智能领域的贡献。

![](bbox=[0.2, 0.4, 0.85, 0.7])

+   - Use selected categories as types
+   - Add WordNet for Taxonomy

图 1.6 YAGO：将 WordNet 与 Wikipedia 中获取的知识进行关联

# 6. BabelNet

![](bbox=[0.1, 0.77, 0.32, 0.86])

BabelNet[资源 1-17] 是一个多语言词汇级的语义网络和本体[351]，由罗马萨皮恩扎大学计算机科学系计算语言学实验室所创建。BabelNet 的主要特点是将 Wikipedia 链接到最常用的英语类义词典 WordNet 上，这一点类似于 YAGO，但是 BabelNet 加入了多语言支持。BabelNet 提供了多种语言的概念和命名实体，并包含了它们之间丰富的语义关系。目前，已经升级到 4.0 版本，覆盖了 284 种语言，包括全部的欧洲语言、大多数亚洲语言等。BabelNet 中所