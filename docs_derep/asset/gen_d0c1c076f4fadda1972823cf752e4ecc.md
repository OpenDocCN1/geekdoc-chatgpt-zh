# 昇腾大模型推理加速实战指南

ATB Models即Ascend Transformer Boost Models，是基于华为昇腾Transformer加速库的推理方案，专为Transformer架构模型打造。通过对矩阵乘法、注意力机制等核心算子的深度优化，它能让模型在昇腾AI处理器上跑得更快，同时控制能耗与成本。方案充分利用芯片的算力、存储带宽和内存带宽优势，借助MindIE即可直接完成ATB Models的推理调用。

##### 1. ATB Models 直接推理
环境准备：CANN、PyTorch、Torch-NPU与ATB Models需已完成安装。
使用限制：推理初始化失败且由用户自定义修改导致时，请手动结束进程；权重目录及文件权限必须正确，禁止其他用户写入。

以下以LLaMA2-7B为例演示对话推理、精度校验与性能压测流程。

（1）配置环境变量：
[PRE0]

（2）获取模型权重：可在Hugging Face官网下载对应权重，并保存至/data/Llama-2-7b-hf。

（3）（可选）ATB Models目前仅支持safetensor格式加载：若下载包已包含safetensor文件则无需转换；若只有bin格式，按以下方式转换：
[PRE1]
转换结果会存放在bin权重所在目录。

（4）对话推理测试：
[PRE2]
默认问句为“What's deep learning? ”，batch size为1，可自行调整输入。

（5）自定义推理内容：（4）中run_pa.sh是对run_pa.py的封装，默认问句与batch size同上。用户可直接调用run_pa.py并传参来自定义输入与推理方式。例如使用/data/Llama-2-7b-hf权重，以8卡运行，输入“What's deep learning? ”与“Hello World.”，batch size设为2。
[PRE3]
也支持传入Token id推理，其中“1, 15043, 2787”对应第一条输入，“1, 306, 626, 2691”对应第二条输入，多条输入以空格分隔。
[PRE4]

（6）性能测试：开启ATB_LLM_BENCHMARK_ENABLE后，可统计首Token、增量Token及端到端时延。
[PRE5]
耗时会在控制台输出，并写入benchmark.csv文件。

##### 2. ATB Models 服务化部署
将基于ATB加速库的模型以服务形式上线，可在实际业务中实现高效推理与预测。推理请求可通过HTTPS客户端如Linux curl或Postman发送。

（1）初始化环境变量，默认安装路径下可执行：
[PRE6]

（2）启动服务并发起请求。默认后端即为ATB Models：
[PRE7]
另开窗口使用HTTPS客户端发送请求，以curl为例：
[PRE8]

##### 3. MindSpore Models 服务化部署
MindSpore Models提供基于MindSpore框架的预训练模型与示例，覆盖视觉、NLP、推荐等领域，帮助开发者快速上手并完成训练与部署。MindSpore凭借自动微分、图算子等特性实现高效训推。MindIE LLM同时支持ATB与MindSpore后端，MindSpore Models对应MindFormers社区开源模型。使用MindSpore后端需已安装CANN与MindFormers，推理前须将权重转为MindFormers的ckpt格式，转换工具由MindFormers提供。

以Qwen1.5-72B为例，转换后权重目录结构如下：
[PRE9]
使用predict_qwen1_5_72b.yaml时需关注关键配置项：
[PRE10]
config.json可通过save_pretrained接口生成，示例：
[PRE11]

运行需配合服务化。默认安装路径下初始化环境变量：
[PRE12]
若本机已有其他卡的MindIE服务，请注意MS_SCHED_PORT避免冲突，日志报错时可更换端口后重启服务。

启用MindSpore后端时，需在服务配置中设置ModelDeployConfig.ModelConfig.backendType为“ms”，以确认为MindSpore后端。
[PRE13]
另开窗口通过HTTPS客户端发送请求，以curl为例：
[PRE14]
MindSpore场景下请求体的seed需在[0, 2^32)范围内，超出则按seed=0处理。

### 8.4 MindIE Service 统一部署
MindIE Service面向通用模型场景的推理服务化框架，采用开放可扩展架构提供服务化能力，兼容主流推理框架接口，满足大语言模型、文生图等多种模型的高性能推理需求。组件包括MindIE Server、MindIE Client、MindIE MS与MindIE Service Tools，架构如图8-11所示。
[PRE15]
图8-11
MindIE Server为推理服务端，支持命令行部署RESTful服务。内部包含EndPoint、GMIS、BackendManager：EndPoint提供RESTful接口并适配主流框架请求；GMIS负责模型推理调度与多实例管理；BackendManager为不同推理引擎与模型提供统一抽象。

MindIE Client是昇腾推理服务化客户端，与MindIE Server协同工作，完成通信协议对接、请求返回处理并向用户应用提供接入能力。

MindIE MS负责服务策略管理与运维。

MindIE Service Tools提供大模型推理性能测试、精度测试与可视化，并支持通过配置提升吞吐量。

#### 8.4.1 服务启动方式
MindIE Server可部署兼容Triton、OpenAI、TGI、vLLM等第三方框架接口的应用。建议启用HTTPS通信并配置服务证书、私钥等以保障安全。默认地址与端口为https://127.0.0.1:1025，可通过修改config.json中的ipAddress与port调整。此外还支持服务状态查询、模型信息查询、文本推理与流式推理。

（1）两种启动方式均需在/{MindIE安装目录}/latest/mindie-service目录下执行。
方式一：后台进程启动：
[PRE16]
后台启动后关闭窗口进程仍在运行。
标准输出捕获文件出现如下信息即表示启动成功：
[PRE17]
方式二：前台直接启动：
[PRE18]
出现如下回显即表示启动成功：
[PRE19]

(2)另开窗口使用HTTPS客户端发送请求，以curl为例：
[PRE20]

#### 8.4.2 接口调用