抑制微调后出现的幻觉，核心思路集中在数据侧、训练方式和模型本身三方面协同改进。

- （1）数据丰富度：幻觉多在微调语料同质化严重时冒头。可借助随机遮蔽、角度旋转、尺寸缩放等增强手段扩充样本形态，也可掺入异域语料做混合训练，让模型接触更广的表达范式。
- （2）对抗式训练：构造带有细微扰动的人工样本参与训练，能逼迫网络捕捉输入的深层规律而非表层线索，从而增强抗干扰能力并降低臆造内容。
- （3）多任务联合：单一任务微调易过拟合。把若干相关任务并行引入，让模型从多视角汲取知识，可削弱对浅层特征的依赖，泛化力也随之提升。
- （4）架构设计：结构也会影响幻觉倾向。可放宽上下文窗口、加入注意力等机制以承载更完整的语境，同时合理规划层级深度和参数规模，在表达力与泛化力之间取得平衡。
- （5）数据质量管控：微调效果受语料质量牵制。正式训练前应做筛选清洗，确保样本准确、丰富且具代表性。

#### 5.5.2 微调面临的核心痛点
大模型微调在实践中常碰到以下几类难题。

灾难性遗忘是突出问题。模型在吸纳新任务时容易覆盖旧知识，导致既有能力衰减，多任务场景下尤为明显。除混合微调之外，回放等经典策略也能起到一定缓解作用。

数据偏斜同样关键。训练集的不均衡会让模型在某些子群体或低资源语言上表现下滑，例如少数族裔或小语种。改善路径是收集更均衡多元的语料，并配合增强、领域自适应与迁移学习等手段提升鲁棒性与泛化。

标注稀缺限制推进。部分垂直领域高质量标注成本高、获取难。可借助迁移学习把相关预训练任务的知识搬运过来，降低对大批量标注的依赖；也可用生成对抗网络合成数据来扩充训练规模。

域迁移与迁移学习本身亦具挑战。源域到目标域存在分布与表征差异。可在目标域上做领域自适应微调，或以多任务联合训练学习更通用的表征，从而改善迁移效果。

#### 5.5.3 微调前沿与未来走向
当前微调技术仍受遗忘、偏斜、稀缺与迁移等制约，数据增强、领域自适应、迁移学习等手段可在一定程度上缓解。面向将来，兼顾规模与效率、深化零样本学习是重要方向。持续迭代将帮助微调在各行业取得更好表现，同时需关注以下议题。

可解释性是关键研究点。尽管大模型性能强劲，却多被视为黑盒，决策依据难以追溯。研究者提出通过可视化输入的关键片段来解释判断依据，也可用生成对抗网络产出与决策相关的解释性样本。

伦理与隐私风险随应用扩大而凸显。模型可能存在偏见歧视，需确保公平无偏；处理个人隐私数据时必须做好保护。技术演进需同步纳入正则化、模型压缩加速、解释性方法以及伦理隐私保护机制。未来还应推动泛化能力提升、引入多模态信息融合[16]、增强长文本处理效率[17]、完善评估方法与数据[18]，并持续强化伦理与隐私保障。

## 参考文献

- [1] ZHANG X, YANG Q, XU D. Deepvt: Deep view-temporal interaction network for news recommendation[C/OL]//CIKM'22: Proceedings of the 31st ACM International Conference on Information & Knowledge Management. New York, NY, USA: Association for Computing Machinery, 2022: 2640-2650.
- [2] ZHANG X, YANG Q, XU D. Combining explicit entity graph with implicit text information for news recommendation[C]//WWW'21: Companion Proceedings of the Web Conference 2021. New York, NY, USA: Association for Computing Machinery, 2021: 412-416.
- [3] ZHANG X, YANG Q. Dml: Dynamic multi-granularity learning for bert-based document reranking[C/OL]//CIKM'21: Proceedings of the 30th ACM International Conference on Information & Knowledge Management. New York, NY, USA: Association for Computing Machinery, 2021: 3642-3646.
- [4] DU J, ZHANG X, WANG S, et al. Instance-guided prompt learning for few-shot text matching[C/OL]//GOLDBERG Y, KOZAREVA Z, ZHANG Y. Findings of the Association for Computational Linguistics: EMNLP 2022. Abu Dhabi, United Arab Emirates: Association for Computational Linguistics, 2022: 3880-3886.
- [5] WANG Y, KORDI Y, MISHRA S, et al. Self-instruct: Aligning language model with self generated instructions[J]. arXiv preprint arXiv:2212.10560, 2022.
- [6] SUN Z, SHEN Y, ZHOU Q, et al. Principle-driven self-alignment of language models from scratch with minimal human supervision[J]. arXiv preprint arXiv:2305.03047, 2023.
- [7] XU C, GUO D, DUAN N, et al. Baize: An open-source chat model with parameter-efficient tuning on self-chat data[J]. arXiv preprint arXiv:2304.01196, 2023.
- [8] ZHANG X, YANG Q. Self-qa: Unsupervised knowledge guided language model alignment[Z]. [S.l.: s.n.], 2023.
- [9] ZHANG X. MC^2: Multi-perspective convolutional cube for conversational machine reading comprehension[C]//Proceedings of the 57th Annual Meeting of the Association for Computational Linguistics. Florence, Italy: Association for Computational Linguistics, 2019: 6185-6190.
- [10] ZHANG X, WANG Z. Reption: Wide and deep interaction networks for machine reading comprehension (student abstract)[J]. Proceedings of the AAAI Conference on Artificial Intelligence, 2020, 34(10): 13987-13988.
- [11] ZHANG X, YANG Q. Generating extractive answers: Gated recurrent memory reader for conversational question answering[C/OL]//BOUAMOR H, PINO J, BALI K. Findings of the Association for Computational Linguistics: EMNLP 2023. Singapore: Association for Computational Linguistics, 2023: 7699-7704.
- [12] JOSEPH L M, ALBER-MORGAN S, CULLEN J, et al. The effects of self-questioning on reading comprehension: A literature review[J]. Reading & Writing Quarterly, 2016, 32(2): 152-173.
- [13] ZHANG X, YANG Q, XU D. Trans: Transition-based knowledge graph embedding with synthetic relation representation[J]. ArXiv, 2022, abs/2204.08401.
- [14] ZHANG X. Cfgnn: Cross flow graph neural networks for question answering on complex tables[J]. Proceedings of the AAAI Conference on Artificial Intelligence, 2020, 34(05): 9596-9603.
- [15] ZHANG X, YANG Q. Xuanyuan 2.0: A large chinese financial chat model with hundreds of billions parameters[C]//CIKM '23: Proceedings of the 32nd ACM International Conference on Information and Knowledge Management. New York, NY, USA: Association for Computing Machinery, 2023: 4435-4439.
- [16] ZHANG X, YANG Q. Position-augmented transformers with entity-aligned mesh for textvqa[C]//MM'21: Proceedings of the 29th ACM International Conference on Multimedia. New York, NY, USA: Association for Computing Machinery, 2021: 2519-2528.
- [17] ZHANG X, LV Z, YANG Q. Adaptive attention for sparse-based long-sequence transformer[C/OL]//Findings of the Association for Computational Linguistics: ACL 2023. Toronto, Canada: Association for Computational Linguistics, 2023: 8602-8610.
- [18] ZHANG X, LI B, YANG Q. Cgce: A chinese generative chat evaluation benchmark for general and financial domains[Z]. [S.l.: s.n.], 2023.

## 6 大语言模型强化对齐
RLHF 已成为大模型训练的关键环节。先用人类偏好标注数据训练奖励模型，再借助奖励信号与强化学习算法对语言模型做二次优化，可把输出与人类价值对齐，提升可用性与安全性，避免不安全内容。为此，RLHF 已成为主流范式，被视为大模型核心技术之一，例如 OpenAI 的 ChatGPT[1] 与 Meta 的 LLaMA[2] 系列均采用该方法提升能力。

本章系统梳理 RLHF 技术。全章共 8 节，6.1 节阐述强化学习基础；6.2 节与 6.3 节分别回顾近年主流深度强化学习方法，即 DQN 与策略梯度方法；6.4 节说明如何把强化学习用于大语言模型建模；6.5 节和 6.6 节分别详解奖励模型训练与 RLHF 训练流程；6.7 节介绍常用训练框架，为实践提供参考；6.8 节讨论 RLHF 的难点与现有问题，并展望发展方向。

### 6.1 强化学习基础

#### 6.1.1 强化学习的基本概念
强化学习本质是智能体与环境持续交互以学习决策。智能体是被训练的主体，如工业机器人或自动驾驶系统；环境是其所处的外部世界，如工厂作业场景或道路状况。目标是让智能体在不同状态下做出合理行动以累积最大回报。

交互可抽象为：环境给出初始状态 s0，智能体据此选动作 a0，环境返回奖励 r0 并转移到 s1；随后在 s1 下选 a1，环境给出 r1 并进入 s2，如此循环直至结束。以考试作比喻，环境像出题考官，状态相当于题目，动作是考生答案，奖励是得分。考官持续给题并评分，考生不断作答直至考试结束。完整交互序列为：

[PRE0]

也称这一交互过程为 1 次实验，τ 为一个轨迹。智能体可多次与环境交互，产生多条轨迹，这些轨迹的集合即经验 E，即

[PRE1]

基于经验 E，智能体能够归纳规律。若经验显示在状态 s_t 下采取动作 a_* 能获得较高奖励，则该状态下选择 a_* 的概率会被提升。

强化学习通常分为探索与学习两阶段。探索阶段按行为策略与环境交互生成经验；学习阶段基于经验优化目标策略。若行为策略与目标策略一致则为同策略算法，否则为异策略算法。

#### 6.1.2 强化学习中的随机性
强化学习的不确定性主要来自两方面，即环境随机性和策略随机性。

环境不确定性体现在状态转移上。给定状态 s_t 与动作 a_t，下一状态 s_{t+1} 的结果并非确定，而是随机分布的，这源于环境自身特性。策略不确定性则源于智能体决策本身的随机性。