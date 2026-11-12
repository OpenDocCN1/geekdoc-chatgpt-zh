## 尾声

这一章我们以红绿灯智能调度为切入点，梳理了问题背景，并层层递进地讲解了强化学习的核心概念，重点剖析了 Q-learning 与 Deep Q-learning 这两大经典算法，最后借助 CityFlow 模拟平台落地了代码实战。受限于篇幅，强化学习的广阔天地无法尽数呈现，但愿这部分内容能点燃你的好奇心，驱动你在课后主动探索、多动手敲代码，从而在实战中内化知识。

## 结语

太棒了，翻到这一页，意味着你已完整读完全书！恭喜你迈过了人工智能应用入门的关键一步。通过这 5 个生动的项目，你不仅接触了该领域的多个明星算法，更揭开了它们的“黑盒”，掌握了原理并能亲手用代码将其复现。希望这段阅读旅程让你切身体会到 AI 的趣味与魅力，种下持续探索的种子。若意犹未尽，欢迎前往配套实验平台 **www.boyu.ai/playground** 继续闯关，那里会持续上新有趣的应用供你把玩，你甚至可以尝试修改代码，创造专属的 AI 算法。

本书定位为青少年 AI 实践入门读物，重点覆盖了各项目的核心知识点与工程落地，但 AI 知识体系博大精深，还有大量进阶内容未涉及。若你渴望构建更系统的认知框架，强烈推荐阅读本系列第三册《人工智能技术入门：让你也看懂的 AI“内幕”》。

当然，除了这套丛书，市面上还有许多深度更大的教材教程。待你夯实数学基础后，不妨挑战更高阶的算法、更复杂的任务，甚至尝试改良现有算法。AI 领域日新月异，每年都有海量新算法涌现、新难题被攻克。愿你保持思考与实践的双轮驱动，未来能成为新算法的发明者，解决更多有价值、有意思的难题！

最后，衷心感谢你的陪伴阅读，愿你满载而归！

## 附录一 思考与实践参考答案

- **1.1** 一张 100×100 的 RGB 图像，每个像素由 3 个通道数值构成，总计需 100×100×3 = 30,000 个数字存储。
- **1.2** 除颜色外，还能记录透明度通道。以常见 PNG 格式为例，其像素数据同时包含 RGB 色彩值与 Alpha 透明度信息。
- **1.3** 完全可以。灰度图可视为形状为（高，宽，1）的三阶张量，通道数为 1。本质上，（x，y，1）张量与（x，y）矩阵等价，但部分需求三阶张量输入的工程代码中，需按前者格式处理（后续实战会遇到）。
- **2.1** 详见第 2.4 节“K 近邻算法的缺点”中的第二点说明。
- **3.1** 主流数据增强手段包括：① 调整图像对比度与亮度；② 随机裁剪；③ 叠加噪声扰动像素值。
- **6.1** 调大内容权重，输出图更贴近内容图细节；调大风格权重，生成图的风格迁移效果更强烈、浓郁。
- **6.2** 普通梯度下降收敛较慢且损失曲线波动明显；Adam 优化器收敛显著加速，损失平稳下降，表现优于前者，与 L-BFGS 相当。
- **6.3** 将目标图初始化为内容图（而非随机噪声），通常能获得更优的起始状态，收敛效果更好。
- **7.1** 视觉特征习得难度排序：颜色最易 → 纹理次之 → 几何形状最难。
- **7.2** 迁移可行性分析：(a) 可行；(b) 可行；(c) 效果一般——色彩纹理可换，几何结构难对齐；(d) 不可行——猫狗形态差异过大；(e) 不可行——毕加索画作多涉及几何结构的重组变形。
- **7.3** 核心在于循环一致性损失权重过大。此时双向变换重构图与原图差异极小，难以产生有效梯度约束。改进思路可参考：https://ssnl.github.io/better_cycles/report.pdf
- **8.1** 统计语言模型的直观短板在于：单词概率仅依赖前文。但在自然语言中，上下文皆为关键语境——如做英语语法填空，决定动词时态的时间状语常出现在动词之后。
- **9.1** N-gram 中 n 取大值：覆盖语境广但语料出现频次低，稀疏性强；n 取小值：高频易统计，但语境信息不足。
- **10.1** 实测为准。通常 LSTM 训练耗时更长，因其门控机制导致计算复杂度更高。
- **13.1** 开放题，参考思路：综合加权双方下一步可落子位置数量与控制区域面积。
- **13.2** 需编程实现，此处略过。
- **13.3** 优先搜到优质（或劣质）子节点，能更快触发 Alpha-Beta 剪枝阈值（最大/最小化当前估值）。因采用深度优先搜索，浅层剪枝一个分支，总搜索量将指数级骤减。
- **13.4** 置换表不精确源于哈希冲突。若忽略冲突，可能导致读取错误估值，干扰剪枝效率。实践中需在冲突处理开销与搜索效率间权衡，通常允许一定容错率。
- **13.5** MCTS 构建的搜索树高度不平衡：高胜率节点被深度扩展，这是 UCB 策略“乐观面对不确定性”特性的自然体现（正文已详述）。
- **13.6** 编程实践题，略。
- **15.1** 折扣因子 γ 越小，智能体越“近视贪婪”，聚焦即时回报；γ 越大，智能体越“深谋远虑”，重视长期累积收益期望。
- **16.1** 探索即鼓励尝试未知动作。UCB 法统计各动作被选次数，综合 Q 值与选取频次：Q 值高或尝试少的动作优先级更高。另一种“乐观初始值法”将所有 Q 值初始化为极大值，迫使智能体初期遍历尝试所有动作，随后价值回归合理区间。
- **16.2** 以博弈为例，游戏规则即环境模型。AlphaGo 便是基于模型的强化学习（MBRL）典范之作。

## 附录二 参考书目

- [1] LeCun, Y., Cortes, C., & Burges, C. J. (2010). MNIST handwritten digit database. *AT&T Labs*.
- [2] Deng, J., et al. (2009). Imagenet: A large-scale hierarchical image database. *IEEE CVPR*.
- [3] Krizhevsky, A., Sutskever, I., & Hinton, G. E. (2012). Imagenet classification with deep convolutional neural networks. *NeurIPS*.
- [4] Simonyan, K., & Zisserman, A. (2014). Very deep convolutional networks for large-scale image recognition. *arXiv:1409.1556*.
- [5] Taigman, Y., et al. (2014). Deepface: Closing the gap to human-level performance in face verification. *IEEE CVPR*.
- [6] Sun, Y., Wang, X., & Tang, X. (2014). Deep learning face representation from predicting 10,000 classes. *IEEE CVPR*.
- [7] Silver, D., et al. (2016). Mastering the game of Go with deep neural networks and tree search. *Nature*, 529(7587), 484.
- [8] Gatys, L. A., Ecker, A. S., & Bethge, M. (2015). A neural algorithm of artistic style. *arXiv:1508.06576*.
- [9] Zeiler, M. D., & Fergus, R. (2014). Visualizing and understanding convolutional networks. *ECCV*.
- [10] Zhu, J. Y., et al. (2017). Unpaired image-to-image translation using cycle-consistent adversarial networks. *ICCV*.
- [11] He, K., et al. (2016). Deep residual learning for image recognition. *IEEE CVPR*.
- [12] Sutskever, I., Vinyals, O., & Le, Q. V. (2014). Sequence to sequence learning with neural networks. *NeurIPS*.
- [13] Hochreiter, S., & Schmidhuber, J. (1997). Long short-term memory. *Neural Computation*, 9(8), 1735–1780.
- [14] Mikolov, T., et al. (2013). Distributed representations of words and phrases and their compositionality. *NeurIPS*.
- [15] Schwalbe, U., & Walker, P. (2001). Zermelo and the early history of game theory. *Games and Economic Behavior*, 34(1), 123–137.
- [16] Zhang, H., et al. (2019). CityFlow: A Multi-Agent Reinforcement Learning Environment for Large Scale City Traffic Scenario. *WWW Conference*.
- [17] Zaidi, A. A., Kulcsar, B., & Wymeersch, H. (2016). Back-pressure traffic signal control with fixed and adaptive routing for urban vehicular networks. *IEEE T-ITS*, 17(8), 2134–2143.
- [18] Liang, X., et al. (2018). Deep reinforcement learning for traffic light control in vehicular networks. *arXiv:1803.11115*.
- [19] Mnih, V., et al. (2015). Human-level control through deep reinforcement learning. *Nature*, 518(7540), 529.
- [20] Wei, H., et al. (2018). Intellilight: A reinforcement learning approach for intelligent traffic light control. *ACM SIGKDD*.

联系电话：18183299177  
联系邮箱：ugcn@qq.com  
官方网站：www.jiaokey.com  

[PRE0]

微信：