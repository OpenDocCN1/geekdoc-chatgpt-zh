模型拉取与加载主要有三种常见做法：

(1) 借助 Hugging Face Transformers 实现自动下载与加载。

[PRE0]

首次使用时框架会自动把权重拉到本地缓存，默认位置是 ~/.cache/huggingface，后续再加载就会直接读取本地缓存。

(2) 使用 ModelScope 官方工具包自动加载。开始前需要先装好官方包。

[PRE1]

装完后运行以下代码就能把对应模型拉下来。

[PRE2]

具体的模型标识可以在官网页面上查到，如图2.3所示。

（3）从网页手动下载模型权重再离线加载。在 Hugging Face 与 ModelScope 的模型主页，切换到 Files and versions 或模型文件标签页，点击右侧的“下载”按钮把全部文件保存下来，如图 2.4 所示。把文件放到本地目录后，在推理脚本里把 pretrained_model_name_or_path 指向 “./your_model_dir” 即可。

这里可以看到权重文件的后缀是 .safetensors，对照表2.5可知这是安全张量格式，适配 Transformers、vLLM、TGI 这类部署方式。

### 2.3.3 Qwen3 本地部署与调用实操

Qwen3 完全基于 Transformers 开源，GitHub 项目里没有单独的 requirements.txt，说明它沿用了 Hugging Face 的依赖体系，一般只要执行下面的安装命令即可。

[PRE3]

#### 1. 获取 Qwen3 权重

想用 modelscope 拉取权重，先安装对应库。

[PRE4]

可以在网页端先预览权重列表，如图2.8所示。

接着导入 modelscope 库，执行下方命令完成下载。

[PRE5]

#### 2. Jupyter 中用 Transformers 原生库调用的流程

(1) 导入所需库。

[PRE6]

(2) 指定模型下载地址。

[PRE7]

(3) 实例化预训练模型与分词器。

[PRE8]

(4) 构造对话消息 message。

[PRE9]

(5) 完成词嵌入步骤。

[PRE10]

（6）生成模型回复。

[PRE11]

输出效果如图2.9所示。

是否开启思考模式由 enable_thinking 参数控制，默认 True，想关闭就改为 False 即可。

### 2.3.4 主流第三方部署工具盘点

除了直接用 Transformers 加载权重，开发者还可以选用工程化更强的第三方部署方案。这类工具在加载优化、推理加速、服务化、模型多租等方面优势明显，特别适合对性能、维护性和并发要求高的企业级场景。

##### 1. SGLang：智谱出品的 GLM 系列推理框架

SGLang 由智谱 AI 开源，专为 GLM 系列模型如 GLM-4、ChatGLM 设计，原生支持对话模版、多轮历史缓存、函数调用、结构化输出等。

核心特点：
- 与 GLM 架构高度契合，支持 multi-turn memory。
- 支持并发调度与动态上下文构建。
- 内置类似 OpenAI 的 API 接口。
- 可配合 fastapi/uvicorn 快速上线服务。

##### 2. LMDeploy：国产模型轻量部署方案

LMDeploy 由昆仑芯与魔搭社区联合打造，目标是为国产开源大模型提供统一简洁的高性能部署。它支持 INT4/INT8/FP16 等多种精度，并集成一键脚本、服务启动、QPS 压测等功能。

核心特点：
- 覆盖 Qwen、Baichuan、InternLM 等众多国产模型。
- 原生支持 INT4 推理，显存占用更低。
- 提供 CLI、Web UI、HTTP API 完整入口。
- 支持多线程调度与批量推理。

##### 3. vLLM：高并发 OpenAI 兼容的推理引擎

vLLM 来自 UC Berkeley，支持动态批量调度、连续批处理、FlashAttention 2，并实现了完全兼容 OpenAI API 协议的服务接口。

核心特点：
- 支持 Hugging Face 格式模型，如 LLaMA、Qwen、Baichuan 等。
- 推理吞吐高，可达数千 token/s。
- 支持流式输出。
- 原生兼容 chat/completions 接口规范。

##### 4. Ollama：开发者友好的本地一键部署平台

Ollama 面向开发者，支持下载和管理 GGUF 格式大模型，如 LLaMA 2/3、Mistral、Qwen、Phi 等，主张“本地部署像用 Docker 一样简单”。

核心特点：
- 自动下载注册模型，无需手动管理权重。
- 支持 Mac/Windows/Linux。
- 内置 HTTP API 与 CLI，调用便捷。
- 支持 CPU 与 GPU，支持流式输出。
- 基于 llama.cpp 生态，兼容 GGUF 格式。

以上工具覆盖了当前主流的本地大模型部署方案，能满足不同模型结构、硬件资源和应用场景的需求。本节对它们做了简要梳理，便于快速了解特性与用法。

##### 4. 加载模型并做本地推理验证

权重下载完成后，需要在本地加载并做一次简单推理，确认模型能否正常加载、显存是否够用、生成是否正常、框架是否兼容模型格式。

1）标准模型加载与推理测试（FP16）

若模型为 .bin 或 .safetensors 格式的标准权重且具备 GPU，可用如下方式加载推理。

[PRE12]

2）加载量化模型（如 INT4）

如果使用的是量化权重，例如 bitsandbytes 格式的 4bit INT4 模型，可采用以下方式。

[PRE13]

INT4 模型不支持 torch_dtype 参数，精度需通过 quantization_config 来管理。

3）官方推理脚本

除了自行用 Transformers 加载，大多数开源大模型在 GitHub 仓库中都会提供官方的推理示例脚本，帮助用户快速验证效果。

#### 2.3.3 GLM-4 本地部署全流程

智谱 AI 的 GLM-4 系列在社区表现突出，中文本地理解与生成能力强，官方还提供了完整的本地部署文档，适合在私有环境做推理测试与服务化。下面以 GLM-4-9B-Chat 为例演示从环境配置到部署的完整过程。

##### 1. 配置基础环境，创建虚拟环境

- 显卡建议：1 张 RTX 3090/4090，显存 ≥24GB；若用 INT4 量化版本，显存需求可降至 6GB~8GB。
- 操作系统：推荐 Linux，如 Ubuntu 20.04 或 Ubuntu 22.04。
- Python 环境：建议用 Conda 新建虚拟环境，Python 版本不低于 3.9。创建命令如下。

[PRE14]

随后安装必要的 torch 与 transformers。

[PRE15]

安装完成后可运行以下代码检查是否生效。

[PRE16]

若输出如下内容即可正常使用。

[PRE17]

##### 2. 从 GitHub 克隆开源项目

查看 GLM-4 模型的 GitHub 目录结构，如图2.5所示。

[PRE18]

项目中的 inference 子目录是本地部署与测试的核心，包含丰富的推理入口脚本。克隆仓库并进入目录的命令如下。

[PRE19]

根据图2.5 的结构可知，依赖清单 requirements.txt 位于 inference 子目录，进入后安装依赖。

[PRE20]

##### 3. 下载 GLM-4 模型权重

使用 modelscope 下载前需先安装官方库。

[PRE21]

可在网页端查看权重详情，如图2.6所示。

[PRE22]

图2.6 glm-4-9b-chat模型权重情况

导入 modelscope 库后运行下方代码下载权重。

[PRE23]

下载完成后打印 model_dir 即可看到本地存储路径。

[PRE24]

输出内容如下。

[PRE25]

##### 4. 使用 transformers 后端进行推理

（1）在单卡服务器下运行如下代码。