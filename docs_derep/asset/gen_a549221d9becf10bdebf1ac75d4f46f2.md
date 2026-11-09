- 全网爆发：CLIP 对比预训练让图文联合的图像合成登上风口。
- 再度走红：大模型微调方案 DreamBooth、LoRA、ControlNet 让个性化训练成为可能。
- 绘画平权：多家厂商推出开箱即用的图像生成服务，AI 作画走进大众。

# 扩散模型：原理与实战全景

## 1.2 发展脉络全解析

##### 1.2.1 起源与奠基：DDPM 及早期改进
在图像合成领域，最早被验证的扩散架构是 2020 年提出的 DDPM。它首次把去噪扩散概率模型引入图像生成，搭建起后续研究的通用底座，涵盖了扩散过程的定义、噪声分布的假设、马尔可夫链的推导、随机微分方程的求解以及损失函数的表述等核心要素。此后涌现的大量变体，基本都是在这一框架上进行针对性的优化。

##### 1.2.2 提速关键：采样器的演进
扩散模型在画质上取得进展的同时，生成效率始终是短板。早期实现往往需要上千次迭代，单张图像耗时可达数分钟，采样效率成为争议焦点。生成速度与质量在很大程度上取决于采样器的设计，如何在不牺牲效果的前提下压缩步数，是推动扩散模型普及的核心课题。

论文《Score-Based Generative Modeling through Stochastic Differential Equations》指出 DDPM 的采样本质上是更一般的随机微分方程求解问题。只要采用更高效的离散化策略，就能把原本 1000 步的流程压到 50 步、20 步甚至更少，显著提升成图效率，如图1-3所示。围绕快速采样，已出现 Euler、SDE、DPM-Solver++、Karras 等一系列求解器，这些加速手段对扩散模型走向主流起到了关键作用。

[PRE0]

[PRE1]

### 图1-3 DPM-Solver++ 用20步实现“一碗水果”到“一碗梨”的编辑演示

> 1 Ho J, Jain A, and Abbeel P. Denoising Diffusion Probabilistic Models [J]. Advances in Neural Information Processing Systems, 2020, pp.6840-6851.

##### 1.2.3 突破GAN：显式分类器引导的爆发
2021 年 5 月前，扩散模型虽已进入图像生成赛道，却始终难敌统治多年的 GAN。真正的转折点是论文《Diffusion Models Beat GANs on Image Synthesis》的发表。OpenAI 在文中系统性地展示了如何利用显式分类器对扩散过程进行引导。

该研究直接击败了长期占据榜首的 GAN，用更稳定、更逼真的样本证明了扩散模型的潜力，使其迅速成为学术圈和产业界关注的焦点，如图1-4所示。

[PRE2]

图1-4 扩散模型与GAN生成质量对比，左侧为BigGAN-deep结果，右侧为OpenAI扩散模型结果

##### 1.2.4 文本即画布：CLIP 点燃多模态生成
CLIP 的作用是把文本与图像映射到同一语义空间，例如“一个苹果”文字和“一个苹果”图像能够被拉到相近位置。CLIP 与扩散模型结合后，文本驱动的图像合成迎来爆发。代表作包括 OpenAI 的 GLIDE、DALL-E、DALL-E 2，其中 DALL-E 2 的样例如图1-5所示，以及 Google 的 Imagen，还有开源社区的 Stable Diffusion，Stable Diffusion v2 的项目主页如图1-6所示。各类文生图模型层出不穷，持续刷新想象空间。

[PRE3]

[PRE4]

##### 1.2.5 个性化微调：DreamBooth、LoRA 与 ControlNet
扩散模型走向大模型后，从零训练的成本变得极高，个人研究者难以负担。好在像 Stable Diffusion 这类开源基座已经具备强大的通用生成知识，再训练并非必要。于是围绕“在已有模型上二次学习”的技术快速兴起，让消费级显卡也能完成定制化训练。DreamBooth、LoRA、ControlNet 是三种典型路径，各自对应不同需求。

DreamBooth 可把现有模型微调以记忆特定主体。用少量样本把主体绑定到专属文本标识后，就能通过提示词驱动该主体在不同场景中生成，如图1-7所示。

LoRA 擅长在保留原模型能力的同时注入特定风格或人物，并能与原生成流程融合。Hugging Face 提供了可视化的 LoRA 训练界面，如图1-8所示。

[PRE5]

图1-8 Hugging Face 提供的 LoRA 训练演示界面

ControlNet 则扩展了可控制的模态，利用分割图、边缘图等条件实现更精细的布局控制。第7章将对 ControlNet 作更深入讲解。

##### 1.2.6 产业落地：商业级绘画工具遍地开花
随着技术成熟、关注度提升以及使用门槛下降，扩散模型在网络上呈现百花齐放的景象。大量成熟的商业解决方案相继出现。Midjourney 通过 Discord 频道支持提示词生成并允许用户社群交流，其主页如图1-9所示。Stability AI 推出的 DreamStudio 如图1-10所示，既支持提示词编辑，也提供 SDK 嵌入和 Photoshop 插件。Adobe 自家的扩散编辑套件 Firefly 如图1-11所示，可结合传统选区实现精细控制。

[PRE6]

[PRE7]

### Meet Adobe Firefly.

Document, transform, and make an infinite range of creations with the fly, a family of creative generative AI models coming to Adobe products

[PRE8]

### 图1-11 Adobe 推出的图像编辑工具库 Firefly

百度发布的文心一格创作平台如图1-12所示，阿里巴巴达摩院也推出通义文生图大模型。除头部企业外，创业公司同样活跃。退格网络的 Tiamat 工具已获多轮融资，其生成的概念场景登上上海地铁广告。北京毛线球科技的 6pen Art APP 如图1-13所示，把 AI 作画带到移动端。

[PRE9]

### 图1-12 百度文心一格 AI 创作平台

[PRE10]

### 图1-13 6pen Art 图像生成移动应用

##### 1.2.7 大众化创作时代到来
众多服务商正以更成熟、更简便的方式，让用户仅靠文字或图片输入即可获得所需图像，AI 作画真正进入大众视野。

### 1.3 应用版图拓展
扩散只是一种思路，并非固定网络结构。把扩散思想迁移到其他领域，同样能发挥价值。

在实践中，最常见也最成熟的仍是图像生成，本书亦聚焦于此。但扩散模型在其他方向的探索同样值得关注，有望在未来迎来爆发。本节梳理如下方向：

- 计算机视觉。
- 时序数据预测。
- 自然语言。
- 文本驱动的多模态。
- AI 基础科学。

#### 1.3.1 计算机视觉
这里聚焦 2D 图像相关任务。图像处理研究历史悠久，扩散方法在多项任务中展现出优势：

- 图像分割与目标检测。分割和检测在自动驾驶、质检等场景至关重要。引入扩散后可获得更准确结果。Meta AI 的 SegDiff 能生成分割 Mask 如图1-14所示，DiffusionDet 可从随机矩形框逐步演化为检测框如 图1-15所示。不过速度仍是制约实时应用的因素。

[PRE11]

[PRE12]

### 图1-14 SegDiff 生成的分割掩码图

### 图1-15 DiffusionDet 生成的检测框演化示例

- 图像超分辨率。超分辨率旨在把低分辨图像重建为高分辨且结构连贯的结果。级联扩散模型 CDM 通过串联多级扩散网络逐步放大分辨率，实现高质量超分，图1-16 给出示例。

[PRE13]

### 图1-16 CDM 实现的图像超分辨率示例

> 1 Jonathan Ho, Chitwan Saharia, William Chan, David J Fleet, Mohammad Norouzi, and Tim Salimans. Cascaded Diffusion Models for High Fidelity Image Generation. J. Mach. Learn. Res. 23(2022), 47–1.

- 图像修复、图像翻译和图像编辑。这类任务涉及对局部或整体区域的操作，如补全、风格迁移、内容替换。Palette 统一了修复、翻译、编辑等功能于单一扩散模型中¹。图1-17 展示了 Palette 的修复效果。

[PRE14]

图1-17 Palette 对图像缺损区域的修复示例

#### 1.3.2 时序数据预测
时序预测的目标是基于历史观测推断未来走势，如气温、股价、销售预测等，本质上是一种条件生成任务，扩散模型能够胜任。

TimeGrad² 是首个将扩散思想引入多元概率时序自回归预测的模型。它先用 RNN 编码历史序列到隐空间，再对历史数据施加噪声实现扩散，最后在高维时序上完成预测。图1-18 展示了其在城市交通流量预测中的表现。

> 1 Chitwan Saharia, William Chan, Huiwen Chang, Chris Lee, Jonathan Ho, Tim Salimans, David Fleet, and Mohammad Norouzi. Palette: Image-to-Image Diffusion Models. In Special Interest Group on Computer Graphics and Interactive Techniques Conference Proceedings. pp.1-10, 2022.

> 2 Kashif Rasul, Calvin Seward, Ingmar Schuster, and Roland Vollgraf. Autoregressive Denoising Diffusion Models for Multivariate Probabilistic Time Series Forecasting. In International Conference on Machine Learning. pp.8857-8868, 2021.

图1-18 TimeGrad 在城市交通流量预测任务中的预测结果

传统方法长期由机器学习和 RNN 体系主导，而扩散模型已显现出强劲潜力。

#### 1.3.3 自然语言
自然语言是人工智能重要分支，近期爆火的 ChatGPT 即是典型问答生成系统。

扩散模型同样可用于语言生成。对句子分词并转为向量后，可通过扩散过程学习语句分布，进而支持翻译、对话、补全、情感分析、续写等任务。

Diffusion-LM¹ 是首个将扩散机制引入自然语言的模型，旨在解决连续扩散与离散文本之间的适配问题，实现高可控性的语言生成。测试显示其在 6 项可控文本生成任务上表现优异，如表1-1所示。

### 表 1-1 Diffusion-LM 在 6 种可控文本生成任务中的生成效果

| 输入类型与输出文本       | 具体内容                                                                                     |

|--------------------------|---------------------------------------------------------------------------------------------|

| 输入（语义内容）         | food : Japanese                                                                             |

| 输出文本                 | Browns Cambridge is good for Japanese food and also children friendly near The Sorrento.    |

| 输入（词性）             | PROPN AUX DET ADJ NOUN NOUN VERB ADP DET NOUN ADP DET NOUN PUNCT                             |

| 输出文本                 | Zizzi is a local coffee shop located on the outskirts of the city.                          |

| 输入（语法树）           | (TOP (S (*)(*)(*)) (VP(*) (NP(NP(*) 0))))                                                    |

| 输出文本                 | The Twenty Two has great food                                                              |

| 输入（语法范围）          | (7,10, VP)                                                                                  |

| 输出文本                 | Wildwood pub serves multicultural dishes and is ranked 3 stars                              |