- （9）介绍跨模态任务里常用的评测指标，例如 BLEU、CIDEr、ROUGE 等，结合典型应用场景说明这些指标如何评价文本生成与推理任务的性能，同时阐述在代码中实现这些指标计算的具体做法。
- （10）在本章模型搭建中，如何将通用预训练模型迁移到特定跨模态任务上进行微调，结合代码说明部分参数冻结与学习率调整的操作，并分析微调对模型效果的影响，以及如何在代码里验证微调后的性能提升。

### 第8章 多模态大模型推理提速实战

多模态模型的落地效率，很大程度上取决于推理端的表现，核心在于算力利用、内存占用和响应速度的整体提升。本章聚焦推理提速的关键手段，涵盖模型量化、结构剪枝、动态计算图以及混合精度等技术，结合不同硬件部署的差异，探讨如何把推理耗时压下来并减少资源开销。

本章提供一套可落地的优化思路，助力模型在多种场景下稳定高效运行。

#### 8.1 ONNX与TensorRT在多模态推理中的应用

推理阶段的性能瓶颈往往决定模型能否实用。ONNX 是一种通用的模型互换标准，支持跨平台、跨框架部署，为模型的转换与优化提供了顺畅通道；TensorRT 则专攻推理加速，尤其在量化与压缩层面优势明显。

本节围绕 ONNX 的优化转换流程展开，同时介绍 TensorRT 在推理加速和量化实现上的具体做法，目标是让多模态模型在高性能计算环境中高效部署。

##### 8.1.1 ONNX模型的优化与转换流程

ONNX 属于开放式中间表示，主要解决不同深度学习框架间的互通与部署问题。借助 ONNX，可把 PyTorch、TensorFlow 等训练框架导出的模型统一为通用格式，再交由 ONNX Runtime 等推理引擎加载执行。优化手段通常包括节点融合、权重量化、算子替换等，目的是提升推理效率。整体流程一般为：从源框架导出 ONNX 格式 → 对 ONNX 模型做优化处理 → 在推理引擎中加载运行。

ONNX 模型在推理与解释环节的技术实现路径如图8-1所示。

[PRE0]

从基于后训练转换得到的 ONNX 模型出发，解释过程可拆为两段。

第一段是推理路径，借助解析器完成前向与反向传播，并通过梯度或修正项的计算获取中间结果。该过程采用深度优先搜索来优化梯度流动的计算效率。

第二段是评分与解释阶段，先将推理结果缓存，再借助 Shapley 值等方法量化输入特征对预测结果的贡献度。该方式能够精准定位模型的关键特征，为优化与决策提供可解释依据。

下面代码演示如何把 PyTorch 模型导出为 ONNX 并做优化。

[PRE1]

运行结果如下：

> 模型已导出为 simple_model.onnx

> 模型已优化并保存为 optimized_model.onnx

> 推理结果: [array([[-0.315, 0.234]], dtype=float32)]

代码要点说明如下：

- （1）SimpleModel 是一个简易神经网络，用来演示 ONNX 导出与优化的过程。
- （2）torch.onnx.export 将 PyTorch 模型转为 ONNX 格式，支持设置动态批大小。
- （3）optimize 调用 ONNX 优化器执行节点融合与简化。
- （4）InferenceSession 加载优化后的 ONNX 模型并执行推理任务。
- （5）所有文件路径均可灵活调整，保证保存与加载路径一致。

上述实现覆盖了模型定义、导出、优化到推理的完整链路，适合小规模验证场景。ONNX 开发中常用函数及其功能与参数信息如表8-1所示。

表8-1 ONNX开发中常用函数及其功能与参数信息

| 函数名 | 功能描述 | 参数信息 |

| :--- | :--- | :--- |

| onnx.load | 加载 ONNX 模型文件 | path: 模型文件路径 |

| onnx.save | 保存 ONNX 模型到文件 | model: 模型对象; path: 文件保存路径 |

| onnx.checker.check_model | 检查 ONNX 模型的结构合法性 | model: ONNX 模型对象 |

| onnx.helper.make_tensor | 创建 ONNX 张量 | name: 张量名; data_type: 数据类型; dims: 维度; vals: 值 |

| onnx.helper.make_node | 创建 ONNX 计算节点 | op_type: 操作类型; inputs: 输入; outputs: 输出 |

| onnx.helper.make_graph | 创建 ONNX 计算图 | nodes: 节点列表; name: 图名称; inputs: 输入列表; outputs: 输出列表 |

| onnx.helper.make_model | 创建 ONNX 模型 | graph: 图对象; producer_name: 生产者名称 |

| onnx.helper.printable_graph | 打印 ONNX 图结构 | graph: 图对象 |

| onnx.optimizer.optimize | 对 ONNX 模型进行优化 | model: 待优化的模型; passes: 优化传递列表 |

| onnx.shape_inference.infer_shapes | 推断 ONNX 模型的形状 | model: 模型对象 |

| onnx.helper.make_attribute | 创建 ONNX 节点属性 | name: 属性名称; value: 属性值 |

| onnx.helper.make_value_info | 创建 ONNX 图的输入或输出信息 | name: 名称; elem_type: 数据类型; shape: 形状 |

| onnx.numpy_helper.to_array | 将 ONNX 张量转换为 NumPy 数组 | tensor: ONNX 张量对象 |

| onnx.numpy_helper.from_array | 将 NumPy 数组转换为 ONNX 张量 | array: NumPy 数组; name: 名称 |

| onnx.version_converter.convert_version | 将 ONNX 模型转换为指定的 IR 版本 | model: 模型对象; target_version: 目标版本 |

| onnx.mapping.NP_TYPE_TO_TENSOR_TYPE | 将 NumPy 数据类型映射到 ONNX 数据类型 | 无 |

| onnx.helper.make_tensor_value_info | 创建张量的元数据 | name: 张量名称; elem_type: 数据类型; shape: 维度 |

| onnx.external_data_helper.load_external_data_for_model | 加载外部数据到 ONNX 模型 | model: ONNX 模型对象; base_dir: 数据所在目录 |

| onnx.external_data_helper.convert_model_to_external_data | 将模型转换为使用外部数据 | model: 模型对象; all_tensors_to_one_file: 是否将所有数据存储到一个文件; location: 存储位置 |

| onnx.helper.make_sequence | 创建序列型数据 | name: 名称; data_type: 数据类型; elems: 元素列表 |

##### 8.1.2 TensorRT的推理加速与量化技术

TensorRT 是 NVIDIA 推出的高性能推理优化组件，面向深度学习模型的快速推理设计。它通过层融合、内存复用、动态批量和精度量化等手段，大幅提升推理速度与效率。量化是其核心能力之一，可把模型从 FP32 等浮点精度压缩至 INT8，显著降低计算复杂度，同时尽量保持精度。典型的加速流程包括加载 ONNX 模型、经 TensorRT 优化、执行推理。

TensorRT 在视觉模型中的加速架构如图8-2所示，该图展示了用 TensorRT 优化视觉 Transformer 的方案，明显提升推理速度与计算效率。左侧为常规 ViT 模型，以 Patch 嵌入与位置嵌入处理输入，随后经多层自注意力模块提取特征。右侧为经 TensorRT 优化的 TRT-ViT 模型，引入卷积模块与自注意力模块的融合结构，在保留 Transformer 特性的同时降低计算量。模型进一步利用 TensorRT 的加速特性，通过层归一化与瓶颈模块提升特征表达，并采用量化技术把浮点计算转为低位计算以降低内存需求。优化后的 TRT-ViT 推理效率更高，适合资源受限场景下部署高性能多模态任务。

图8-2 基于TensorRT的ViT模型推理加速与优化框架

以下代码示例将展示如何在TensorRT中使用量化技术优化一个模型并进行推理。