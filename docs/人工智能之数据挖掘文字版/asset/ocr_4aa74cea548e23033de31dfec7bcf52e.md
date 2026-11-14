An Efficient Two-Layer Mechanism for Privacy-Preserving Truth Discovery.
作者：Yaliang Li (Tencent Medical AI Lab), Chenglin Miao (State University of New York at Buffalo), Lu Su (State University of New York at Buffalo), Jing Gao (State University of New York at Buffalo), Qi Li (University of Illinois at Urbana Champaign), Bolin Ding (Alibaba Group), Zhan Qin (University of Texas at San Antonio), Kui Ren (State University of New York at Buffalo)
收录会议：KDD'18

“MIX: Multi-Channel Information Crossing for Text Matching” 提出利用多通道信息交叉进行文本匹配模型 MIX。MIX 融合了 CNNs 和注意力机制，通过提取多个粒度的特征得到 MIX 表示的文本片段，然后通过 semetic information channel 和 structutal information channel （用作 attention mechanism）进行文本匹配得到精确的匹配结果。下图给出了多粒度建模的总体框架。具体方法分为三步：

+   Step1: 句子在不同的粒度被分割成不同的片段；

+   Step2: 在 attention units 部分，通过提取语法信息来设计 attention channels 中的注意矩阵。

+   Step3: 如图 weighed channels 和 2D-convolution 部分所示，通过交叉局部匹配通道和注意通道，提取显著特征组合进行局部匹配。

![](bbox=[0.305, 0.436, 0.726, 0.622])

图 58 局部匹配图

“On the Generative Discovery of Structured Medical Knowledge” 在智能医疗领域，芝加哥大学的 Chenwei Zhang 和腾讯的 Yaling Li 等人引入了一种生成式的视角来研究关系医学实体对发现问题，旨在在最小化数据需求的同时，扩大高质量而又新颖的结构化新医学知识的规模。他们提出了 Conditional Relationship Variational Autoencoder（CRVAE）模型，在没有复杂特征工程的情况下，通过单独地从不同维度表达的实值对学习共性，该模型可以生成性的发现特定医疗关系下的实体对，并在模型编码的过程中获得关系增强的实体表示。