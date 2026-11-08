面对前面梳理的性能短板，人为地对瓶颈进行雕琢是提升硬件利用率最直接的手段。只有在吃透计算图结构的基础上，自研算子才有价值。它能把多步中间算子合并成一步来削减调度损耗，也能对 GPU 的运行进行精细化干预，比如线程块的划分方式、内存访问的布局策略等。典型代表就是 flash-attention 库，它为 Transformer 的自注意力模块量身定制了专属算子，通过重排内存访问并更充分地调用 GPU 并行能力，把算力效率拉满。

不过自定义算子不是谁都能用的玩具。用好它需要对算子的内存访问特征、计算瓶颈分布以及 CUDA / C++ 编程有相当深的理解。我们更倾向的建议是，把它留到性能调优和模型部署的收尾阶段再考虑。模型探索期还是以 PyTorch 原生的算子为主，简单可控更利于迭代。

#### 9.2.1 自定义算子封装流程概览

本节以 Sigmoid 作为演示对象，展示一种同时兼容 CPU 和 CUDA 输入的自定义算子开发思路。需要说明的是，这里并非讲解 CUDA 优化技巧，这个样例的运行效率甚至可能不及 PyTorch 原生实现。核心在于把实现思路和关键步骤讲清楚，帮助读者理解如何迁移社区已有的算子，并为日后自行编写打下基础。

如图 9-4 所示，一个 PyTorch 自定义算子的落地方案大致包含三层工作：

- （1）[C++/CUDA] 多后端实现，完成 CPU、CUDA 等不同后端的具体计算代码。
- （2）[Python] 通过 Pybind 把算子暴露到 Python 侧，实现导入调用。
- （3）[PyTorch] 将算子注册进 PyTorch 生态，封装成 nn.Module 方便在模型中使用。

受篇幅限制，后续内容聚焦 CUDA 后端，逐步演示从实现到注册的完整路径。

#### 9.2.2 自定义算子的后端代码实现

先在 custom_sigmoid_cuda.cu 中定义 Sigmoid 的 CUDA 核函数：

[PRE0]

对 CUDA 不熟悉的读者可以先跳过细节。通俗地说，sigmoid_kernel 负责读取输入张量的显存地址，完成计算后再把结果写回输出张量的显存。模板写法是为了兼容不同数据类型。

头文件里会用到 torch/extension.h，它提供了 PyTorch 为自定义算子准备的一系列辅助接口和头文件，后续会逐步说明。核函数定义好后，还需要一层封装，使其能够接收并返回 torch::Tensor，也就是 PyTorch C++ 层的张量对象：

[PRE1]

这里的 custom_sigmoid_cuda_forward 主要负责把数据从输入张量取出，设定好 CUDA 线程数与线程块数后调用前面写的 sigmoid_kernel 对每个元素做 Sigmoid，再把结果填充到 output。AT_DISPATCH_FLOATING_TYPES 来自 torch/extension.h，它会依据输入张量的实际数值类型自动分发到对应的模板实例。

按同样思路把反向传播也补齐：

[PRE2]

#### 9.2.3 自定义算子导入 Python

到这里 Sigmoid 的 CUDA 后端已经实现，CPU 甚至其他后端也可以用类似方式补充。但最终要根据 Python 端 torch.device 的选择来决定调用哪套实现，因此需要一层分发逻辑。下方代码展示了一个简易的 CPU 后端实现，并根据输入张量的设备自动路由到 CUDA 或 CPU 版本：

[PRE3]

代码里用 custom_sigmoid_forward 与 custom_sigmoid_backward 做了一次薄封装，随后借助 PYBIND11_MODULE 将其注册到 Python 命名空间，方便在 Python 脚本中调用 C++ 侧的算子。

#### 9.2.4 自定义算子导入 PyTorch

要把算子真正跑在 PyTorch 里，还需要一个 setup.py 来完成编译并以 Python 模块的形式提供：

[PRE4]

编译自定义模块 custom_ops 的指令如下：

[PRE5]

随后可以把自定义的 Sigmoid 以 torch.nn.Module 的形式接入：

[PRE6]

此处利用 torch.autograd.Function 把自定义的前向与反向函数绑定起来，和 PyTorch 的自动微分系统无缝对接，在反向图中插入对应的自定义反向计算。

#### 9.2.5 在 Python 中使用自定义算子

完成以上注册流程后，调用方式就和原生算子一致，可以直接在 PyTorch 代码里使用并进行验证。

### 9.3 基于计算图的性能优化

第 3 章提到，深度学习模型本质上是由算子节点构成的计算图。但在 PyTorch 的动态图模式下，每次算子调用都会立刻执行，图的信息不会被长期保存，这就限制了很多全局优化的机会。那么怎样让 PyTorch 在真正执行前保留并优化整张图呢？社区曾尝试 torch.jit.trace、TorchScript、Lazy Tensor 等方案，它们在提速的同时也牺牲了动态图的便利性。为了兼顾性能与易用性，从 2.0 版本开始，PyTorch 正式引入了图优化工具 torch.compile。