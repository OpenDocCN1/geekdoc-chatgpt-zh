## (6) SIGKDD 2018：伦敦盛会回顾与硕果盘点

### KDD Cup 2017 回眸：双冠王诞生

在阿里云主办的 KDD Cup 2017 “高速公路收费路段流量预测”赛事中，**Convolution 队**（成员涵盖微软的 Ke Hu 与 Pan Huang、北航的 Huan Chen、美团的 Peng Yan）凭借数据驱动的前瞻性策略，助力交通管理破解瓶颈，成功摘得桂冠。紧接着在 2018 赛季的双赛道较量——旅行时间预测与流量预测中，该队更是从 3547 支全球劲旅中杀出重围，**包揽双料冠军**，展现了跨机构协作解决真实交通拥堵难题的硬核实力。

### 大会概况：群英荟萃伦敦城

2018 年 8 月，第 24 届 SIGKDD 大会在伦敦帝国理工学院拉开帷幕。大会由帝国理工 Yi-Ke Guo 教授与 IBM 的 Faisal Farooq 博士联袂执掌大旗，清华大学唐杰副教授任副主席；学术与工业两条评审线分别由台大 Chih-Jen Lin 教授、罗格斯大学 Hui Xiong 教授，以及 Google 的 Andrei Broder 博士、马格德堡大学 Myra Spiliopoulou 教授把关。来自 99 国的 3377 名学者齐聚一堂，共襄盛举。

投稿规模再创新高：研究类论文 983 篇，录用 107 篇口头报告（录用率 10.9%）与 74 篇海报（7.5%）；应用数据科学类 496 篇，录用 40 篇口头（8.0%）与 72 篇海报（14.5%）。四场主题演讲阵容星光熠熠：帝国理工 David Hand 教授、诺奖得主 Alvin E. Roth、DeepMind 科学家 Yee Whye Teh 教授、哥大 Jeannette M. Wing 教授，分别从统计学、经济学、机器学习与数据科学前沿视角开启智识碰撞。

---

### 荣誉榜单：定义领域标杆

#### 【最佳研究性论文】

**🏆 最佳论文奖：图分类模型的对抗性攻击**  
*作者：Daniel Zügner 等*  
针对离散域特性，团队设计基于增量计算的高效攻击算法。实验揭示：微小扰动即可显著摧毁节点分类准确率，且攻击具备跨模型迁移性，能推广至其它最先进分类器，为图神经网络安全敲响警钟。  
🔗 [论文链接](http://h5ip.cn/uy96)

**🏆 最佳学生论文奖：XiaoIce Band — 流行音乐旋律与编曲生成框架**  
*作者：Hongyuan Zhu 等（微软小冰团队核心成员）*  
面对和弦进行、节奏型、多音轨和声等音乐创作的高复杂性，论文构建基于和弦的节奏-旋律交叉生成模型（CRMCG）输出带和弦旋律，再经多乐器联合编曲模型（MICA）实现多轨协同。在十万级曲库上训练，让 AI 真正懂得“作曲”与“编曲”的区别。  
🔗 [论文详情](https://www.aminer.cn/archive/xiaoice-band-a-melody-and-arrangement-generation-framework-for-pop-music/5b67b45517c44aac1c8607e9)

#### 【最佳应用数据科学论文】

**🏆 最佳论文奖：Airbnb 实时个性化搜索排序的 Embedding 实践**  
*作者：Mihajlo Grbovic、Haibin Cheng (Airbnb)*  
定制化 Embedding 模型同步捕捉用户长短期兴趣，经严格线上线下 A/B 测试验证后全量上线，大幅提升房源推荐精准度与实时响应能力。  
🔗 [论文详情](https://www.aminer.cn/archive/real-time-personalization-using-embeddings-for-search-ranking-at-airbnb/5b67b45517c44aac1c8607cb)

**🏆 最佳学生论文奖：ActiveRemediation — 密歇根弗林特铅管筛查实战**  
*作者：Jacob Abernethy 等（跨校合作团队）*  
融合统计建模与自适应收入特征，向政府输出“优先检测/更换管道”推荐名单，并将方法论推广至全国基建投资决策，展现数据科学介入公共卫生危机的范式价值。  
🔗 [论文详情](https://www.aminer.cn/archive/activemediation-the-search-for-lead-pipes-in-flint-michigan/5b67b45517c44aac1c860889)

#### 【最佳博士论文梯队】

| 排名 | 论文题目 | 作者 / 导师 | 核心贡献 |
| :--- | :--- | :--- | :--- |
| **冠军** | **从文本挖掘实体与关系：轻量级标注范式** | Xiang Ren / 指导：Jiawei Han | 提出原则性、可扩展框架，以极低人工标注成本跨领域、跨体裁、跨语言抽取类型化实体及关系。 |
| **亚军** | **演化在线社区可信度分析的概率图模型** | Subhabrata Mukherjee / 指导：Gerhard Weikum | 联合建模用户交互、社区动态与文本内容，自动量化内容可信度与用户专长演变轨迹。 |
| **季军** | **Web 恶意行为刻画与检测：五类模型全景解析** | Srijan Kumar / 指导：V.S. Subrahmanian | 构建五大算法精准识破破坏者、恶搞者、傀儡、巨魔及欺诈评论者，赋予恶意行为可解释性洞察。 |

#### 【时间检验奖：经典永流传】

**《因子分解邂逅邻域：多面协同过滤模型》 (KDD 2008)**  
*作者：Yehuda Koren（Netflix 大赛百万奖金主力）*  
这篇“教科书级”奠基之作，在统一框架下融合邻域模型（擅长局部关系）与潜在因子模型（即今之 Embedding，擅长全局结构）。十余载风雨，其工程落地指导意义依然未减。  
🔗 [经典回顾](https://www.aminer.cn/archive/factorization-meets-the-neighborhood-a-multifaceted-collaborative-filtering-model/53e9bc74b7602d97048f4169)

#### 【SIGKDD 终身荣誉奖】

- **创新贡献奖：刘兵教授 (UIC)**  
  观点挖掘与情感分析开拓者，ACM/AAAI/IEEE 三料 Fellow，曾任 SIGKDD 主席（2013-2017），深耕数据挖掘核心版图二十载。
- **服务奖：唐杰教授 (清华大学)**  
  AMiner 缔造者，200+ 高被引论文、20 项专利。长期担任 KDD、CIKM、WSDM 等顶会要职，为华人学术共同体搭建国际舞台立下汗马功劳。

---

### KDD Cup 2018：空气质量预测新战场

赛题聚焦中英双城（北京 PM2.5/PM10/O3、伦敦 PM2.5/PM10）未来 48 小时浓度预测。吸引 49 国、3000+ 院所、5687 名选手组队 4180 支角逐。

- **🥇 冠军：First floor to eat Latiao 队**  
  中南大学 Haoran Jiang、Binli Luo 联手北邮 Jindong Han、Juan Liu、Qianqian Zhang 夺魁。
- **🥈 亚军：getmax 队**  
  微软 Zhipeng Luo、北大 Jianqiang Huang、阿里 Ke Hu 组成的“全明星阵容”独揽三赛道两冠一亚，**成就 KDD Cup 史上唯一“单届三甲包揽”壮举**。值得注意的是，核心成员去年即率 Convolution 队拿下 2017 双冠，实力令人叹服。

*此外，最佳审稿人奖花落 George Valkanas；初创团队奖（Startup Award）由“智能一点”、ASTOUND、氪信科技、FactMATA、Inflect、Zugata 六家创新企业分享。*

---

### 1.4 近六年 KDD 代表作速览（上）：2013 纪事

本节精选近六届最佳论文及候选佳作深度解读，先睹 2013 芝加哥大会风采。当年两大看点 Session 聚焦 **大数据分析框架** 与 **社交网络分析**。

#### 🔧 大数据框架三杰

| 论文 | 核心突破 | 关键技术/成果 |
| :--- | :--- | :--- |
| **Big Data Analytics with Small Footprint: Squaring the Cloud**<br>(John Canny, Huasha Zhao) | 自研 **BID 引擎** + **BIDMat/BIDMath** 工具链，单机 CPU/GPU 协同将聚类分类速度提升数十倍。 | 矩阵计算与机器学习原语的底层重构。 |
| **Beyond Myopic Inference in Big Data Pipelines**<br>(Karthik Raman 等) | 提出 **概率管道模型**，将复杂推理分解为简单概率计算串联，显著加速 Top-K、Fixed Beam、Autadaptive 等推断。 | 复杂分析任务的模块化解耦范式。 |
| **TurboGraph: A Fast Parallel Graph Engine Handling Billion-scale Graphs in a Single PC**<br>(Wook-Shin Han 等) | 创新 **Pin-and-Slide** 并行执行模式，大幅提升 I/O 并发，单机即可处理十亿级图，超越 GraphChi 等前辈。 | 图计算引擎底层调度的工程极致。 |

#### 📈 大规模算法改进专场
四篇佳作分别切入：有限内存下传统算法落地、异构网络矩阵分解扩展两大方向，直击大规模数据落地痛点。

#### 🌐 社交网络分析三大热潮
1. **用户行为建模**：从单平台分享行为可信度验证，延伸至跨平台用户画像融合与群体智能涌现机制。
2. **影响力学习**：隐性影响力挖掘、从众效应量化、影响力驱动聚类与话题发现，应用场景显著拓宽。
3. **信息传播演化**：探讨传播对网络拓扑的反向重塑，及传播链路中节点可信度评估。

> *2014 年度精华解读，请见下一节展开。*

---

*(注：原素材中标记为 `## (2) SIGKDD 2014 论文解读` 的章节此处仅保留标题占位，详细内容待后续补充。)*