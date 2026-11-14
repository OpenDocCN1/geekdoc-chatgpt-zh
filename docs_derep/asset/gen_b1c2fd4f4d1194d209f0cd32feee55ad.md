[PRE0]

图 1-11 生成对抗网络示意图

##### 6. 深度信念网络

深度信念网络（DBN）属于概率生成模型范畴，其架构融合了 Sigmoid 信念网络（SBN）与受限玻尔兹曼机（RBM）：顶层由两层 RBM 构建，底层则为 SBN 结构（如图 1-12a 所示）。DBN 采用逐层贪婪策略训练，因底层为 SBN，需待下层训练收敛后再向上逐层推进，此机制有效化解了深度神经网络的优化难题。其核心优势在于利用预训练机制，规避了前馈神经网络因参数随机初始化易陷入局部最优的弊端。第 6 章将对 DBN 展开深度剖析。

##### 7. 深度玻尔兹曼机

深度玻尔兹曼机（DBM）常与 DBN 并提，二者渊源颇深，均以 RBM 为基石，却在架构细节上截然不同（见图 1-12b）。对比可见：DBN 仅顶层为 RBM，其余为 SBN；而 DBM 全层级均由 RBM 堆叠而成。这种结构差异直接导致两者训练范式的分野。

[PRE1]

图 1-12 深度信念网络与深度玻尔兹曼机

DBM 的主流训练范式为“逐层贪婪预训练 + 监督微调”。预训练阶段，各层作为独立 RBM 单独优化：可见层建模输入数据，后续隐藏层则建模前一层 RBM 的后验分布样本。待所有 RBM 训练就绪，即可组装成完整的 DBM。第 6 章将详细阐释 DBM 细节。

##### 8. 深度自编码器

自编码器（AE）由编码器与解码器两大模块组成：编码器将原始输入压缩为紧凑表示，解码器再由该表示重构输入。深度自编码器（DAE）则由两个对称的 DBN 构建，编码与解码端各采用 4~5 个隐藏层（如图 1-13 所示）。第 6 章将继续深入讲解 DAE。

[PRE2]

### 1.4 深度学习主要应用版图

深度学习极大推动了 AI 进程，备受学术与产业界瞩目。计算机视觉、语音处理、自然语言处理（NLP）是其三大核心战场，而在多模态领域，应用版图亦持续拓展。

#### 1.4.1 计算机视觉：从感知到理解

2012 年 AlexNet 于图像分类任务取得突破，标志着以 CNN 与 Transformer 为代表的深度算法在视觉任务全面开花。应用可划分为“基础任务”与“高级任务”两大梯队：前者夯实通用能力，后者面向具体落地，常依赖基础任务支撑。

**基础任务矩阵：**
*   **图像分类**：全图语义判别，如手写数字识别、猫狗分类。
*   **目标检测**：定位物体位置并判别类别，如行人、车辆检测。
*   **图像分割**：按边缘分离物体并识别，细分为像素级分类的语义分割、与实例级区分的实例分割（如街景、细胞分割）。
*   **图像回归**：预测关键坐标，如人脸/人体关键点定位。

**高级任务图谱：**
*   **人脸识别**：检测正脸 -> CNN 提特征 -> 身份认证。
*   **行人重识别 (Re-ID)**：检测行人 -> 提取穿着、骨架、步态特征 -> 跨镜头身份关联。
*   **目标跟踪**：融合检测与关联算法，实现视频流中目标的持续定位。
*   **动作识别**：解析视频行为，主流路线为双流法与关键点法。
*   **工业缺陷检测**：识别紧固件缺失、建筑病害、金属表面瑕疵等。
*   *……更多场景不胜枚举。*

#### 1.4.2 语音处理：精准与自然的双重飞跃

深度学习在语音领域成果丰硕，识别准确率等核心指标大幅超越传统技术，应用聚焦三大支柱：语音识别、声纹识别、语音合成。

**语音识别 (ASR) 流水线：** 训练集预处理/特征提取 -> 训练声学模型 -> 语料训练语言模型（捕捉词序关系） -> 测试集打分（声学+语言联合解码） -> 输出最高分词序列。

**声纹识别 (说话人识别) 流程：** 训练集建模声纹模型 -> 测试集特征提取 -> 模型分类决策 -> 输出身份结果。

**语音合成 (TTS) 现代范式：**
1.  **文本前端**：语种判断、分词、转音素、韵律预测（停顿、重轻音）。
2.  **声学建模**：摒弃传统波形拼接的生硬感，**端到端深度模型**已成主流，文本输入，语音波形直接输出。

#### 1.4.3 自然语言处理：从统计到大模型时代

2010 年 RNN 语言模型问世，LSTM 与 Transformer 接力推动 NLP 任务全面突围。同视觉一样，任务分基础与高级两层。

**基础任务（语言理解基石）：**
*   **词法分析**：词级处理（词性标注、纠错等）。
*   **句法分析**：句级结构解析（成分/依存句法）。
*   **语义分析**：挖掘深层含义（词/句/篇章三级）。
*   **信息抽取**：非结构化转结构化（实体、事件抽取）。
*   **语言模型**：基于上下文预测下一词。

**高级任务（场景化落地）：**
*   **情感分析**：识别文本倾向（正负/多维度）。
*   **神经机器翻译 (NMT)**：基于神经网络的多语互译。
*   **自动摘要**：单/多文档压缩生成摘要。
*   **机器阅读理解 (MRC)**：读文本答题/完形填空/选择题。
*   **自动问答**：单轮交互答疑。
*   **人机对话**：多轮自由对话，依赖海量语料训练。
*   *……应用边界持续外拓。*

#### 1.4.4 多模态处理：打破单一模态壁垒

图像、语音、文本均为单模态，现实场景多为多模态融合。深度学习在图文互生、视觉问答、虚拟数字人等领域大显身手：

*   **图像描述 (Captioning)**：看图生成语句，即“看图说话”。
*   **可视问答 (VQA)**：针对图像/视频内容回答提问。
*   **图像生成**：文本驱动生成对应图像 (Text-to-Image)。
*   **视频生成**：文本驱动生成视频片段 (Text-to-Video)。
*   **虚拟主播/数字人**：自动化新闻播报等交互场景。
*   *……多模态融合正重塑人机交互范式。*

### 1.5 本书架构导览

全书系统覆盖深度学习核心概念、典型算法与应用、正则化优化、主流框架，脉络如图 1-14 所示：

*   **第 1 章 引言**：溯源发展、理清 AI/ML/DL 关系、奠基核心概念与算法、纵览应用版图。
*   **第 2 章 卷积神经网络 (CNN)**：溯源、基础结构、训练技巧、经典架构、视觉应用。
*   **第 3 章 循环神经网络 (RNN)**：溯源、结构与训练、LSTM 核心、变体家族、序列应用。
*   **第 4 章 Transformer**：注意力机制、架构详解、预训练巨头 (GPT/BERT)、广泛应用。
*   **第 5 章 生成对抗网络 (GAN)**：起因、原理博弈、优化改进、生成式应用。
*   **第 6 章 深度生成模型**：聚焦 DBN、DBM、DAE、扩散模型，兼溯源 Hopfield、玻尔兹曼机、RBM、AE 及变种。
*   **第 7 章 正则化与优化**：参数范数、数据增强、Bagging、早停、Dropout、归一化等正则手段；梯度下降、动量法等优化算法。
*   **第 8 章 深度学习框架**：全景扫描，深度实战 TensorFlow、PyTorch、飞桨三大主流框架。

[PRE3]

图 1-14 本书的组织结构

### 复习题

1.  简述深度学习的发展脉络。
2.  阐释深度学习、机器学习、人工智能三者关系。
3.  对比浅层学习与深度学习核心差异，并举例。
4.  列举典型深度学习算法及其核心功用。
5.  列举深度学习典型应用案例。

### 参考文献

- [1] HINTON G E, OSINDERO S and TEH Y. A fast learning algorithm for deep belief nets [J]. Neural computation, 2006, 18: 1527-1554.
- [2] HINTON G E, SALAKHUTDINOV R R. Reducing the dimensionality of data with neural networks [J]. Science, 2006, 313 (5786): 504-507.
- [3] MCCULLOCH W S, PITTS W. A logical calculus of the ideas immanent in nervous activity [J]. Bulletin of mathematical biophysics, 1943, 5: 115-133.
- [4] HEBB D O. The organization of behavior [M]. New York: Wiley & Sons, 1949.
- [5] ROSENBLATT F. The perceptron: a probabilistic model for information storage and organization in the Brain [J]. Psychological review, 1958, 65 (6): 386-408.
- [6] MINSKY M L, PAPERT S A. Perceptrons: an introduction to computational geometry [M]. Cambridge, MIT Press, 1969.
- [7] FUKUSHIMA K. Neocognitron: a self organizing neural network model for a mechanism of pattern recognition unaffected by shift in position [J]. Biological cybernetics, 1980, 36 (4): 193-202.
- [8] HOPFIELD J J. Neural networks and physical systems with emergent collective computational abilities [J]. Proceedings of the national academy of sciences of the USA, 1982, 79 (8): 2554-2558.
- [9] RUMELHART D E, HINTON G E, WILLIAMS R J. Learning representations by back-propagating errors [J]. Nature, 1986, 323: 533-536.
- [10] JORDAN M I. Serial order: a parallel distributed processing approach [R]. Report 8604, institute for cognitive science, University of California, San Diego, 1986.
- [11] ELMAN J L. Finding structure in time [R]. CRL technical report 8801, center for research in language, University of California, San Diego, 1988.
- [12] CORTES C, VAPNIK V. Support-vector networks [J]. Machine learning, 1995, 20: 273-297.
- [13] HOCHREITER S, SCHMIDHUBER J. Long short-term memory [J]. Neural computation, 1997, 9 (8): 1735-1780.
- [14] LECUN Y, BOTTOU L, BENGIO Y, et al. Gradient-based learning applied to document recognition [J]. Proceedings of the IEEE, 1998, 86 (11): 2278-2324.
- [15] SEIDE F, LI G, YU D. Conversational speech transcription using context-dependent deep neural networks [C]. Proceedings of the 29th International Conference on Machine Learning, 2012: 1-2.
- [16] KRIZHEVSKY A, SUTSKEVER I, HINTON G E. ImageNet classification with deep convolutional neural networks [C]. Advances in Neural Information Processing Systems 25, 2012: 1097-1105.
- [17] SIMONYAN K, ZISSERMAN A. Very deep convolutional networks for large-scale image recognition [C]. Proceedings of the 3rd International Conference on Learning Representations, 2015.
- [18] SZEGEDY C, LIU W, JIA Y, et al. Going deeper with convolutions [C]. Proceedings of IEEE Conference on Computer Vision and Pattern Recognition, 2015: 1-9.
- [19] HE K, ZHANG X, REN S, et al. Deep residual learning for image recognition [C]. Proceedings of IEEE Conference on Computer Vision and Pattern Recognition, 2016: 770-778.
- [20] HU J, SHEN L, SUN G. Squeeze-and-excitation networks [C]. Proceedings of IEEE Conference on Computer Vision and Pattern Recognition, 2018: 7132-7141.
- [21] GIRSHICK R, DONAHUE J, DARRELL T, et al. Rich feature hierarchies for accurate object detection and semantic segmentation [C]. Proceedings of IEEE Conference on Computer Vision and Pattern Recognition, 2014: 580-587.
- [22] GIRSHICK R. Fast R-CNN [C]. Proceedings of International Conference on Computer Vision, 2015: 1440-1448.