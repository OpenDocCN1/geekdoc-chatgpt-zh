### 破解二值网络算力瓶颈：从核分解到核转化的进阶之路

在二值权重 CNN 的加速赛道上，**卷积核分解**曾是主流思路。以单输入多输出场景为例（对应原文图 8.7(a)），假设 N 个卷积核的 +1/-1 权重比例对半开，常规做法需执行 N 次卷积。分解架构将核拆为“基础核”与“滤波核”，因基础核共享，首次计算后即可复用，省下 N-1 次基础卷积；再加上滤波核天然具备 50% 稀疏性，总计算量从 N 降至 N/2+1，大致砍半。若配合门控时钟跳过零权重运算，能效比还能再提一档。

[PRE0]

推广到 M 输入 N 输出的通用场景（原文图 8.7(b)），逻辑同理：原本需 N×M 次运算，利用基础核复用与稀疏性优势，可压缩至 N×M/2+M 次。当输出通道数 N 足够大时，计算量同样逼近减半水平。

[PRE1]

图 8.7 单/多输入多输出卷积场景示意（见彩图）

[PRE2]

**但分解法有两大短板**：一是冗余消除不彻底；二是拆解过程会破坏三值核（含 0 值）的稀疏结构，导致该方法**无法迁移至三值网络**。

Zheng 团队提出的**卷积核转化架构**精准破局。它不拆核，而是通过启发式迭代搜索，从海量卷积核中挖掘“最大公约数”式的公共因子，将原始核群转化为数量更少、稀疏度更高的“基卷积核”（原文图 8.8 对比显著）。实测在 SVHN、AlexNet、VGG-16 上，**计算量骤降 43.4%~79.9%**，且二值、三值网络通吃，能效大幅跃升。

[PRE3]

图 8.8 卷积核分解 vs 转化 对比示意（见彩图）

[PRE4]

**硬件落地方面**，为挖掘数据复用、规避零权重运算并完成特征图重组，专用架构包含：并行处理阵列（PE）、片上缓存、转化控制器及存储接口。PE 从缓存取激励与稀疏压缩权重/索引，完成基础卷积并暂存中间结果。

针对二/三值网络“激励位宽 > 权重位宽”特性，采用**输入静态数据流**削减激励搬运开销：激励广播至各 PE 输入寄存器，完成当前通道所有基础卷积；中间结果落临时缓存。权重沿用 SCNN/ESE 风格稀疏编码。PE 同步承担原始部分和重组任务，32 通道并行累加。查找表（LUT）双重发力——索引基础卷积结果广播给 PE，亦索引输出缓存读取历史累加值，实现高效重组。

**存内计算（CIM）新范式**近期异军突起，为 BNN 带来全新硬件图景：
- Ando 等构建 **BRein Memory** 芯片，权重驻留 SRAM，逻辑电路直连实现并行原地计算；
- Liu 团队用 6T-SRAM 跑 BNN，拓展 8T 单元支持位级 XNOR；
- Kim 等巧借核分解思路将 XNOR 映射为 NAND，沿用成熟 6T-SRAM 完成 XNOR-Net 推理，兼顾算量削减与电路简化。

---

### 参考文献

- [1] Lee E H, 等. Lognet: 对数计算驱动的能效神经网络. ICASSP 2017.
- [2] Ma Y, 等. 面向 FPGA 的 CNN 可扩展模块化 RTL 编译. FPL 2016.
- [3] Gysel P, 等. 硬件导向的 CNN 近似计算. ICLR 2016.
- [4] Horowitz M. 计算能耗墙及对策. ISSCC 2014.
- [5] Judd P, 等. Stripes: 位串行 DNN 计算. MICRO 2016.
- [6] Moons B, Verhelst M. 0.3-2.6 TOPS/W 精度可扩展实时大规模 ConvNet 处理器. VLSI Circuits 2016.
- [7] Han S, 等. Deep Compression: 剪枝/量化/霍夫曼编码三管齐下压缩 DNN. ICLR 2016.
- [8] Miyashita D, 等. 对数数据表示的 CNN. arXiv:1603.01025, 2016.
- [9] Zhou A, 等. 增量网络量化：迈向无损低精度 CNN. ICLR 2017.
- [10] Chen W, 等. 哈希技巧压缩神经网络. ICML 2015.
- [11] Shi S, Chu X. 利用 ReLU 稀疏性加速 CNN. arXiv:1704.07724v2, 2017.
- [12] Chen Y H, 等. Eyeriss: 能效可重构 CNN 加速器. ISSCC 2016.
- [13] Albericio J, 等. Cnvlutin: 无效神经元免疫 DNN 计算. ISCA 2016.
- [14] Reagen B, 等. Minerva: 低功耗高精度 DNN 加速器. ISCA 2016.
- [15] Wen W, 等. DNN 结构化稀疏学习. NIPS 2016.
- [16] Anwar S, 等. 深度 CNN 结构化剪枝. ACM JETC 2017.
- [17] Mao H, 等. 探索 CNN 稀疏结构规律性. CVPR Workshop 2017.
- [18] Yu J, 等. Scalpel: 面向硬件并行度的 DNN 剪枝定制. ISCA 2017.
- [19] LeCun Y, 等. Optimal Brain Damage. NIPS 1990.
- [20] Han S, 等. 同时学习权重与连接的高效网络. NIPS 2015.
- [21] Yang T J, 等. 能量感知剪枝设计能效 CNN. CVPR 2017.
- [22] Dorrance R, 等. FPGA 上能效稀疏矩阵-向量乘法内核. ISFPGA 2014.
- [23] Han S, 等. EIE: 压缩 DNN 上的高效推理引擎. ISCA 2016.
- [24] Parashar A, 等. SCNN: 压缩稀疏 CNN 加速器. ISCA 2017.
- [25] Szegedy C, 等. 重新思考 Inception 架构. CVPR 2016.
- [26] Lim J S. 二维信号与图像处理. Prentice Hall, 1990.
- [27] Chollet F. Xception: 深度可分离卷积. CVPR 2017.
- [28] Howard A G, 等. MobileNets: 移动视觉高效 CNN. arXiv:1704.04861, 2017.
- [29] He K, 等. 深度残差学习. CVPR 2016.
- [30] Szegedy C, 等. Going Deeper with Convolutions. CVPR 2015.
- [31] Iandola F N, 等. SqueezeNet: 50 倍参数量达 AlexNet 精度. ICLR 2017.
- [32] Denton E, 等. 利用线性结构高效评估 CNN. NIPS 2014.
- [33] Lebedev V, 等. 微调 CP 分解加速 CNN. ICLR 2015.
- [34] Kim Y D, 等. 面向移动端的深度 CNN 压缩. ICLR 2016.
- [35] Courbariaux M, 等. BinaryConnect: 传播中二值化权重训练. NIPS 2015.
- [36] Courbariaux M, Bengio Y. BinaryNet: 权重/激活约束至 ±1. arXiv:1602.02830, 2016.
- [37] Rastegari M, 等. XNOR-Net: 二值 CNN 做 ImageNet 分类. ECCV 2016.
- [38] Hubara I, 等. 量化神经网络: 低精度权重/激活训练. arXiv:1609.07061, 2016.
- [39] Li F, Liu B. 三值权重网络. NIPS Workshop 2016.
- [40] Zhu C, 等. 训练三值量化. ICLR 2017.
- [41] Andri R, 等. YodaNN: 基于二值权重的超低功耗 CNN 加速器. ISVLSI 2016.
- [42] Kim 等. 卷积核分解将 XNOR 转 NAND，6T-SRAM 实现 XNOR-Net. (文中引用)
- [43] Zheng 等. 卷积核转化架构，适用二/三值网络. (文中引用)
- [44] Ando 等. BRein Memory: 存内计算二/三值网络芯片. (文中引用)
- [45] Liu 等. 6T/8T-SRAM 实现 BNN 与位级 XNOR. (文中引用)