# 迁移学习与领域自适应领域经典文献速览

回顾机器学习发展历程，跨域知识迁移始终是核心热点。以下梳理了一批奠基性与里程碑式的工作，涵盖理论奠基、深度适配、对抗训练、多任务协同及垂直场景落地等多个维度，为相关研究提供脉络参考。

**早期理论探索与统计学奠基**  
千禧年前后，研究者聚焦于马尔可夫决策过程（MDP）度量与结构风险最小化。Ferns、Panangaden 与 Precup（2004）在 UAI 大会上提出了有限 MDP 的度量体系；Ferguson 与 Mahadevan（2006）则在 ICML 迁移学习研讨会上，借助谱方法实现了原型迁移。Friedman、Hastie 与 Tibshirani（2001）合著的《统计学习基础》更是成为该领域的教科书级参考。Getoor 与 Taskar（2007）编著的《统计关系学习导论》进一步拓展了关系数据建模的视野。

**跨网络与异构数据迁移**  
Fang 等（2015）在 *IEEE TKDE* 上发布 TrGraph 框架，利用公共签名子图实现跨网络迁移。Ganguly 与 Pudi（2017）于 ECIR 会议提出 Paper2vec，融合图拓扑与文本语义表征学术论文。Gao、Chen、Feng 等（2019）在 WWW 大会展示了无需共享用户隐私数据的跨域推荐方案；更早的 Gao、Luo、Chen 等（2013）则在 ECML PKDD 上采用簇级潜在因子模型攻克该难题。Fong 与 Weber-Jahnke（2012）在 *IEEE TKDE* 探讨了利用“未实现数据集”保护决策树学习隐私的可能性。

**深度学习时代的领域自适应范式革新**  
Glorot、Bordes 与 Bengio（2011）在 ICML 首次将深度学习引入大规模情感分类跨域适配。Ganin 与 Lempitsky（2015）于 ICML 提出反向传播实现无监督域适应，随后联合 Ustinova、Ajakan 等人（2016）在 *JMLR* 系统阐述了域对抗神经网络（DANN）架构。Gong、Shi、Sha 与 Grauman（2012）在 CVPR 引入测地流核处理无监督场景。Gopalan、Li 与 Chellappa 先后在 ICCV 2011 与 *IEEE TPAMI* 2014 通过生成中间表示缓解域偏移。Ghifary 等人（2015 ICCV；2016 ECCV）则利用多任务自编码器与深度重建分类网络推动域泛化与无监督适应。

**对抗生成与风格迁移的视觉突破**  
Goodfellow 等（2014）在 NIPS 奠基性提出生成对抗网络（GAN），为后续跨域生成奠定基石。Gatys、Ecker 与 Bethge（2016）在 CVPR 利用卷积网络实现图像风格迁移，开启美学迁移新范式。Ghifary、Kleijn、Zhang 等（2016）在 ECCV 结合重建与分类进一步强化无监督适应鲁棒性。

**多任务、多语言与结构化学习**  
Gong、Ye 与 Zhang 连续在 KDD 2012、JMLR 2013 发布鲁棒及多阶段多任务特征学习框架。Görnitz 等（2011）在 NIPS 提出层级多任务结构化输出学习，服务于大规模序列分割。Gillick、Brunk、Vinyals 与 Subramanya（2016）在 NAACL-HLT 实现字节级多语言处理。Firat、Sankaran、Al-Onaizan 等（2016）于 EMNLP 探索零资源机器翻译。Giorgi 与 Bader（2018）在 *Bioinformatics* 验证迁移学习在生物医学命名实体识别中的有效性。

**对话系统与策略迁移的持续演进**  
Gašić 团队长期深耕统计对话管理。2013 年 SIGDIAL 大会奠基 POMDP 管理器扩展域适应；2014 年 INTERSPEECH 实现增量在线适应；2015 年在 ICASSP 提出分布式策略，同期 NIPS 研讨会与 ASRU 工作坊分别探讨多智能体协作与策略委员会机制。Genevay 与 Larochelle（2016）在 AAMAS 引入迁移学习实现用户个性化适配。

**优化理论与自动化机器学习**  
Germain、Habrard、Laviolette 与 Morvant（2013）在 ICML 基于 PAC-Bayes 理论为线性分类器域适应提供泛化界分析。Glorot 与 Bengio（2010）在 AISTATS 深度剖析深层前馈网络训练难点。Feurer、Klein、Eggensperger 等（2015）在 NIPS 推动高效鲁棒的自动化机器学习（AutoML）流程。

**视觉识别里程碑**  
Girshick、Donahue、Darrell 与 Malik（2014）在 CVPR 发布 R-CNN，构建丰富特征层级大幅推进目标检测与语义分割性能。Frome、Corrado、Shlens 等（2013）在 NIPS 提出 DeViSE 模型，对齐视觉与语义嵌入空间。

**认知启发与类比推理**  
Forbus、Gentner、Markman 与 Ferguson（1998）在 *JETAI* 论证类比映射的领域通用性，为跨域知识迁移提供认知科学视角。

这份清单虽不详尽，却勾勒出从统计学理论到深度架构、从单任务适配到多场景泛化的清晰演进脉络，值得研究者反复研读。