# TFLite 解释器核心接口一览

TensorFlow Lite 的解释器类是模型加载、张量访问与推理执行的调度中心。下面用更口语化的方式梳理一下它对外暴露的关键能力，方便在自媒体或技术分享中快速抓住重点。

**类型安全的张量指针获取**
提供模板化的 typed_tensor 读取，先通过 tensor 索引拿到 TfLiteTensor 原始指针，再校验当前类型是否与模板参数 T 对应，匹配时用 reinterpret_cast 转换到 T* 或 const T*，不匹配直接返回空指针。基于此封装了 typed_input_tensor 与 typed_output_tensor，分别按输入/输出索引间接定位到对应的张量，读写版本都有。

**推理生命周期控制**
ResizeInputTensor 用于在推理前调整输入维度，AllocateTensors 负责内存分配，Invoke 触发一次完整前向。UseNNAPI、SetNumThreads、SetAllowFp16PrecisionForFp32 控制硬件加速、线程数与 FP32 放宽到 FP16 的策略，GetAllowFp16PrecisionForFp32 读取当前允许状态。SetCancellationFunction 可注入外部取消检查回调。

**委托与缓冲区协作**
ModifyGraphWithDelegate 支持以裸指针或智能指针形式注入 TfLiteDelegate，内部会把托管的委托保存到 owned_delegates_。EnsureTensorDataIsReadable 保证指定张量数据可读，SetBufferHandle 与 GetBufferHandle 实现与委托之间的缓冲区句柄传递。SetProfiler / GetProfiler 接入性能分析器。

**配置与常量**
kTensorsReservedCapacity 设为 128，kTensorsCapacityHeadroom 设为 16，用于张量容器预留。SetAllowBufferHandleOutput 打开输出缓冲区句柄支持，ResetVariableTensors 重置可变张量。OpProfilingString 在操作注册信息存在时返回 profiling 字符串。

**子图与外部上下文**
SetExternalContext 添加外部上下文，AddSubgraphs 可动态增加子图，subgraphs_size 返回子图数量，subgraph 按索引获取，primary_subgraph 直接返回第一个子图的引用，注释说明该容器永远至少保留一个元素。友元类 InterpreterBuilder 与 InterpreterTest 可直接访问内部。

私有侧保留 TfLiteContext* context_、ErrorReporter* error_reporter_、委托所有权队列 owned_delegates_、是否允许缓冲区句柄输出的标志 allow_buffer_handle_output_、外部上下文数组 external_contexts_ 以及子图对象集合 subgraphs_。

[PRE0]cpp
实现文件以 tensorflow/lite/interpreter.h 为主入口，引入 cassert、cstdarg、cstdint、cstring 等基础头，以及 TensorFlow Lite 内部的 c_api_internal、context_util、error_reporter、graph_info、memory_planner、nnapi_delegate、profiler、schema_generated、util 等模块，代码位于命名空间 tflite 下并开启匿名内部命名空间。