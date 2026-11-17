# GAN 的博弈机制：生成器与判别器的“斗法”

以图像生成任务为例，GAN 的生成器网络接收随机噪声 `z` 作为输入，以此合成图像。判别器网络则负责鉴别输入图像 `x` 的真伪，输出 `D(x)` 表示 `x` 为真实图像的概率：数值 1 意味着百分百真实，0 则代表绝无可能。训练阶段，生成器致力于制造以假乱真的图像欺骗判别器，而判别器则全力识破生成器的伪造品。这对网络由此构建起一个动态的“博弈过程”，最终收敛于纳什均衡点，如图 4-55 所示。

## 8 胶囊神经网络

[PRE0]

Figure 4-55

胶囊神经网络（CapsNet）是当前最受瞩目的神经网络架构之一。它由 Hinton 于 2017 年 10 月提出。与 CNN 类似，CapsNet 最初也聚焦于视觉任务。其诞生缘于 Hinton 发现 CNN 在图像识别中存在难以攻克的短板：CNN 通过卷积提取图像各组成元素的特征，却难以捕捉元素间的空间关联关系。例如，人脸具备眼睛、鼻子、嘴巴，CNN 仅凭这些元素存在与否便判定为人脸；即便鼻子长在额头、眼睛挪到嘴边，CNN 仍可能误判。实际上，人脸识别不仅要求元素齐全，元素间的几何拓扑关系至关重要。此外，CNN 对图像的平移、旋转变换适应性较差。图 4-56 展示了多个不同姿态的字母 “A”，人类极易辨认其同一性，但 CNN 却将它们视为不同字符。

[PRE1]

Figure 4-56

Hinton 指出，人类与 CNN 的根本差异在于：人类识别图像遵循树状结构抽象，CNN 则依赖层级结构抽象。CNN 追求不变性与稳定性，而胶囊网络追求协变性。这使得 CapsNet 能在不同视角下对同一图像形成抽象表征，而非局限于固定视角。

正因具备对图像进行翻转、平移及特征组合的能力，CapsNet 大幅降低了数据标注负担，实现了自主学习与抽象。

与 CNN 拥有深层网络相似，CapsNet 也呈现层级架构（见图 4-57）。不同层级的胶囊处理不同信息：底层胶囊训练图像细节特征，高层胶囊学习细节特征间的相互关系，并整合各胶囊给出的位置姿态信息，最终重构图像。

[PRE2]

Figure 4-57

CapsNet 作为近年来极具颠覆性的网络架构，其核心创新在于将输入标量转换为向量集合。目前，它在 MNIST 数据集上刷新了最高准确率。得益于胶囊对特征位置与旋转关联的建模能力，CapsNet 在小样本条件下即可超越传统 CNN 的大数据训练效果。

## 4.6 机器学习框架概览

随着 AI 技术演进，AI 产品研发已高度依赖各类开发库与框架。市场主流框架众多，如 TensorFlow、Caffe、CNTK、PyTorch、Theano 等，各有所长。2017 年 5 月，GitHub 上曾统计开源机器学习框架热度榜（见图 4-58），TensorFlow 居首，Caffe 与 CNTK 紧随其后。

[PRE3]

Figure 4-58

2018 年初，Google 研究科学家 Chollet 基于 Google 搜索指数发布新榜单（见图 4-59）：TensorFlow 指数量第一，Keras 第二，随后依次为 Caffe、PyTorch、Theano、MXNet、Chainer、CNTK。

[PRE4]

Figure 4-59

2018 年 3 月，特斯拉 AI 总监 Karpathy 对近 6 年 ArXiv 上 4300 篇论文进行全面统计，结果更具参考价值。按框架提及占比排名：TensorFlow 5.9%、Caffe 5.4%、Theano 3.2%、Keras 2.3%、Torch 1.6%、PyTorch 1%、其他 0.5%。

跨时间、跨维度的多份榜单一致指向：近年最热门框架非 TensorFlow 莫属，第二梯队包含 Caffe、Keras、Theano、PyTorch 等。主流框架特性对比见表 4-60。

[PRE5]

Figure 4-60

### 4.6.1 TensorFlow

TensorFlow 源自 Google 的 DistBelief 项目。名中 “Tensor” 指张量，“Flow” 指流动，寓意张量在数据流图中的传递过程。其架构极具灵活性，支持轻松部署至 CPU、GPU、TPU 乃至智能手机等多元平台。

TensorFlow 优势显著：原生支持 Python，极大降低入门门槛；采用数据流图抽象，配合 TensorBoard 实现可视化追踪。目前 AMD、NVIDIA、Uber、SAP、DeepMind、小米、京东、联想、楷谱云等众多企业均采用它开发。

全开源特性使其随取随用，寥寥数行代码即可运行，让神经网络开发变得触手可及。安装同样便捷：Windows 下 CPU 版执行 `pip install tensorflow`，GPU 版执行 `pip install tensorflow-gpu`。装配完成后，建议安装 Jupyter Notebook（`pip install jupyter`）以便文档化编程。