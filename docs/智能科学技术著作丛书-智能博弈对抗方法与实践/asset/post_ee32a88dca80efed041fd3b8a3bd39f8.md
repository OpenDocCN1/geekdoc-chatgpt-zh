
所有人打出来的牌。在打牌的过程中存在大量隐藏信息，具有高度的不确定性，因此，目前仅靠算力无法从根本上解决问题，还需要更强的直觉、预测、推理和模糊决策能力。麻将中的复杂策略和带有随机性的博弈过程更贴近人类真实的生活，麻将问题的突破将有助于解决现实生活中的复杂问题。

![](img/cabd2c20e917d014f37d4e65ac1e41e0_26_0.png)

图 1.11 麻将 AI 对战游戏界面

2019 年 8 月，微软在世界人工智能大会上正式宣布由微软亚洲研究院基于深度强化学习技术研发的麻将 AI “天风”（Suphx）[14]，成为首个在国际知名专业麻将平台上荣升十段的 AI，其实力超越该平台公开房间顶级人类玩家的平均水平。

## 4. 桥牌

桥牌 (图 1.12) 桥牌对抗分为叫牌阶段和打牌阶段。其技术水平主要体现在叫牌 (叫到最准确的定约) 和打牌的准确性上 (对牌分布的判断以及在给定判断下的最佳打法)。由于桥牌有相对繁复的博弈规则，而逐渐成为人工智能研究者跃跃欲试的对象。桥牌是混合条件下的多人不完全信息博弈，其最大特点是在竞争与合作的混合条件下，敌方与友方的信息都是部分可观的，不仅不知道对手的牌，也不知道对家 (友方) 的牌。桥牌的难点在于 “信息” 的传递和对 “行为” 的分析，拥有更多的 “社会性”，更像是在对抗环境下寻求与敌友合作一起对抗敌方。

1997 年，Bridge Baron 赢得了第一届世界计算机桥牌大赛。1998 年，该比赛的冠军被俄勒冈大学开发的 GIB[15] 获得，该 AI 被邀请参加了世界桥牌大赛，最终在 35 位参赛者中获得了第 12 名的成绩。在之后的十多年里，基于蒙特卡罗方法的人工智能程序 Jack[16] 和 WBridge5[17] 轮番取得了该比赛的冠军。2015 年，东京大学开发出 AI “爆打”。2019 年，田渊栋团队设计的桥牌计算机 AI[18]与 WBridge5 进行了 1000 局比赛，在双明手（double dummy）评估下，性能显著优于之前的最好算法。

![](img/cabd2c20e917d014f37d4e65ac1e41e0_27_0.png)

图 1.12 桥牌 AI 对战游戏界面

### 1.3.3 军事仿真类对抗

模拟仿真技术的研究，开始只是为了方法验证，如今可以迁移到现实世界中，尤其是迁移到具有鲜明对抗特征的军事仿真类对抗场景中。以美国为代表的世界军事强国提早预见了人工智能技术在军事领域的广阔应用前景，已提前布局了“深绿”（DeepGreen）、“洞察”（Insight）、可视化数据分析（visual data analysis, XDATA）、深度学习、文本深度挖掘与过滤（deep exploration and filtering of text, DEFT）、高级机器学习概率编程（probabilistic programming for advanced machine learning, PPAML）、“心灵之眼”（Mind's Eye）、指挥官虚拟参谋（commander's virtual staff, CVS）、“阿尔法”（Alpha）、“狗斗”（Dogfight）、“雅典娜”（Athena）、“指南针”（COMPASS）等研究计划，力求在智能化上与潜在对手拉开代差。

- 1. “深绿”

2007 年，美军启动了一项面向美国陆军、旅级的指挥控制领域的研究计划，取名“深绿”（源于 IBM 的“深蓝”），旨在运用计算机仿真技术，推演预测未来态势发展的多种可能，帮助指挥员提前进行思考是否需要调整计划，并协助指挥员生成新的替代方案。其包括“指挥官助手”“闪电战”“水晶球”三部分。“指挥官助手”支持用户以手绘草图结合语音的方式快速制定方案。“闪电战”快速对作战计划及战场态势进行仿真推演，生成一系列未来可能结果。“水晶球”收集各种计划方案，更新战场当前态势，控制快速仿真推演。胡晓峰等[19]以“深绿”计划为例，深入分析作战辅助决策中人工智能的难题和未来可能的解决途径。“深绿”计划可以视为美军发展指挥控制智能化迈出的第一步大胆尝试，可惜由于多方面原因于 2011 年被中止。

## 2. 指挥官虚拟参谋

美陆军着手打造的智能助手重要项目 CVS[20]是继“深绿”后美军发展指挥控制智能化的又一重要举措。CVS 借鉴 Siri、Watson 等产品理念，扮演类似参谋或助手的角色，旨在综合应用认知计算、人工智能和计算机自动化等智能化技术，来应对海量数据源及复杂的战场态势，提供主动建议、高级分析及针对个人需求和偏好量身剪裁的自然人机交互，从而为陆军指挥官及其参谋制定战术决策提供从规划、准备、执行到行动回顾，全过程的决策支持。

## 3. “阿尔法”与“狗斗”

2015 年，美国辛辛那提大学和空军实验室合作研发了一款智能空战人工智能程序“阿尔法”（图 1.13）[21]，在空战仿真环境中战胜人类退役的飞行员。在模拟多机空战中，机器(红方)控制 4 架战斗机，与 2 名人类飞行员(蓝方)分别控制的 2 架战斗机之间进行对抗，获得了全胜。蓝方有预警机支持，且在导弹性能和数量上占优。红方则凭借巧妙配合和超过人类 250 倍的反应速度反败为胜。对战中，蓝方施展出了钳形攻击等战术配合，以及在躲避红方导弹的同时抢占有利阵位的技巧。“阿尔法”可以通过类人的模糊逻辑思维，生成超视距空战中主动攻击、机动规避等攻防态势下的飞机航路规划、导弹攻击、武器选择等战术策略。“阿尔法”可以视为战术编队指挥控制，相对简单。但和围棋相比，战斗机控制起来要复杂得多，实时性要求也高得多。项目团队经过多年研究，尝试了众多方法，迭代了很多版本之后才达到这样的水平，虽然对于实用还有较大距离，但为指挥控制智能化提供了一种实现途径。

![](img/cabd2c20e917d014f37d4e65ac1e41e0_28_0.png)

图 1.13 “阿尔法”

2019 年 5 月，DARPA 宣布启动“空战演进”（Air Combat Evolution, ACE）项目，旨针对人机协同“狗斗”（Dogfight）①挑战展开研究，开发可信、可扩展、人类水平、AI 驱动的空战自主能力。2020 年 8 月，“阿尔法狗斗试验”（Alpha Dogfight trials）开始进行，8 个团队的 AI 在约翰·霍普金斯大学应用物理实验室开发的 1 对 1 空中格斗中驾驶模拟 F-16 战斗机。随后，冠军 AI 在模拟器中与一名经验丰富的 F-16 战斗机飞行员进行了 5 次模拟“狗斗”，最终以 5:0 击败了人类。

2021 年 3 月 18 日，DARPA 取得若干关键阶段性成果：进行视距内与视距外多飞机场景的人工智能虚拟空中格斗；进行人机共生飞行试验，以观测飞行员生理状况以及对人工智能的信任程度；对有载全尺寸教练机的初步修改，支持项目第三阶段的人工智能“飞行员”上机。

## 4. “雅典娜”

目前美军作战训练数据库中已有的数据并不支持机器学习和其他人工智能方法。美海军陆战队正在研制“雅典娜”（Athena）[22]，一款专门用于训练、测试未来人工智能应用的推演平台，获取用于测试军事决策的人工智能应用程序的大量数据。它提供了一系列战争游戏，用于收集玩家数据，优化人工智能应用程序。一旦有足够的数据量，“雅典娜”就可以自行模拟现代军事行动，并提出新型战术。分析认为，博弈实验平台已成为训练和测试人工智能应用程序的一种有效方法途径。正是因为面临缺数据的瓶颈问题，同时看到了这一新途径，美军推出了上述一系列举措。这些举措可以视为美军在为发展指挥控制智能化探路、打基础。

## 5. “指南针”

2018 年 3 月，DARPA 战略技术办公室发布了一项名为“指南针”（COMPASS）[23]的项目，旨在帮助作战人员通过衡量对手对各种刺激手段的反应来弄清对手的意图。目前采用的观察—调整—决策—行动（observe-orient-decide-act, OODA）环不适合于“灰色地带”作战，因为这种环境中的信息通常不够丰富，无法得出结论，且对手经常故意植入某些信息来掩盖真实目的。该项目试图从两个角度来解决问题：首先试图确定对手的行动和意图，然后确定对手如何执行这些计划，如地点、时机、具体执行人等。但在确定这些之前必须分析数据，了解数据的不同含义，为对手的行动路径建立模型，这就是博弈论的切入点。然后在重复的博弈论过程中使用人工智能技术在对手真实意图的基础上试图确定最有效的行动选项。

① 视距内空战。

## 6. “进攻性集群使能战术”

DARPA 的“进攻性集群使能战术”（offensive swarm-enabled tactics, OFFSET）(图 1.14)[24]项目设计有基于战术的游戏模块，用于开发集群战术。城市作战中，高耸的建筑物、狭窄的空间以及受限的视野形成的“城市峡谷”，限制了军事移动和战术实施。无人机和无人车在城市地区的空中侦察和建筑清剿作战中具有明显优势。为此，DARPA 启动了 OFFSET 项目，旨在利用大规模空中和地面自主机器人，大幅提升城市作战能力。项目设想利用 250 个以上的无人机或无人车的跨域集群，在 8 个城市街区自主执行 6h 的区域隔绝任务。

![](img/cabd2c20e917d014f37d4e65ac1e41e0_30_0.png)

图 1.14 “进攻性集群使能战术”

## 7. “打破游戏规则”

2020 年 5 月 13 日，DARPA 宣布启动 “打破游戏规则”（Gamebreaker）的人工智能探索项目（图 1.15）[25]，挑选了芭鹭系统公司、洛克希德·马丁公司在内的9 个研究团队开展研究。Gamebreaker 项目致力于开发并将人工智能应用到现有的开放世界视频游戏中，以定量评估博弈均衡，确定显著有助于均衡的基本参数，并探索对博弈最不稳定的新功能、战术和规则修改。平衡游戏通常更具娱乐性，而市场压力有助于推动游戏的发展，因此商业游戏业对保持游戏平衡有着长期的兴趣。在未来的冲突中，DARPA 的投资旨在最大化不平衡，以创造优势，或在对手寻求优势时寻求平衡。

Gamebreaker 项目的目标不是从头开始，而是利用游戏行业在这些领域的重要人工智能发展，并在这些领域的基础上为 DARPA 独有的目的而发展。被选中的每个团队都提出了两场他们将试图打破规则的游戏。用来打破第一场游戏规则的人工智能将在第二场游戏中进行测试。

## 1.4 主要内容及章节安排

本书编撰的目的是帮助智能博弈对抗领域的初学者快速入门并掌握基本方法，为未来的研究和应用夯实基础。本书为深入研究智能博弈对抗问题提供全局视野、基本理论和实践方法，为后续研究奠定良好的基础。

本书按照“理论方法、应用实践、前沿展望”的纵向逻辑进行编撰，整体遵循“什么”——介绍智能博弈对抗，“为什么”——为什么要研究智能博弈对抗，“怎么办”——如何进行智能博弈对抗研究。其中“什么”主要瞄准智能博弈对抗理论基础，相关概念是一切研究的前提；“为什么”主要阐述当前研究智能博弈对抗的必要性和紧迫性；“怎么办”是研究重点，占据两大篇章，主要分为理论方法和应用实践，力求最大限度地方便初学者。本书的各章节独立性比较强，因此，初学者不必从头开始阅读，直接阅读自己需要的或者自己感兴趣的部分即可。本书各章节的主要内容如图 1.16 所示。

第 1 章介绍智能博弈对抗的基本概念，重点讨论智能博弈对抗的内涵、意义，从即时策略类对抗、序贯策略类对抗和军事仿真类对抗三个方面认真梳理智能博弈对抗相关研究应用。

第 2 章～第 4 章是理论方法，主要介绍智能博弈对抗的理论、相关基础方法。第 2 章基于博弈论相关基础知识介绍多智能体学习方法。第 3 章首先简要介绍马尔可夫决策过程，其次基于马尔可夫决策理论分别介绍强化学习方法、深度强化学习方法、分层强化学习方法，最后简要介绍满足分布式学习的三种分布式强化学习框架。第 4 章首先对手建模方法展开介绍，分析对手建模面临的挑战，对两大类对手建模方法（显式和隐式）进行简要细分类；其次围绕即时策略类对抗中对抗规划问题进行梳理，指出未来研究重点；最后围绕序贯策略类对抗中的对手剥削方法进行梳理，指出未来研究重点。

![](img/cabd2c20e917d014f37d4e65ac1e41e0_32_0.png)

图 1.16 本书各章结构图

第 5 章~第 8 章是应用实践，为三大类智能博弈对抗系统平台提出智能体设计思路及实践。第 5 章以《星际争霸》游戏为研究对象，首先将《星际争霸》问题抽象成即时策略对抗问题，分析其问题状态空间复杂度，总结该问题的研究挑战，有针对性地总结应对这些挑战的关键技术；其次介绍《星际争霸》的 AI 构建工作，包括《星际争霸》AI 研究历程、AI 环境；最后基于《星际争霸》多智能体挑战平台开展智能体实验验证。第 6 章以德州扑克为研究对象，首先介绍德州扑克基础知识，分析德州扑克问题复杂度，剖析关键技术；其次总结德州扑克 AI研究历程，介绍德州扑克智能博弈系统；最后围绕两人无限注和多人无限注德州扑克开展智能体实验验证。第 7 章分别以斗地主和桥牌为例，首先介绍游戏规则，分析问题复杂度以及关键技术；其次分析各类智能体的研究历程；最后在斗地主和桥牌游戏平台上开展智能体实验验证。第 8 章首先介绍兵棋基础，举例分析兵棋问题的复杂度，介绍解决兵棋问题的关键技术；其次介绍兵棋 AI 研究历程和兵棋智能系统的构成；最后在墨子兵棋推演系统上演示完整的兵棋 AI 开发过程。

第 9 章和第 10 章是前沿展望，紧跟智能博弈新方向，介绍智能博弈对抗的相关元理论和典型前沿应用。第 9 章简要介绍元宇宙、元博弈、元认知及元学习等最新前沿博弈对抗相关理论。第 10 章从三种博弈模型（微分博弈、攻防博弈和平均场博弈）出发，分析“视觉欺骗目标检测、复杂网络攻防和无人机集群对抗”共三个典型应用场景。

## 参考文献

[1] 中国人工智能学会. 中国人工智能系列白皮书——机器博弈[M/OL]. http://dl.caai.cn/storage/file/20210621/0649c1fd2107d5ba18ab14a394a234fc.pdf.[2022-05-01].

[2] Omids hafei S, Tuyls K, Czarnecki W M, et al. Navigating the landscape of multiplayer games[J]. Nature Communications, 2020, 11(1): 5603.

[3] 黄凯奇，兴军亮，张俊榜，等. 人机对抗智能技术[J]. 中国科学: 信息科学, 2020, 50(4): 540-550.

[4] Vinyals O, Babuschkin I, Czarnecki W M, et al. Grandmaster level in StarCraft II using multi-agent reinforcement learning[J]. Nature, 2019, 575 (7782): 350-354.

[5] Berner C, Brockman G, Chan B, et al. Dota 2 with large scale deep reinforcement learning [EB/OL]. http://arxiv.org/abs/1912.06680.[2021-08-01].

[6] Jaderberg M, Czarnecki W M, Dunning I, et al. Human-level performance in 3D multiplayer games with population-based reinforcement learning[J]. Science, 2019, 364 (6443): 859-865.

[7] Ye D H, Liu Z, Sun M F, et al. Mastering complex control in MOBA games with deep reinforcement learning[C]. Proceedings of the AAAI Conference on Artificial Intelligence, New York, 2020: 6672-6679.

[8] Silver D, Huang A, Maddison C J, et al. Mastering the game of Go with deep neural networks and tree search[J]. Nature, 2016, 529 (7587): 484-489.

[9] Silver D, Schrittwieser J, Simonyan K, et al. Mastering the game of Go without human knowledge[J]. Nature, 2017, 550 (7676): 354-359.

[10] Schrittwieser J, Antonoglou I, Hubert T, et al. Mastering Atari, Go, Chess and Shogi by planning with a learned model[J]. Nature, 2020, 588 (7839): 604-609.

[11] Bowling M, Burch N, Johanson M, et al. Heads-up limit hold’em poker is solved[J]. Science, 2015, 347 (6218) : 145-149.

[12] Moravčík M, Schmid M, Burch N, et al. DeepStack: Expert-level artificial intelligence in heads-up no-limit poker[J]. Science, 2017, 356 (6337) : 508-513.

[13] Brown N, Sandholm T. Superhuman AI for multiplayer poker[J]. Science, 2019, 365 (6456) : 885-890.

[14] Li J, Koyamada S, Ye Q, et al. Suphx: Mastering mahjong with deep reinforcement learning [EB/OL]. http://arxiv.org/abs/2003.13590.[2021-08-01].

[15] Amit A, Markovitch S. Learning to bid in bridge[J]. Machine Learning, 2006, 63 (3) : 287-327.

[16] Ando T, Kobayashi N, Uehara T. Cooperation and competition of agents in the auction of computer bridge[J]. Electronics and Communications in Japan, 2003, 86 (12) : 76-86.

[17] Ando T, Uehara T. Reasoning by agents in computer bridge bidding[C]. International Conference on Computers and Games, Hamamatsu, 2000: 346-364.

[18] Tian Y, Gong Q, Jiang Y. Joint policy search for multi-agent collaboration with imperfect information[J]. Proceedings of the 34th International Conference on Neural Information Processing Systems, Vancouver, 2020: 19931-19942.

[19] 胡晓峰, 荣明. 作战决策辅助向何处去——“深绿”计划的启示与思考[J]. 指挥与控制学报, 2016, 2 (1) : 22-25.

[20] Lisa H. Mission Command[M/OL]. https://fortbenningausa.org/wp-content/uploads/2016/04/Mission-Command-RDECOM-CERDEC.pdf.[2021-08-01].

[21] Ernest N D. Genetic fuzzy trees for intelligent control of unmanned combat aerial vehicles[D]. Cincinnati: University of Cincinnati, 2015.

[22] Benjamin J S C, Whyte C. Wargaming with Athena How to Make Militaries Smarter, Faster, and More Efficient with Artificial Intelligence[M/OL]. https://css.ethz.ch/en/services/digital-library/articles/article.html/f8940eb3-6066-409c-ae8c-e11e838e0c92.[2021-08-01].

[23] Roff H M. COMPASS: A new AI-driven situational awareness tool for the Pentagon?[EB/OL]. https://thebulletin.org/2018/05/compass-a-new-ai-driven-situational-awareness-tool-for-the-pentagon/.[2021-08-01].

[24] Timothy H. Chung. OFFSET OFFensive Swarm-Enabled Tactics[M/OL]. https://www.darpa.mil/work-with-us/offensive-swarm-enabled-tactics.

[25] Defense Brief Editorial. DARPA Will Try Ruining Video Games to Improve Pentagon's War Games[M/OL]. https://defbrief.com/2020/05/14/darpa-will-try-ruining-video-games-to-improve-pentagons-war-games/.[2021-08-01].
