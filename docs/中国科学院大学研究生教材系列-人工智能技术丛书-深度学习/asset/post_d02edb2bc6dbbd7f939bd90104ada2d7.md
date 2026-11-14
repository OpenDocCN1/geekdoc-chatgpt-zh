
共用一个判别器来判断来自真实音乐还是生成器。与第二种方式相比，混合模式更加灵活，可以在生成器模型中使用不同的参数（如层数、卷积核大小等），将音轨的独立生成和全局和谐结合起来，如图 5-20 所示。

![](img/57b180d92f84b0150a5b5e4fc7583b76_178_0.png)

图 5-20 混合模型

### (3) 时序模型

以上三种模型只能逐小节生成多音轨音乐，而小节之间可能没有连贯性。因此需要一个时序模型来生成多小节音乐。MuseGAN 给出了两种时态模型：重头（From Scratch）生成模型与条件音轨（Track-Conditional）生成模型。

如图 5-21 所示，将生成器分为两个子网络：时序生成器 \(G_{temp}\) 和小节生成器 \(G_{bar}\)。\(G_{temp}\) 将一个输入噪声 \(z\) 映射为一个携带了时序信息的隐藏变量序列 \(\hat{z} = \{z^{(1)}, z^{(2)}, \cdots, z^{(t)}\}\)。随后 \(\hat{z}\) 作为输入进入 \(G_{bar}\) 来产生对应的多音轨 Piano-Roll，这个过程可以表示为

\(G(z) = | G_{bar}(G_{temp}(z^{(t)})) |_{t=1}^T \quad (5-23)\)

![](img/57b180d92f84b0150a5b5e4fc7583b76_178_1.png)

图 5-21 小节连贯性（重头生成）模型

如图 5-22 所示，该方法给定一个人工设计音乐小节 y，通过将此作为条件学习该小节的时间结构，并生成剩余完整小节（完成歌曲）。其中 \(G_{bar}^c\) 代表条件小节生成器，有两个输入，分别是 y 以及随机时间噪声 z。为了保证生成小节的质量，额外引入了一个 Encoder 将 y 特征映射到 z 的特征空间上，这个过程可以表示为

```
G^n(z, y) = | G^0_{bar}(z^{(l)}, E(y^{(l)})) |^l_{l=1} (5-24)
```

![](img/57b180d92f84b0150a5b5e4fc7583b76_179_0.png)

图 5-22 小节连贯性（条件音轨）模型

### (4) MuseGAN 模型

将多轨道相互依赖模型和时序模型组合即可得到最终的 MuseGAN 模型，如图 5-23 所示，模型共有 4 个输入：轨道间时序无关随机变量 z、轨道内时序无关随机变量 z_i、轨道间时序相关随机变量 z_l、轨道内时序相关随机变量 z_{i,l,t}。对于每一个音轨 i，共享时序模型 G_{temp,i} 和独立时序模型 G_{temp,i}，分别将 z 和独立的 z_i 作为输入，得到的输出分别包含轨道间和轨道内的时序信息。z 和 z_i 经过时序模型的输出以及 z_l、z_{i,l,t} 共同进入小节生成器 G_{bar} 后产生音乐的小节来堆叠产生 Piano-Roll。这个过程可以表示为

```
G(\vec{z}) = | G_{bar}(z, G_{temp,i}((z_i)^{(l)}), z_l, G_{temp,i,l}((z_{i,l,t})^{(l)})) |^{M,T}_{l,t=1} (5-25)
```

![](img/57b180d92f84b0150a5b5e4fc7583b76_180_0.png)

图 5-23 MuseGAN 模型

图 5-23 根据文献 [18] 重新绘制。

## 5.3.5 异常检测

异常检测（Anomaly Detection）是指识别和分类图像或其他数据中的异常物品、异常行为或异常数据。传统的异常检测通常使用一些基于统计学的方法，如密度估计方法、聚类方法等，但这些方法通常需要足够多的异常先验知识，并且泛化能力容易受到数据分布的影响。GAN 出现后，人们开始使用它进行异常检测，取得了很好的效果。接下来介绍两个代表性工作：AnoGAN[19] 和 GANomaly[20]。

### 1. AnoGAN

AnoGAN 可以自动地检测图像中的异常部分，不需要人工标注或先验知识。AnoGAN 模型使用了一种称作“异常重构”（Anomaly Reconstruction）的方法，通过将随机噪声生成的图像重构成与输入图像相似的图像，然后通过计算两者的误差来检测异常数据，如图 5-24 所示。

![](img/57b180d92f84b0150a5b5e4fc7583b76_181_0.png)

图 5-24 AnoGAN 模型结构图

AnoGAN 包含以下三个组成部分。

- 1) 生成器: AnoGAN 使用生成器来生成与正常数据类似的样本。生成器接受一个随机向量作为输入，并输出生成的样本。通过使用正常数据训练生成器，它可以学习到正常数据的分布，并生成与之相似的样本。
- 2) 判别器: 判别器的作用是区分真实的正常数据与生成器生成的数据。它接受一个样本作为输入，并输出判断该样本是真实样本还是生成样本的概率。通过使用生成样本和真实样本训练判别器，它可以学习辨别生成样本与真实样本的区别。
- 3) 异常重构: 在训练完成后，AnoGAN 使用了一种称为反向映射（Inverse Mapping）的技术来计算一个异常分数，用于衡量测试样本与训练样本之间的差异，分为以下三个步骤：
  - 1) 从随机噪声 z 中生成一系列图像 x'，其中有一张图像最接近输入图像 x。
  - 2) 对于最接近输入图像的生成图像，计算其与输入图像之间的重构误差，误差越小，则生成的图像越接近该输入图像。
  - 3) 通过不断迭代，将重构误差定义为距离度量，找到输入图像周围最近的随机噪声，然后将其作为异常标记区域。

可以发现在测试阶段传入缺陷数据时最终的损失大，传入正常数据时的损失小，这时候就可以设置一个合适的阈值来判断图像是否有异常了。

AnoGAN 的损失函数如下：

Loss = (1-λ) | D(x)-D(G(x)) | +λ(x-G(x)) （5-26）

使用 AnoGAN 进行模型训练和异常检测的流程如图 5-25 所示。首先用正常数据训练获得模型，然后使用该模型对未知数据进行异常检测。

![](img/57b180d92f84b0150a5b5e4fc7583b76_181_1.png)

图 5-25 AnoGAN 模型进行异常检测的流程 [19] （详见彩插）

AnoGAN 模型的优点是不需要先验知识和标注数据，可以自动化地检测异常数据，且具有很好的可扩展性和可重复性。

### 2. GANomaly

GANomaly 在 AnoGAN 的基础上进行了改进，不再比较两个图像的分布，而是比较图像编码的潜在向量，如图 5-26 所示。具体来说，GANomaly 的生成器并没有直接根据随机噪声采样生成数据，而是通过将输入数据编码成潜在向量再解码来生成数据，判别器则用来判断输入数据中哪些是正常的、哪些是异常的。对于正常样本数据，编码、解码再编码得到的潜在向量和第一次编码得到的潜在向量差异不会特别大。但是，使用基于正常样本训练好的编码器、解码器对未知的异常进行处理时，两者的差距往往是比较大的。

![](img/57b180d92f84b0150a5b5e4fc7583b76_182_0.png)

图 5-26 GANomaly 的模型结构

通过将正常数据输入由编码器-解码器组成的生成器，可以得到对应输出空间中的生成数据。GANomaly 的目标是学习从输入空间到输出空间的映射，同时也学习对抗损失。在这个过程中，如果一个输入数据 x 被生成器转换成输出数据 x_hat，并被判别器识别为正常数据，那么该输入就被认为是正常的。反之，如果一个输入数据 x 被转化成输出数据 x_hat，并被判别器识别为异常数据，则该输入数据被认为是异常的。

图 5-27 为 AnoGAN 与 GANomaly 的结构对比，可以直观看出 GANomaly 在 AnoGAN 基础上做出的改进。

![](img/57b180d92f84b0150a5b5e4fc7583b76_182_1.png)

图 5-27 AnoGAN 与 GANomaly 的结构对比

但 GANomaly 与 AnoGAN 在损失函数上有所区别，如下所示：

```
Loss = \omega_1(L_{enc}) + \omega_2(L_{con}) + \omega_3(L_{adv})
```

其中 L_{enc} 指的是图像编码后的特征与图像编码、解码再编码后的特征差异，L_{enc} = ||z - z_hat||_2;

$L_{con}$ 指的是原始图像和经过编码、解码后的生成图像与原始图像之间的差异，$L_{con} = \|x - \hat{x}\|_1$;
$L_{adv}$ 指的是将原始图像和生成图像通过判别器后得到的正确概率的差异，$L_{adv} = \|f(x) - f(\hat{x})\|_2$;
\(\omega_1\)、\(\omega_2\) 和 \(\omega_3\) 分别是三项对应的权重。

GANomaly 的优点是可以对输入数据进行端到端的学习，免去了大量的人工标注和处理异常数据的困难，可以很好地应用于异常检测、图像修复与补全等任务。

## 复习题

- 1. 请简述生成对抗网络的基本结构和目标函数。
- 2. 请简述生成对抗网络的训练过程并图示说明。
- 3. 请简述生成对抗网络限定条件优化的主要方法。
- 4. 请简述生成对抗网络迭代式生成优化的主要方法。
- 5. 请简述生成对抗网络结构优化的主要方法。
- 6. 请思考并给出生成对抗网络的一些应用场景，并设计初步的网络架构。

## 实验题

- 1. 基于手写数字识别数据集 MNIST，设计并实现一个限定条件的生成对抗网络，支持手写数字图像的有条件生成，条件如手写数字图像的类别、倾斜角度和宽度等。要求能够完成数据读取、网络设计、网络构建、模型训练和模型推理等过程。MNIST 数据集官方下载地址：http://yann.lecun.com/exdb/mnist/。
- 2. 请参考 CycleGAN 设计并实现一个支持图像风格转换的生成对抗网络，支持多种图像风格的转换，如季节互换、画风与照片互换等，数据集自选。要求能够完成数据读取、网络设计、网络构建、模型训练和模型推理等过程。CycleGAN 参考网址：https://junyanz.github.io/CycleGAN/。
- 3. 请参考 StackGAN 设计并实现一个支持文本描述生成图像的生成对抗网络，支持从文本描述生成逼真的图像，数据集自选。要求能够完成数据读取、网络设计、网络构建、模型训练和模型推理等过程。StackGAN 参考网址：https://github.com/hanzhanggit/StackGAN。

## 参考文献

- [1] GOODFELLOW I, POUGET-ABADIE J, MIRZA M, et al. Generative adversarial networks [J]. Communications of the ACM, 2020, 63 (11): 139-144.
- [2] PASCUAL S, BONAFONTE A, SERRA J. SEGAN: speech enhancement generative adversarial network [J]. arXiv preprint arXiv:1703.09452, 2017.
- [3] MIRZA M, OSINDERO S. Conditional generative adversarial nets [J]. arXiv preprint arXiv:1411.1784, 2014.
- [4] CHEN X, DUAN Y, HOUTHOOFT R, et al. InfoGAN: interpretable representation learning by information maximizing generative adversarial nets [C]. Advances in Neural Information Processing Systems 29, 2016.
- [5] DENTON E L, CHINTALA S, FERGUS R. Deep generative image models using a laplacian pyramid of adversarial networks [C]. Advances in neural information processing systems 28, 2015.
- [6] ZHANG H, XU T, LI H, et al. StackGAN: text to photo-realistic image synthesis with stacked generative adversarial networks [C]. Proceedings of the IEEE International Conference on Computer Vision, 2017: 5907-5915.
- [7] RADFORD A, METZ L, CHINTALA S. Unsupervised representation learning with deep convolutional generative adversarial networks [J]. arXiv preprint arXiv:1511.06434, 2015.
- [8] ISOLA P, ZHU J Y, ZHOU T, et al. Image-to-image translation with conditional adversarial networks [C]. Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition, 2017: 1125-1134.
- [9] LI C, WAND M. Precomputed real-time texture synthesis with markovian generative adversarial networks [C]. Proceedings of European Conference on Computer Vision, 2016.
- [10] HUANG H, YU P S, WANG C. An introduction to image synthesis with generative adversarial nets [J]. arXiv preprint arXiv:1803.04469, 2018.
- [11] WANG X, GUPTA A. Generative image modeling using style and structure adversarial networks [J]. arXiv preprint arXiv:1603.05631, 2016.
- [12] YOO D, KIM N, PARK S, et al. Pixel-level domain transfer [C]. Proceedings of the 14th European Conference on Computer Vision, 2016: 517-532.
- [13] ZHU J Y, PARK T, ISOLA P, et al. Unpaired image-to-image translation using cycle-consistent adversarial networks [C]. Proceedings of the IEEE International Conference on Computer Vision, 2017: 2223-2232.
- [14] YI Z, ZHANG H, TAN P, et al. DualGAN: unsupervised dual learning for image-to-image translation [C]. Proceedings of the IEEE International Conference on Computer Vision, 2017: 2849-2857.
- [15] LEDIG C, THEIS L, HUSZAR F, et al. Photo-realistic single image super-resolution using a generative adversarial network [C]. Proceedings of IEEE Conference on Computer Vision and Pattern Recognition, 2017.
- [16] 孙琪. 基于 GAN 的人脸图像修复和超分重建方法研究与应用 [D]. 开封: 河南大学, 2022.
- [17] MOGREN O. C-RNN-GAN: continuous recurrent neural networks with adversarial training [J]. arXiv preprint arXiv:1611.09904, 2016.
- [18] DONG H W, HSIAO W Y, YANG L C, et al. MuseGAN: multi-track sequential generative adversarial networks for symbolic music generation and accompaniment [C]. Proceedings of AAAI Conference on Artificial Intelligence, 2017.
- [19] SCHLEGL T, SEEBÖCK P, WALDSTEIN S M, et al. Unsupervised anomaly detection with generative adversarial networks to guide marker discovery [C]. Proceedings of the 25th International Conference on Information Processing in Medical Imaging, 2017: 146-157.
- [20] AKCAY S, ATAPOUR-ABARGHOUEI A, BRECKON T P. GANomaly: semi-supervised anomaly detection via adversarial training [C]. Proceedings of the 14th Asian Conference on Computer Vision, 2019: 622-637.
