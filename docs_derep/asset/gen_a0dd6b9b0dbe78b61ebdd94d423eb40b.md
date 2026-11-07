（3）init_weights 的作用是对模型参数进行初始设定，重点覆盖位置编码 pos_embed 与潜在向量 latent。初始化时两者都采用截断正态分布，其中 pos_embed 的方差依据 1 / sqrt(embedding_dim) 计算，latent 则依据 1 / sqrt(latent_dim) 计算。该策略能约束参数幅值，缓解梯度消失或爆炸的风险，从而让训练更平稳。

[PRE0]

（4）init_weights_vit_timm 参照 timm 库的惯例来初始化 ViT 权重，以保证结果可复现。对 nn.Linear 的 weight 使用标准差 0.02 的截断正态分布，bias 全部置零。若模块本身实现了 init_weights 方法，则会额外调用，以便自定义初始化逻辑得到执行。

[PRE1]

（5）Attention 类提供了多头自注意力 MHSA 的实现，用来对输入特征做加权交互。其内部流程包括：
● 投影：借助 qkv 线性层把输入 x 映射为查询 Q、键 K、值 V。
● 归一化：可选择对 Q、K 做归一化，提升训练稳定性。
● 注意力求解：当 self.fused_attn 为真时调用 torch.F.scaled_dot_product_attention 做高效计算；否则手工实现缩放点积注意力，先做 softmax(q @ k^T / sqrt(d)) 再与 v 相乘。
● 输出处理：把注意力结果重排后经 proj 投影回原通道数，并通过 Dropout 抑制过拟合。
该机制帮助 Transformer 建立不同位置间的长程依赖，增强全局建模能力。

[PRE2][PRE3]

（6）LayerScale 类引入层级缩放思想，通过可学习系数 gamma 调节张量尺度。初始化时 gamma 维度与输入一致，默认初值 1e-5。前向时张量 x 直接与 gamma 相乘，可选就地 inplace 计算或返回新张量。该设计用于稳定深层网络的激活幅度。

[PRE4]

（7）Block 类是 Transformer 的基本单元，由注意力子层和前馈 MLP 构成。其工作方式如下。
● 归一化与注意力：先做 norm1，再经注意力处理，随后叠加 LayerScale 与残差，若开启 DropPath 则随机丢弃部分路径。
● 前馈网络：通过 norm2 进入 MLP，同样配合 LayerScale 与 DropPath。
● 最终把两次变换的结果残差相加得到输出。
该模块融合了多头注意、前馈网络、层级缩放和路径丢弃，以改善训练效果并降低过拟合。

[PRE5][PRE6]

（8）VisionTransformer 是一个面向图像分类的 ViT 模型，核心做法是将图像切分为固定 patch 嵌入为向量后送入 Transformer。该结构支持输入尺寸动态调整、位置编码插值、patch 丢弃等配置，整体由图像块嵌入、多层 Transformer 以及分类头组成，通过自注意力学习块间关系并输出类别。

● 方法 __init__()：负责创建模型所需变量，包括图像大小、嵌入维、层数、头数等，完成整体骨架搭建。

[PRE7][PRE8][PRE9][PRE10][PRE11]

● 方法 init_weights()：依据指定方案如 "jax"、"moco" 等初始化权重，并对位置嵌入、类别 token 做特殊处理。

[PRE12]

● 方法 no_weight_decay()：返回不参与权重衰减的参数集合。

[PRE13]

● 方法 group_matcher()：返回参数分组匹配字典，用于优化器设定。

[PRE14]

● 方法 set_grad_checkpointing()：开关梯度检查点，以在训练时节省显存。

[PRE15]

● 方法 get_classifier()：取出分类头模块。

[PRE16]

● 方法 reset_classifier()：重设分类头类别数，可选择增删全局池化。

[PRE17]

● 方法 _pos_embed()：对输入做位置嵌入，并按动态尺寸采样后拼接到特征。

[PRE18]

● 方法 _intermediate_layers()：提取中间层输出，支持多层结果返回。

[PRE19][PRE20]

● 方法 get_intermediate_layers()：提供获取中间层输出的接口，可控制是否归一化、是否包含类别 token。

[PRE21][PRE22]

● 方法 forward_features()：完成特征提取流程，包括嵌入、位置编码、Transformer 编码。

[PRE23]

● 方法 forward_head()：执行分类头前向，含池化与类别预测。

[PRE24]

● 方法 forward()：整合特征提取与分类头，最终输出预测结果。

[PRE25]

（9）SigLIPVisionCfg 是一个数据类，用来保存视觉模型超参数，涵盖模型宽度、层数、头数、patch 大小、输入尺寸、全局池化方式、MLP 比例、是否使用类别 token、类别数及是否启用检查点等，便于灵活调参。

[PRE26]

（10）代码中定义了 SigLIP 模型的配置字典，收录多套不同版本的参数组合，用于根据图像尺寸和计算预算初始化对应模型。

[PRE27][PRE28]

（11）SigLIP_MODEL_CONFIG 字典以模型名为键存储各类视觉配置的超参数，包含图像尺寸、patch 大小、宽度、层数、头数、MLP 比例、全局池化以及检查点开关等，提供多种可选方案以便按需选用。

[PRE29][PRE30]

### **5.5.5** **图像处理器**

image_processing_vlm.py 实现了 VLMImageProcessor，用来对输入图像做预处理，包括尺寸调整、填充、归一化，确保图像符合模型输入要求。

[PRE31][PRE32][PRE33][PRE34][PRE35]

其中 VLMImageProcessor 继承 BaseImageProcessor，执行标准化流程；VLMImageProcessorConfig 则负责配置图像尺寸、最小尺寸、均值标准差等参数。

### **5.5.6** **多模态因果语言模型**

modeling_vlm.py 定义了融合视觉与语言的多模态因果语言模型，支持理解与生成任务。视觉、语言、对齐等模块协同工作，输出图像与文本的联合表征。

（1）vision_head 是一个视觉处理模块，包含输出投影 mlp、GELU 激活以及最终的视觉头线性层，将输入嵌入映射为指定维度的图像特征。

[PRE36][PRE37]

（2）model_name_to_cls 根据类名字符串返回对应模型类，匹配到 MlpProjector、CLIPVisionTower、VQ 或 vision_head 时返回相应类，否则抛 ValueError。

[PRE38]

（3）VisionConfig 继承 PretrainedConfig，用来保存视觉模型配置。包含类名 cls 与参数字典 params，初始化时处理字符串形式的 cls。

[PRE39][PRE40]

（4）AlignerConfig 继承 PretrainedConfig，定义对齐模型配置，初始化时按 kwargs 设定 cls 与 params。

[PRE41]

（5）GenVisionConfig 继承 PretrainedConfig，定义生成式视觉模型配置。

[PRE42]

（6）GenAlignerConfig 继承 PretrainedConfig，定义生成式对齐模型配置。

[PRE43][PRE44]

（7）GenHeadConfig 继承 PretrainedConfig，定义生成头模型配置。

[PRE45]

（8）MultiModalityConfig 继承 PretrainedConfig，整合视觉、对齐、生成视觉、生成对齐、生成头及语言模型等子配置。初始化时根据传入参数创建各子配置，language_config 若为 LlamaConfig 实例则直接使用，否则新建。

[PRE46][PRE47]

（9）MultiModalityPreTrainedModel 继承 PreTrainedModel，提供多模态模型基类，配置类为 MultiModalityConfig，前缀设为 multi_modality，并定义空的 _no_split_modules 与 _skip_keys_device_placement。

[PRE48]

（10）MultiModalityCausalLM 继承自前述基类，实现多模态因果语言模型。依据配置实例化视觉模型、对齐器、生成视觉、生成对齐、生成头和语言模型等。提供 prepare_inputs_embeds 将图像和文本转为嵌入，以及 prepare_gen_img_embeds 处理生成图像嵌入，支持多模态融合与输出生成。

[PRE49][PRE50][PRE51]

（11）该段代码利用 AutoConfig 与 AutoModelForCausalLM 做注册，将 VisionConfig、AlignerConfig 等配置类与对应模型类型关联，实现自动加载与实例化，提升配置管理的扩展性。

[PRE52]

### **5.5.7** **多模态处理器**

训练与推理时需把用户文本与图像转为模型可接受格式。processing_vlm.py 定义的 VLChatProcessor 即用于处理多模态输入并生成适配格式。