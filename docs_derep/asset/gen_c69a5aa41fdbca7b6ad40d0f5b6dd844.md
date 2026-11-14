- [19] ZHANG Z, WU Y, ZHOU J, et al. SG-Net: syntax-guided machine reading comprehension [C]. Proceedings of the AAAI Conference on Artificial Intelligence, 2020: 9636-9643.

- [20] CARION N, MASSA F, SYNAAEVE G, et al. End-to-end object detection with transformers [C]. Proceedings of the 16th European Conference on Computer Vision, 2020: 213-229.

- [21] GIRSHICK R. Fast R-CNN [C]. Proceedings of the IEEE International Conference on Computer Vision, 2015: 1440-1448.

- [22] REDMON J, DIVVALA S, GIRSHICK R, et al. You only look once: unified, real-time object detection [C]. Proceedings of IEEE Conference on Computer Vision and Pattern Recognition, 2016: 779-788.

- [23] CHANG W C, YU H F, ZHONG K, et al. Taming pretrained transformers for extreme multi-label text classification [C]. Proceedings of the 26th ACM SIGKDD International Conference on Knowledge Discovery & Data Mining, 2020: 3163-3171.

- [24] CORNIA M, STEFANINI M, BARALDI L, et al. Meshed-memory transformer for image captioning [C]. Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition, 2020: 10578-10587.

- [25] ZHANG X, SUN X, LUO Y, et al. RSTNET: captioning with adaptive attention on visual and non-visual words [C]. Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition, 2021: 15465-15474.

- [26] LUO Y, JI J, SUN X, et al. Dual-level collaborative transformer for image captioning [C]. Proceedings of the AAAI Conference on Artificial Intelligence, 2021: 2286-2293.

- [27] WANG Y, XU J, SUN Y. End-to-end transformer based model for image captioning [C]. Proceedings of the AAAI Conference on Artificial Intelligence, 2022: 2585-2594.

- [28] ZENG P, ZHU J, SONG J, et al. Progressive tree-structured prototype network for end-to-end image captioning [C]. Proceedings of the 30th ACM International Conference on Multimedia, 2022: 5210-5218.

- [29] FANG Z, WANG J, HU X, et al. Injecting semantic concepts into end-to-end image captioning [C]. Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition, 2022: 18009-18019.

### 本章人物：Yoshua Bengio 教授

[PRE0]

提到深度学习的“三巨头”，Yoshua Bengio 教授绝对是绕不开的名字。这位 1964 年出生的加拿大计算机科学家，现任蒙特利尔大学教授，同时也是英国皇家学会与加拿大皇家学会的双料院士。2018 年，他因在深度学习领域的奠基性贡献，与 Hinton、LeCun 共同斩获图灵奖——这被视为计算机界的“诺贝尔奖”。除此之外，他还是全球知名的 Mila 实验室创始人兼科学总监，IVADO 实验室科学总监，并荣获 2019 年 IEEE 计算智能分会神经网络先锋奖等诸多殊荣。

Bengio 教授的学术生涯起步于麦吉尔大学， dort 先后拿下计算机工程学士（1986）、计算机科学硕士（1988）及博士学位（1991）。博士毕业后，他曾在麻省理工学院（MIT）与贝尔实验室从事博士后研究，于 1993 年入职蒙特利尔大学，2002 年晋升为正教授。

其核心学术贡献堪称深度学习发展的基石，主要体现在五大方面：
1. **注意力机制的奠基者**：联合 Dzmitry Bahdanau 等人首创注意力机制，在神经机器翻译中大放异彩，直接为后来 Transformer 架构的诞生铺平道路。
2. **神经语言模型的开拓者**：基于 N-gram 统计语言模型思想，提出前馈神经网络语言模型，开启了统计语言建模新纪元，后续的 RNN 语言模型与 Transformer 语言模型皆沿袭此脉络。
3. **GAN 的推手之一**：携手 Ian Goodfellow 提出生成对抗网络（GAN），瞬间点燃了生成式 AI 的研究热潮。
4. **深度学习框架先驱**：主导开发早期框架 Theano，虽已停止维护，却深刻影响了 TensorFlow、PyTorch 等后起之秀的设计范式。
5. **正则化利器发明者**：与 Pascal Vincent 等人共同提出降噪自编码器，有效缓解了原始数据训练中的过拟合难题。

值得一提的是，前文介绍 Yann LeCun 时提到，两人合力创办了表示学习领域的顶级盛会 ICLR（International Conference on Learning Representations），至今已成功举办十余届，稳居 AI 领域顶会之列。

想了解更多动态？可访问其个人主页：https://yoshuabengio.org/。

## 生成对抗网络（GAN）深度解析

在机器学习的版图里，**生成模型**与**判别模型**是两大经典流派。生成模型致力于学习联合概率分布 $P(X,Y)$，核心任务是“造数据”——生成符合真实分布的新样本，主导无监督学习领域，擅长在无标签环境下捕捉数据的高阶关联。判别模型则直奔主题，学习决策函数 $f(X)$ 或条件概率 $P(Y|X)$，专注于“分类别”——给定输入 $X$ 预测输出 $Y$，是有监督学习的主力军。

有监督学习往往因标签加持而效果更优，但其阿喀琉斯之踵在于极度依赖海量标注数据；放眼长远，无监督学习潜力更大。然而，支撑无监督学习的生成模型面临两大拦路虎：一是建模真实世界需要大量先验知识，建模质量直接定生模型上下限；二是真实数据分布极其复杂，拟合所需算力往往高得惊人。

为破局，学界涌现出一批深度生成模型：生成对抗网络（GAN）[1]、深度信念网络、深度玻尔兹曼机、深度自编码器、扩散模型等。鉴于 GAN 应用最广、影响最深，本章将单独拆解；其他模型我们留到第 6 章再细说。

### 5.1 GAN 核心原理揭秘

#### 5.1.1 零和博弈：对抗的数学本质

GAN 最初为生成逼真图像而生，其设计灵感直取博弈论中的**零和博弈**（Zero-Sum Game）。简单说，双方利益此消彼长，一方赢多少，另一方就输多少，总收益恒为零。

拿大伙儿熟悉的“石头剪刀布”举例（如图 5-1）。单局必分胜负或平，赢 +1 分，输 -1 分，平 0 分。若甲赢 $N$ 局、输 $M$ 局、平 $P$ 局，则乙必输 $N$ 局、赢 $M$ 局、平 $P$ 局。总分相加：$N - M + M - N = 0$，妥妥的零和博弈。图 5-1 还给出了对应的收益矩阵直观展示。

| | ✊ | ✌️ | ✋ |
|---|---|---|---|
| ✊ | 0, 0 | 1, -1 | -1, 1 |
| ✌️ | -1, 1 | 0, 0 | 1, -1 |
| ✋ | 1, -1 | -1, 1 | 0, 0 |

图 5-1 石头剪刀布收益矩阵示意

[PRE1]

#### 5.1.2 双塔架构：生成器与判别器

GAN 由两大核心模块构成：**生成器（Generator, G）**与**判别器（Discriminator, D）**，分工明确、各司其职。G 的使命是“以假乱真”，从噪声中生成逼近真实分布的样本；D 的使命是“火眼金睛”，拼命区分真样本与 G 造的假样本。双方在对抗博弈中互相逼迫、共同进化，最终让 G 掌握生成高质量样本的能力。

以图像生成为例（如图 5-2），G 是一个图片生成网络，输入服从均匀或高斯分布的随机噪声 $z$，输出一张类真实训练图 $G(z)$，追求“越像越好”。D 是一个二分类器，输入真实图 $x$ 与生成图 $G(z)$，输出“输入为真实图”的概率。真图进则输出大概率，假图进则输出小概率。

[PRE2]

图 5-2 GAN 基本架构示意

#### 5.1.3 目标函数：极大极小博弈

GAN 的训练目标直白：让 G 产出的数据骗过 D。为此定义目标函数，使 D 判真样本为“真”、假样本为“假”的概率最大化（即差异程度最大化）。

[PRE3]

式中，$V(D, G)$ 量化真假样本差异；$p_{data}(x)$ 为真实数据分布，$p_z(z)$ 为噪声分布；$D(x)$ 为 D 判 $x$ 为真的概率，$D(G(z))$ 为 D 判生成样本为真的概率。

训练时，双方目标截然相反：
- **判别器 D**：想**最大化** $V(D, G)$，即固定 G，拼命提高辨别准确率。写成损失函数形式即：
[PRE4]
- **生成器 G**：想**最小化** $V(D, G)$，即迷惑 D，降低其判断正确率。若严格遵循零和博弈，G 最小化 $V(D, G)$；但实战发现这效果不佳，通常改用最小化以下形式（非饱和损失）：
[PRE5]

#### 5.1.4 训练实战：三步走策略

GAN 训练是个动态博弈过程（如图 5-3），核心分三步[2]：
1. **喂真样本练 D**：输入真实数据 $x$，前向传播，目标输出 1（真），反向传播更新 D 参数。
2. **喂假样本练 D**：输入生成数据 $G(z)$，前向传播，目标输出 0（假），反向传播再更新 D 参数。
3. **练 G 骗 D**：输入 $G(z)$，冻结 D 参数，前向传播，目标输出 1（骗过 D 判为真），反向传播更新 G 参数。

双方你方唱罢我登场，拼命优化各自网络，直至达成**动态纳什均衡**——此时生成分布无限逼近真实分布，D 彻底“眼瞎”，真假样本输出概率均为 50%。

工程上常用随机梯度上升/下降优化。判别器每轮迭代步数 $k$ 是超参数，文献 [1] 采用 $k=1$（成本最低）。优化器可用普通 SGD 或动量法（详见 7.8.2 节），文献 [1] 采用动量法。完整训练伪代码如下：

[PRE6]

图 5-3 基于文献 [2] 复现的训练流程图

### 5.2 GAN 优化与进阶：攻克训练难题

GAN 虽强，但“调教”极难。双模型对抗训练极易陷入**训练不稳定**、**模式崩溃**（Mode Collapse，生成样本单一）等坑。为此，研究者从三大维度祭出优化大招：**条件约束优化**、**迭代式生成优化**、**架构结构优化**。

#### 5.2.1 条件约束优化：给生成加“导航”

原始 GAN 的 G 只管“像不像”，不管“生啥”。引入条件约束（如类别标签、属性标签），能让 G 生成指定类别、具备特定属性的样本，大幅提升可控性与多样性。此时 G 的目标变为：在对抗中找到满足约束条件的最优解。代表作有 **CGAN**[3] 与 **InfoGAN**[4]，下面逐一拆解。

##### 1. CGAN：有条件的生成对抗网络

CGAN[3] 核心思想：**可控生成，降低随机性**。如图 5-4，G 额外接收条件向量 $y$（通常是类别标签），任务变为“按图索骥”生成符合 $y$ 的图像；D 同步接收 $y$，判断“给定 $y$ 下，这图是真还是假”。

[PRE7]

图 5-4 CGAN 架构示意

目标函数引入条件 $y$：

[PRE8]

其中，$E_{x \sim p_{data}(x)}[\log D(x|y)]$ 为给定 $y$ 时真样本被判为真的期望；$E_{z \sim p_z(z)}[\log (1 - D(G(z|y)))]$ 为给定 $y$ 与噪声 $z$ 时生成样本被判为假的期望。

实战中，CGAN 可定向生成带属性图像。如手写数字实验（图 5-5），指定类别 0~9，每行生成对应数字，效果清晰可辨。

[PRE9]

图 5-5 CGAN 生成手写数字（每行一类）

##### 2. InfoGAN：挖掘潜在语义