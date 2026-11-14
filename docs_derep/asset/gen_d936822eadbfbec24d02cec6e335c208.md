# PureT架构深度解析：融合全局与局部的图像描述新范式

在图像描述生成任务中，如何高效建模视觉特征与语言序列的深度交互始终是核心挑战。PureT 模型提出了一种基于纯 Transformer 的端到端架构，通过精心设计的双流增强编码器与预融合解码器，实现了粗粒度全局语义与细粒度网格细节的协同建模。

## 核心骨干：Swin Transformer 特征提取

PureT 选用 Swin Transformer 作为骨干网络，从 $384 \times 384$ 的输入图像中抽取 $12 \times 12$ 共 144 个网格特征向量 $V_c = \{v_1, \dots, v_m\}$，维度为 $D$（经线性层映射至 512 维）。同时，通过均值池化聚合得到初始全局特征 $v_g \in \mathbb{R}^{512}$。得益于同源的 Transformer 架构，骨干网络可与后续模块无缝联合训练，避免了异构模型衔接的困境。

> **窗口注意力机制回顾**  
> Swin 核心在于将自注意力限制在局部窗口内计算：  
> $$(S) \text{W-MSA}(Q, K, V) = \text{Merge}(\text{window}_1, \dots, \text{window}_w)$$  
> $$\text{window}_i = \text{MSA}(Q^i_w, K^i_w, V^i_w)$$  
> 其中常规 W-MSA 采用固定划分，SW-MSA 引入位移窗口实现跨窗口信息流动（见下文图示）。

[PRE0]

a) 常规窗口划分 (W-MSA)

b) 位移窗口划分 (SW-MSA)

图示：PureT 采用的两种窗口注意力划分策略对比

## 增强编码器：双流交互与参数共享

增强编码器由 $N$ 个同构子层堆叠而成，其设计巧妙之处在于**网格特征流**与**全局特征流**的并行更新与深度耦合：

1.  **网格特征增强**：交替采用 W-MSA 与 SW-MSA 捕捉局部与跨窗口依赖，Key/Value 拼接全局特征 $v_g^{l-1}$，实现“局部看全局”的语义注入。
2.  **全局特征增强**：采用标准 MSA，Key/Value 拼接网格特征 $V_c^{l-1}$，实现“全局聚局部”的细节聚合。
3.  **参数共享机制**：两路特征流的投影矩阵 $W_q^l, W_k^l, W_v^l \in \mathbb{R}^{D \times D}$ 完全共享，大幅减少参数量并强制对齐语义空间。

数学形式上，第 $l$ 层更新规则为：

$$\tilde{V}_c^l = \text{LN}\big(V_c^{l-1} + (S)\text{W-MSA}(W_q^l V_c^{l-1}, W_k^l [V_c^{l-1}; v_g^{l-1}], W_v^l [V_c^{l-1}; v_g^{l-1}])\big)$$

$$\tilde{v}_g^l = \text{LN}\big(v_g^{l-1} + \text{MSA}(W_q^l v_g^{l-1}, W_k^l [V_c^{l-1}; v_g^{l-1}], W_v^l [V_c^{l-1}; v_g^{l-1}])\big)$$

随后分别接入前馈网络（FFN）与残差归一化，输出 $V_c^l, v_g^l$ 供下一层使用。最终得到增强后的 $V_c^N$ 与 $v_g^N$ 送入解码端。

## 解码器：预融合机制强化多模态交互

标准 Transformer 解码器仅在交叉注意力处融合视觉信息，PureT 指出这限制了模态间交互深度。为此，其在每个解码层内引入**预融合模块**，实现视觉全局特征 $v_g^N$ 与语言历史隐状态的**早期融合**，构成“双阶段交互”范式：

### 四大核心组件逐层解析

| 模块 | 功能定位 | 关键操作 |
| :--- | :--- | :--- |
| **预融合** | 第1次模态交互：视觉全局 $\rightarrow$ 语言 | 拼接 $v_g^N$ 经 ReLU+Linear 降维，残差接入词嵌入 |
| **掩码自注意力** | 语言内部建模：单词间依赖 | 标准因果 MSA，防止信息泄露 |
| **交叉注意力** | 第2次模态交互：视觉网格 $\leftrightarrow$ 语言 | Query 来自语言，Key/Value 取增强网格特征 $V_c^N$ |
| **前馈 & 生成** | 特征变换与词表概率预测 | FFN 后接 Softmax 输出下一词分布 |

**预融合公式化表达**：
$$X^{p,l} = \text{LN}\big(X^{l} + \text{ReLU}(W_f [X^{l}; v_g^N])\big)$$
其中 $W_f \in \mathbb{R}^{D \times 2D}$ 为融合投影矩阵，$X^l$ 为上一层输出（首层为词嵌入 $W_e x_{1:t-1}$）。

**后续流程**：
1.  掩码自注意力：$\widetilde{X}^l = \text{LN}(X^{p,l} + \text{MSA}_{mask}(\dots))$
2.  交叉注意力：$\hat{X}^l = \text{LN}(\widetilde{X}^l + \text{MSA}_{cross}(Q=\widetilde{X}^l, K,V=V_c^N))$
3.  FFN 与输出：$X^l = \text{LN}(\hat{X}^l + \text{FFN}(\hat{X}^l))$，最终 $p(x_t|x_{<t}) = \text{Softmax}(W_s X^l_{t-1})$

这种“预融合 + 交叉注意力”的双通道设计，使得全局语义先引导语言建模，细节特征再精准定位生成内容，显著提升了描述的准确性与连贯性。

## 章节小结与思考

PureT 的创新在于：
- **编码端**：双流共享参数的交替窗口注意力，平衡了局部细节感知与全局语义聚合。
- **解码端**：预融合模块前置了跨模态交互时刻，打破了“编码-解码”割裂的单一交互瓶颈。
- **工程性**：全 Transformer 统一架构，便于端到端优化与大规模预训练迁移。

### 复盘自测题
1.  对比 MSA、W-MSA、SW-MSA 的计算图差异及适用场景。
2.  PureT 增强编码器中“参数共享”对模型容量与泛化性的双重影响？
3.  预融合模块为何选择全局特征 $v_g^N$ 而非网格特征 $V_c^N$？若替换会怎样？
4.  解码器两次注意力（掩码自注意力 vs 交叉注意力）在 Query/Key/Value 来源上的本质区别。

### 实战复现指南
- **任务 1**：基于 NiuTran 语料复现标准 Transformer 机翻基线（数据清洗 $\rightarrow$ 模型搭建 $\rightarrow$ 训练评测）。
- **任务 2**：CIFAR-10 上从零实现 ViT 分类器，对比 CNN 基线，分析 Patch Size 与深度对性能的影响。
- **任务 3**：Flickr30k 数据集上构建 CNN-Transformer 或 PureT 风格的图像描述模型，重点调试 Beam Search 解码策略与 CIDEr 指标优化。

> **数据获取**：NiuTran (GitHub) / CIFAR-10 (官网) / Flickr30k (Illinois官网)

## 核心参考文献速览
| 方向 | 代表作 | 核心贡献 |
| :--- | :--- | :--- |
| **基石** | Vaswani et al. (2017) | Attention Is All You Need，确立 Transformer 范式 |
| **预训练演进** | GPT 系列 / BERT / RoBERTa / ALBERT / BART / ERNIE | 从单向到双向，从大模型到参数高效，融合知识增强 |
| **视觉骨干** | Dosovitskiy et al. (2021) - ViT | 图像即 Patch 序列，开启纯 ViT 时代 |
| | Liu et al. (2021) - Swin Transformer | 层次化 + 位移窗口，兼顾效率与感受野，成 CV 基座 |
| **应用拓展** | Liu & Lapata (2019) | 预训练 Encoder 用于文本摘要，验证迁移有效性 |

*注：完整参考文献列表见原教材第 4 章末尾 [1]-[18]。*