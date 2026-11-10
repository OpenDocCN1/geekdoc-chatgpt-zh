TensorFlow Lite Interpreter 核心实现速览

在 TensorFlow Lite 的解释器层，绝大多数对外接口其实都是对首个子图的透传，同时对量化、线程、委托等运行时策略做了统一收口。下面把源码里暴露的关键机制梳理一遍。

旧版量化参数的兼容转换
GetQuantizationFromLegacy 的作用是把老式的 TfLiteQuantizationParams 迁移到新的 TfLiteQuantization 结构。内部先把类型固定为 kTfLiteAffineQuantization，随后通过 malloc 新建一个 TfLiteAffineQuantization 实例，为 scale 与 zero_point 各分配长度为 1 的数组，并把 legacy 中的 scale 和 zero_point 原值拷贝进去，最后把指针挂到 quantization.params 返回。

解释器构造与销毁
构造时若未传入 error_reporter 会回退到默认实现，随后默认创建 1 个子图并拿到其上下文。外部上下文数组全部初始化为空指针，NNAPI 默认关闭。析构函数为空，实现依赖子图自行释放资源。

外部上下文与输入输出设定
SetExternalContext、SetInputs、SetOutputs、SetVariables 全部转发给 primary_subgraph，对外保持统一入口。AllocateTensors、ReserveNodes、AddTensors、ResetVariableTensors 也同理，统一走主子图。

子图管理
AddSubgraphs 会根据需要预留容量并循环新建 Subgraph 实例，保存到 subgraphs_ 列表中，并可返回第一个新子图的索引。AddNodeWithParameters、ResizeInputTensor、SetExecutionPlan 均直接委托给主子图执行。

推理调用
Invoke 先调用主子图的 Invoke，若未允许使用 buffer handle 输出，则遍历当前输出张量列表，逐个调用 EnsureTensorDataIsReadable 保证数据可读，最后返回成功状态。

张量参数设置与量化兼容
针对只读与可读写两种张量参数设置，提供了基于 vector<int> dims 的重载和基于原始 rank/dims 的重载。老式签名内部会先调用 GetQuantizationFromLegacy 完成转换，调用主子图接口后若失败则释放新建的量化对象并返回错误，确保资源不泄漏。

执行环境控制
UseNNAPI 直接透传给主子图。SetNumThreads 会遍历所有子图修改 recommended_num_threads，并对已注册且提供 Refresh 回调的外部上下文进行刷新。SetAllowFp16PrecisionForFp32 则批量修改各子图上下文的 allow_fp32_relax_to_fp16 标志。

取消与委托
SetCancellationFunction 为每个子图注册同一份取消检查回调。ModifyGraphWithDelegate 遍历全部子图逐个应用委托，任一失败即回滚。SetBufferHandle 会校验张量索引合法性、委托一致性，若原张量已有 buffer handle 且委托提供 FreeBufferHandle 则先释放，再写入新的 handle 并更新委托指针。

整体来看，Interpreter 类的核心职责是做统一调度与策略下发，真正的张量、节点、执行逻辑都下沉到 Subgraph，构造、量化兼容、线程与委托管理是对外最易关注的要点。