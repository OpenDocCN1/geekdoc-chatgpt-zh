**用 GPT-4 造数据，LLaVA 如何学会看图说话**

2023 年 Liu 等人做了一次很有启发的尝试，让纯文本的 GPT-4 来合成多模态指令跟随范例。他们对模型产出的指令进行人工校准，并据此发布了一个名为 LLaVA 的大规模多模态模型，即大语言视觉助手。该系统做端到端训练，把视觉编码器和大语言模型拼在一起，目标是让模型在通用场景下兼顾图像理解和语言生成。

做多模态对话机器人最头疼的就是自然对话样本太少，同时出现图像和文字的聊天记录并不常见。面对这个瓶颈，团队设计了一套数据增强思路。先准备一类配对模态的基础集，比如图像描述，用作训练的起点；再借助 GPT-4 扮演智能数据增强器，模拟真实对话流程。这样即使自然数据稀缺，模型也能学会处理多模态交互的特性。图 11-15 就给出了一个示范，上方是喂给 GPT 的描述、框标注等提示，下方展示了三种不同类型的回复。需要说明的是，示例中的视觉图像并未参与对 GPT 的提示，仅作对照参考。

背后的逻辑并不复杂。把 CC3M 里的描述文本和对象检测器给出的边界框一起交给 GPT-4 就能工作。随着 SAM 等分割技术进步，空间定位信息的加入对落地精度帮助很大。GPT-4 本身没有视觉感知能力，所以无法做到百分百精确，但在不需要严格空间精度的任务上表现依然可用。

具体实现上，LLaVA 的网络结构如图 11-16 所示。语言侧选用了在纯文本指令微调中表现优秀的 LLaMA。图像侧采用 CLIP 的视觉编码器 ViT-L/14 提取特征 Z，再用一个轻量的线性投影把 Z 映射成与语言模型词嵌入同维度的 token 序列 H。这种映射既省参数又利于快速实验。作者也提到未来可以尝试更复杂的方案，比如门控交叉注意或能输出对象级特征的视觉编码器。

*vvq*

[PRE0]

▲图 11-15 遵循指令的数据工作示意（图片来源：Liu et al，2023）

[PRE1]

▲图 11-16 LLaVA 网络架构（图片来源：Liu et al，2023）

语言-图像多模态助手的训练被拆成两步。第一步是特征对齐预训练，研究团队把筛选后约 595k 条图像-文本对改写成单轮对话。对每张图随机抽一个语言指令，让助手用一句话概括图像内容，原描述作为标准答案。训练时视觉编码器和大语言模型都冻结，只学习映射矩阵，使输入的似然最大化。这一阶段相当于训练一个能与语言模型兼容的视觉“分词器”。第二步是端到端微调，视觉编码器保持冻结，映射层和语言模型一起更新。Liu 等在科学问答多模态推理集上取得了领先成绩。图 11-17 给出了一个对比示例。

[PRE2]

图 11-17 LLaVA 和 GPT-4 模型的视觉输入功能示例提示（图片来源：Liu et al，2023） [PRE3]

## 11.7 本章要点

本章围绕视觉语言模型展开，涵盖生成图像描述、视觉问答、视觉对话等任务。讨论涉及多模态落地、直接利用预训练模型、轻量适配、图文联合训练、检索增强以及视觉指令微调等多个方向。

可以看出，视觉语言模型在处理多模态理解和推理时潜力很大。融合视觉与文本信息后，它们能完成传统语言模型或纯计算机视觉难以胜任的工作。预训练与轻量适配的进展，也让这类模型更容易迁移到不同领域。展望未来，更高效的多模态对齐技术仍是研究重点，同时如何让模型支撑更细腻复杂的人机交互也有大量空间。随着发展，视觉语言模型有望在图像视频搜索、虚拟助手、自主系统等场景发挥更大作用。

# 第十二章

# AI 的隐形碳账单

联合国政府间气候变化专门委员会的报告指出，未来五年全球气温突破 1.5℃的概率达到 50%，这正是科学家为避免灾难性气候后果划定的红线。一旦越线，人类生活与生态系统都将承受巨大冲击。因此大幅削减碳排放势在必行。

人工智能本可助力应对气候变化，比如把可再生能源接入电网、降低碳捕集成本。但技术自身的可持续性同样必须审视。大语言模型体量和复杂度持续攀升，训练所需算力激增，对环境造成明显压力。有估算显示，训练一个大模型产生的排放，可能相当于数十辆汽车全生命周期的排放。

随着 AI 应用遍地开花，其环境影响愈发受关注。要真正减轻负面效应，需要先了解模型如何嵌入整体环境图景，以及如何量化训练阶段的排放。同时要认识到个人在监测和降低影响上的责任。

本章首先梳理 AI 与机器学习系统的能耗与温室气体排放，其次探讨估算训练排放的难题与对策，最后介绍云环境中 AI 的碳强度。

[PRE4]

## 12.1 能源消耗与排放

数据中心的碳足迹日益成为焦点，尤其是电力消耗。碳排放对气候的影响最大，而数据中心的排放高度依赖供电结构。

碳强度指单位电量所对应的碳排放量。化石燃料发电会直接释放温室气体，但可再生能源在建设、采矿、废弃环节也会产生排放。比较不同数据中心时，供电来源差异会拉开很大差距。例如同样任务在以水电为主的加拿大魁北克省执行，排放可能比在以煤炭为主的爱沙尼亚低约 96.7%。

因此数据中心需要评估自身碳足迹并采取减排措施，包括使用清洁能源、提升能效、优化废物管理等。这些举措能显著降低排放，为更可持续的数字基础设施贡献力量。

[PRE5]

## 12.2 如何估算训练排放

估算模型训练的排放有多种工具和方法，其中机器学习二氧化碳影响计算器是常用方案，可结合硬件、运行时间、服务商和地区来估算。自然语言处理社区早在 2019 年 Strubell 等的工作中就开始讨论这个问题。

Strubell 等提出先用论文报告的训练时长和硬件推算总耗时，再换算为千瓦时。综合功耗包括 GPU、CPU、DRAM，并乘以电源使用效率 PUE 来计入制冷等额外能耗。按 Ascierto 2018 年数据，全球数据中心平均 PUE 为 1.58。训练总功率 p_t 的公式为：

p_t = 1.58(p_c + p_r + g × p_g)/1000

其中 p_c 为 CPU 平均功耗，p_r 为 DRAM 平均功耗，p_g 为单张 GPU 平均功耗，g 为 GPU 数量。

二氧化碳排放估算为：

CO[2]eq = 0.954 p_t

该系数基于美国环保局的平均排放因子，反映了天然气、煤炭、核能和可再生能源的混合比例。

Patterson 等 2021 年给出更简化的公式，同时考虑训练与推理：

碳足迹 = (电能训练 + 查询 × 电能推理) × CO[2eq] / kWh

他们指出约 80% 的机器学习负载是推理而非训练，这对排放核算方式产生影响。模型架构、处理器选择、数据中心效率和能源结构都会改变结果。谷歌公布的估算显示，T5 约产生 86 MWh 和 47 t 二氧化碳当量，GPT3 约产生 1287 MWh 和 552 t 二氧化碳当量。二氧化碳当量是把不同温室气体统一换算为等效二氧化碳的指标。

**12.2.1 测量云实例中人工智能的碳强度**

电网碳强度会随地点和时间波动，受能源组合、需求、发电方式影响。碳排放强度在小时、日、季节尺度上都变化。为了利用这些波动，业界提出碳感知计算，推荐在低碳时段或低碳区域调度任务。

绿色软件基金会正推动相关标准和工具。微软、AI2 以及希伯来大学、卡内基梅隆大学、Hugging Face 的研究者采用软件碳强度 SCI 规范，衡量 Azure AI 工作负载的运行排放。具体做法是把云工作负载的能耗乘以供电电网的碳强度。

SCI 使用后果性碳核算方法，衡量决策带来的边际排放变化。通过对比 11 个机器学习模型的 SCI，团队获得了相对排放水平的认识，为用户和云厂商提供更合理的减排依据。

[PRE6]

## 12.3 小结

人工智能和机器学习越发普及，其环境成本也必须纳入考量。本章讨论了它们在能耗和温室气体排放方面的表现。没有单一解法，需要技术、政策和行业协同。尽管环境代价存在，AI 的潜在福祉依然巨大，关键是以负责任、可持续的方式推进。在研发和部署过程中，优先考虑环境可持续性，才能让技术既创新又对地球友好。

# 参考文献

A. Aghajanyan, L. Zettlemoyer, and S. Gupta. Intrinsic dimensionality explains the effectiveness of language model fine-tuning. *arXiv preprint arXiv:2012.13255*, 2020.

A. Aghajanyan, B. Huang, C. Ross, V. Karpukhin, H. Xu, N. Goyal, D. Okhonko, M. Joshi, G. Ghosh, M. Lewis, et al. Cm3: A causal masked multimodal model of the internet. *arXiv preprint arXiv:2201.07520*, 2022.

M. Ahn, A. Brohan, N. Brown, Y. Chebotar, O. Cortes, B. David, C. Finn, K. Gopalakrishnan, K. Hausman, A. Herzog, et al. Do as i can, not as i say: Grounding language in robotic affordances. *arXiv preprint arXiv:2204.01691*, 2022.

J. Ainslie, S. Ontanon, C. Alberti, V. Cvicek, Z. Fisher, P. Pham, A. Ravula, S. Sanghai, Q. Wang, and L. Yang. Etc: Encoding long and structured inputs in transformers. *arXiv preprint arXiv:2004.08483*, 2020.

J. Ainslie, T. Lei, M. de Jong, S. Ontañón, S. Brahma, Y. Zemlyanskiy, D. Uthus, M. Guo, J. Lee-Thorp, Y. Tay, et al. Colt5: Faster long-range transformers with conditional computation. *arXiv preprint arXiv:2303.09752*, 2023.

E. Akyürek, T. Bolukbasi, F. Liu, B. Xiong, I. Tenney, J. Andreas, and K. Guu. Towards tracing knowledge in language models back to the training data. In *Findings of the Association for Computational Linguistics: EMNLP 2022*, pages 2429-2446, 2022a.

E. Akyürek, D. Schuurmans, J. Andreas, T. Ma, and D. Zhou. What learning algorithm is in-context learning? investigations with linear models. *arXiv preprint arXiv:2211.15661*, 2022b.

J.-B. Alayrac, J. Donahue, P. Luc, A. Miech, I. Barr, Y. Hasson, K. Lenc, A. Mensch, K. Millican, M. Reynolds, et al. Flamingo: a visual language model for few-shot learning. *arXiv preprint arXiv:2204.14198*, 2022.