[PRE0]

[PRE1]

[PRE2]

把换脸拆开看，技术路线大致分两支，一条是人脸融合，一条是人脸生成。两者都会改写目标人脸的纹理细节，只是操作方法不一样。融合派多靠图形学篡改的思路，先用 3D 建模和图像处理把源人脸贴合到目标人脸上；生成派则多借助深度学习直接重建目标人脸。

FaceSwap¹是典型的融合路线代表，它先定位人脸关键点，

> ① FaceSwap: 引自 GitHub 中的 FaceSwap 项目，2021/06/05.

# AI 安全：技术与实战

图 7.8 展示了一种在 FaceSwap 基础上改进的融合做法，为让最终贴合更顺滑，该方案先用面部分割把脸部区域抠出来，只在分割后的区域执行融合。Nirkin 等人①借鉴分割思路，先用网络把人脸分割出来估计出 3D 人脸形状，再把源脸和目标脸这两个已对齐的 3D 形状进行融合。

[PRE3]

融合类的换脸门槛相对友好，通常一张替换目标的照片就够用了，但在人脸角度变化大的场景下合成质量容易掉线。所以研究者开始把目光投向深度学习驱动的人脸生成，这类方案常见的是自动编码器 Autoencoder 和生成对抗网络 GAN。

Deepfakes²是较早开源的基于自动编码器的换脸网络，整体分成训练阶段和生成阶段两步，如图 7.9 所示。自动编码器由编码器和解码器构成，编码器负责把输入压缩成低维表示，解码器再用这个表示还原出与输入相似的输出。实践中用源人物和目标人物各几百张照片分别训练出识别、还原的能力，最后拿源人物的照片配合目标人物的解码器就能完成转换。Deepfakes 的短板很明显，训练往往需要上百张甚至更多的样本，耗时耗算力。

GAN 在换脸里应用很广，FaceSwap-GAN①是在 Deepfakes 基础上引入 GAN 的版本，加入鉴别器损失，在生成时不断比对生成图与原图的相似度来提升质量。CycleGAN②③利用 GAN 学习两类域之间转换的特点，在没有成对数据的情况下实现跨域图像迁移，可算作换脸的早期尝试，但效果有限。后续的 ReCycleGAN④⑤在 CycleGAN 上叠加了空间信息和视频流的时间信息，并融合了内容转换与风格保留的对抗损失，细节拟合上比 CycleGAN 更贴近目标。GAN 的加入让生成人脸更逼真自然。除换脸外，GAN 也被大量用于合成虚拟人脸和篡改人脸属性，比如 StarGAN⑥、StackGAN⑦、PGAN⑧等。

> ① NIRKIN Y, MASI I, TUAN A T, et al. On face segmentation, face swapping, and face perception[C]//2018 13th IEEE International Conference on Automatic Face & Gesture Recognition (FG 2018). IEEE, 2018: 98-105.

> ② Deepfakes: 引自 GitHub 官网 deepfakes/faceswap 库，2021/06/05.

# AI 安全：技术与实战

# ▶ 训练过程

[PRE4]

# ▶ 换脸过程

[PRE5]

图 7.9 Deepfakes 的训练和生成阶段

视觉可以造假，听觉同样可以。语音伪造大致分三类：文本到语音合成 TTS、语音转换 Voice Conversion、语音克隆 Voice Cloning。TTS 是按指定文本合成带对应内容的语音文件；语音转换是把源人物的音色改成目标人物的音色；语音克隆则是基于少量目标人物的音频学习其音色，再合成带指定内容的语音。几种技术的差异如图 7.10 所示。

# 第7章 AI应用失控风险

[PRE6]

# 图7.10 几种语音伪造技术的区别

随着深度学习成熟，TTS、语音转换、语音克隆都有大幅提升。谷歌提出的 WaveNet¹是首个端到端的语音合成模型，能做出接近人声的音频。类似的 TTS 还有 Tacotron²和 DeepVoice³，后来 Arik 等人和 Ping 等人对 DeepVoice 系列做了改进，分别给出 DeepVoice2①和 DeepVoice3②。DeepVoice2 用低维可训练的说话者编码增强文本到语音的转换，让单模型能输出不同声音；DeepVoice3 则是基于注意力机制的全卷积 TTS 系统，合成速度更快。

图像伪造和语音伪造的思路相通，GAN 也常被用在语音转换里把源说话人声音转成目标说话人。Kaneko 等人③用一种特殊的 CycleGAN 架构做语音转换，这种方法需要事先指定源说话人和目标说话人身份。Kinnunen 等人④受说话人识别中 i-vector 与 PLDA 的启发，打破单一对一的限制，训练一个系统就能处理多个源说话人和目标说话人。自编码器也被引入语音转换，模型包含编码器和解码器，编码器把波形、语谱图、MFCC 序列等表层特征压缩成隐表示，解码器再从隐表示恢复。隐表示里藏着语音内容和说话人身份信息。基于自编码器的语音转换代表工作有论文⑤等。

> ① ARIK S, DIAMOS G, GIBIANSKY A, et al. Deep voice 2: Multi-speaker neural text-to-speech[C]//Advances in neural information processing systems. 2017: 2962-2970.

> ② PING W, PENG K, GIBIANSKY A, et al. Deep voice 3: 2000-speaker neural text-to-speech[J]. Proc. ICLR, 2018: 214-217.

> ③ KANEKO T, KAMEOKA H. Parallel-data-free voice conversion using cycle-consistent adversarial networks[J]. arXiv preprint arXiv:1711.11293, 2017.

> ④ KINNUNEN T, JUVELA L, ALKU P, et al. Non-parallel voice conversion using i-vector PLDA: Towards unifying speaker verification and transformation[C]//2017 IEEE international conference on acoustics, speech and signal processing (ICASSP). IEEE, 2017: 5535-5539.

> ⑤ HSU C C, HWANG H T, WU Y C, et al. Voice conversion from non-parallel corpora using variational auto-encoder [C]//2016 Asia-Pacific Signal and Information Processing Association Annual Summit and Conference (APSIPA). IEEE, 2016: 1-6.

# AI 安全: 技术与实践

与 TTS 和语音转换不同，语音克隆的输入同时包含文本和语音。系统先从输入语音里提取说话人音色特征，再按输入文本控制生成内容，让目标人物“说”任意话。本质上可视为多说话人 TTS。Jia 等人①提出了一个经典系统：语音编码器提取音色特征，文本编码器编码待生成文本，两路编码拼接后送入解码器生成梅尔声谱图，最后由声码器合成最终语音，流程如图 7.11 所示。

[PRE7]

语音生成技术进步后，合成音频不仅能骗过人耳，还能骗过部分自动语音认证系统。语音伪造常与视频换脸同步出现，鉴别难度进一步加大。

前面提到的视觉伪造和语音伪造技术里，很多已开源并走向商业化。FaceSwap、Deepfakes、FaceSwap-GAN 等都在 GitHub 上有开源项目。值得一提的是 DeepFaceLab，由 Iperov 创建的开源深度伪造系统，集成多种换脸算法，并加入图像处理和超分辨率模块来优化结果。只要具备基础深度学习知识，在有 GPU 的设备上就能实现换脸。Deep-Voice-Conversion 和 MelNet 则是开源的语音伪造方案。表 7.1 列出经典开源深度伪造算法。

# 表 7.1 经典开源深度伪造算法

| 伪造类别 | 伪造算法 | 特点 |

| --- | --- | --- |

| 视觉伪造 | FaceSwap | 基于人脸融合的换脸算法 |

| 视觉伪造 | Deepfake | 基于自动编码器的换脸算法 |

| 视觉伪造 | FaceSwap-GAN | 基于GAN的换脸算法 |

| 视觉伪造 | CycleGAN | 基于GAN的换脸算法 |

| 视觉伪造 | ReCycleGAN | 基于GAN的换脸算法 |

| 视觉伪造 | DeepFaceLab | 实现了多种换脸算法, 并加入了图像处理和超分辨率模块来改进最终生成结果 |

| 语音伪造 | Deep-Voice-Conversion | 语音克隆算法 |

| 语音伪造 | MelNet | 基于频谱图的端到端语音生成算法 |

除了开源项目，还有商业化应用。曾经火爆的 ZAO 用一张照片就能换脸，Avatarify 制作的“蚂蚁呀黑”也刷屏。类似 App 还有 FakeApp、FaceApp、去演、IFace、脸优、AI 换脸、ReFace 等。更早引发争议的是 DeepNude，这类生成 App 上传女性图片后自动“脱衣”生成裸照，很快被下架。

# 7.1.2 深度伪造安全风险

深度伪造滥用带来的安全隐患，核心是对 AI 技术失控的担忧。

主要风险有三点。

一是肖像权、名誉权和隐私权受损。随着技术开源、模型增多，门槛不断降低。不法者可能制作虚假色情视频或虚假言论视频，用于诬陷、诽谤、报复，侵犯个人权益并助长不良内容传播。据统计，全网流传的深度伪造合成视频中色情内容占比高达96% ①。

二是诈骗更容易。传统诈骗在深度伪造加持下更猖獗，尤其是语音诈骗。有人用 AI 变声把自己声音调成可爱动听的声音聊天骗取信任；或用语音克隆用少量受害者录音模拟其声音实施电话诈骗。2019 年德国某公司 CEO 就因语音克隆电话诈骗被骗走 220000 欧元。

三是网络谣言加剧，新闻公信力受损。深度伪造可篡改新闻报道的音视频，制造虚假新闻信息，成为谣言生产工具。长期来看会削弱公众对记者和媒体的信任。2018 年特朗普宣布美国退出《巴黎气候协定》后不久，比利时一个政党就制作了一段特朗普呼吁比利时效仿的虚假讲话视频，尽管片尾标注“这不是真的特朗普”，仍在当地引发轩然大波。

> ① A governance framework for algorithmic accountability and transparency[EB/OL].(2019-04-04)[2020-01-18].

# 7.2 AI 应用失控防御方法

面对 AI 失控带来的潜在风险，防范不合理应用需要从数据集、技术防御、内容溯源、行业实践等多角度讨论。

# 7.2.1 数据集

要鉴别伪造视频，首要的是用技术手段建立鉴别模型，而模型的前提是拥有深度伪造数据并构建完备数据集。目前已发布不少数据集，常见的数据集如表 7.2 所示。按内容分，可划分为视觉伪造数据和语音伪造数据。

# 表 7.2 常见的深度伪造数据集

| 数据类型 | 数据集 | 真实视频（语音）数量/伪造视频（语音）数量（个） |

| --- | --- | --- |

| 视觉伪造数据 | UADFV | 49/49 |

| 视觉伪造数据 | Celeb-DF | 590/5639 |

| 视觉伪造数据 | FaceForensics | 1004/1004 |

| 视觉伪造数据 | FaceForensics++ | 1000/1000 |

| 视觉伪造数据 | Dceepfake-TIMIT | 320/640 |

| 视觉伪造数据 | Mesonet data | 11509/8000 |

| 视觉伪造数据 | DFDC | 100129/19025 |

| 视觉伪造数据 | DeepForensics-1.0 | 50000/10000 |

| 视觉伪造数据 | WildDeepfake | 0/1869 |

| 语音伪造数据 | ASVspoof 2015 | 9404/184000 |

| 语音伪造数据 | ASVspoof 2019 | - |

UADFV¹是早期研究常用的数据集之一，包含 49 个来自 YouTube 的真实视频和 49 个由 FakeApp 生成的合成视频，每个视频约 11 秒。其短板是分辨率低、生成质量差、人脸篡改痕迹明显、规模也偏小。