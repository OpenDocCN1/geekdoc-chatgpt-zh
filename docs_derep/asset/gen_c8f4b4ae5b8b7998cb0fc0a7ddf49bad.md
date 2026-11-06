在本书围绕Qwen3、DeepSeek等大模型展开的实践篇章里，PyTorch充当核心后端，负责模型导入、推理运行、参数微调乃至分布式训练等关键环节。LLM要实现高效部署和精细化微调，很大程度上依赖PyTorch提供的张量运算能力、自动微分机制以及对CPU/GPU的统一调度。因此，把PyTorch环境正确搭好，是后续所有工作的起点。

安装层面，PyTorch提供CPU版和GPU版两条路线，兼顾不同硬件条件和性能需求。

- CPU版本：适合开发调试、轻量推理或没有GPU资源的场景，安装简便，兼容性好。
- GPU版本：能够调用NVIDIA CUDA加速，显著拉高训练和推理速度，尤其适合Qwen3这类大规模模型的落地与调优。

以Windows为例，视读者机器资源而定，若要装仅限GPU的PyTorch版本，以PyTorch 2.8.0 CUDA12.6为例，执行命令如下：

[PRE0]

相比之下，GPU版的配置要牵涉到CUDA驱动、cuDNN等底层依赖的版本匹配，复杂度更高。考虑到Qwen3对算力的较高要求，建议在拥有NVIDIA显卡的环境中优先安装GPU版PyTorch，以发挥并行计算优势，加速训练与推理。接下来重点说明GPU版的安装流程和常见问题处置。

1. 核心概念理解

动手前，先把三者的关系理清十分关键。

(1) CUDA：NVIDIA推出的并行计算平台与编程模型，让开发者能用支持CUDA的GPU进行通用计算，而不仅限于图形。简言之，PyTorch要在NVIDIA GPU上运算，需要CUDA作为桥梁。

(2) cuDNN：即CUDA Deep Neural Network library，是NVIDIA针对深度神经网络的GPU加速库，提供卷积、池化、归一化等常用操作的高度优化实现。cuDNN构建在CUDA之上，是一个专用加速层，PyTorch等框架会调用它来极致提升训练与推理效率。

(3) PyTorch：开源机器学习框架本身不直接与GPU对话，而是通过CUDA接口利用GPU算力。安装了CUDA版PyTorch后，框架即可借助CUDA和cuDNN在NVIDIA GPU上高效运行。

调用链路可概括为：PyTorch→cuDNN→CUDA→NVIDIA GPU Driver→NVIDIA GPU。

因此安装顺序应为：先装GPU驱动，再装CUDA与cuDNN，最后装对应版本的PyTorch。

2. 安装前的准备工作

1) 核查NVIDIA GPU是否支持CUDA

确认电脑配备NVIDIA显卡且支持CUDA。几乎所有现代NVIDIA GPU，如GeForce、RTX、Quadro、Tesla等，均已支持。

官方支持清单见：https://developer.nvidia.com/cuda-gpus。

2) 查看显卡驱动版本

这一步用于确定可安装的CUDA版本。

(1) 按Win+R，输入cmd回车打开命令提示符。
(2) 输入命令：nvidia-smi。
(3) 查看右上角的驱动版本与最高支持的CUDA版本。

例如输出中出现CUDA Version:12.4，表示当前驱动最高可支持CUDA 12.4，需安装不高于12.4的CUDA版本。

3. CUDA安装

CUDA是NVIDIA的并行计算平台，PyTorch的GPU加速依赖于它。

1) 检查GPU是否支持CUDA

访问NVIDIA官网核对GPU型号，或在命令行执行：wmic path win32_VideoController get name 来查看。

2）下载CUDA Toolkit

- 访问NVIDIA CUDA下载归档页：https://developer.nvidia.com/cuda-toolkit-archive。
- 选择适合系统的版本，建议选PyTorch兼容的版本，目前常用11.7或11.8。
- 按系统类型挑选安装包。

3）安装CUDA

运行下载的.exe，建议采用默认安装路径。安装时务必勾选CUDA Toolkit。

4）验证安装

命令行输入：nvcc -V，若能显示版本信息，则安装成功。

4. cuDNN安装

cuDNN是NVIDIA的深度神经网络加速库，是PyTorch GPU加速的必要组件。

1）下载cuDNN

(1) 访问：https://developer.nvidia.com/rdp/cudnn-download。
(2) 需要注册NVIDIA开发者账号。
(3) 选择与已装CUDA版本相匹配的cuDNN版本。

2）安装cuDNN

解压ZIP包，将bin、include、lib文件夹拷贝到CUDA安装目录，默认路径为：C:\Program Files\ NVIDIA GPU Computing Toolkit\CUDA\vxx.x。

3）验证安装

查看CUDA安装目录下的cudnn_version.h文件确认版本。

5. PyTorch安装

1）推荐使用Anaconda环境，可选但建议

(1) 安装Anaconda：https://www.anaconda.com/products/distribution。
(2) 打开Anaconda Prompt，创建虚拟环境：

[PRE1]

2）安装PyTorch

(1) 访问PyTorch官网：https://pytorch.org/。
(2) 按系统与CUDA版本选择对应安装命令。
(3) 常用命令示例，以PyTorch 2.6.0 + CUDA 11.8为例：

[PRE2]

3) 验证PyTorch安装

打开Python终端，执行以下代码：

[PRE3]

若无报错且torch.cuda.is_available()返回True，则安装成功。

6. 常见问题解决

- CUDA版本不匹配：确保PyTorch与CUDA版本兼容。
- 权限问题：Linux安装时可能需要sudo。
- PATH环境变量：确认CUDA安装路径已加入系统PATH。
- 驱动问题：确保NVIDIA显卡驱动为最新。

若电脑无NVIDIA显卡，可安装CPU版PyTorch，在PyTorch官网选择CPU对应的安装命令即可。

### 2.1.3 PyCharm的安装与使用

同类语言的程序同样可以用Windows自带编辑器编写，但面对工程复杂的项目时，容易混淆层级与关联文件，所以编写Python工程时建议使用专用IDE PyCharm。

PyCharm安装较为简便，本节不作展开。读者可自行搜索PyCharm官网，进入Download页面，选择适配操作系统的版本安装即可。PyCharm下载页示意如图2.4所示。

[PRE4]

图2.4 下载PyCharm

安装完成后，可在PyCharm中打开本书配套代码，并在Anaconda Prompt搭建虚拟环境。配套代码可在虚拟环境中运行，如图2.5所示。

[PRE5]

图2.5 在PyCharm中设置代码虚拟运行环境

## 2.2 LLM的调用与使用

本节以ModelScope魔搭社区中的Qwen3大模型为例，介绍LLM的调用与使用方法。

### 2.2.1 ModelScope（魔搭社区）

ModelScope由阿里巴巴牵头，联合多家国内顶尖科研机构与高校推出，是一个面向“模型即服务”MaaS的共享平台。核心目标是降低AI模型应用门槛，促进开源模型的共享、协作与创新。

1. 核心定位与愿景

ModelScope的愿景是成为AI模型领域的GitHub。如同GitHub托管代码，ModelScope致力于托管、分享与运行AI模型。它不仅是模型仓库，更提供从模型探索、体验、微调到部署的一站式生态。

主要用户包括：

- AI研究人员：发布与验证自有模型。
- 应用开发者：快速找到并集成现成模型，无需从零训练。
- 学生与爱好者：学习前沿AI技术并动手实践。
- 企业用户：寻找商业化方案，降低研发成本。

2. 主要功能与核心组成部分

ModelScope提供从模型探索、体验、部署到再开发的全链路服务。

1）庞大的模型库

（1）汇聚阿里巴巴、清华大学、北京大学、浙江大学、商汤科技、澜舟科技等顶尖实验室与高校的开源模型。
（2）覆盖范围广：自然语言处理、计算机视觉、语音识别与合成、多模态、科学计算等。
（3）模型类型多样：从基础图像分类、文本Embedding，到大语言模型如Qwen、Baichuan，文生图模型如Taiyi、SD，语音合成模型等。

2）在线体验与Demo

几乎每个模型都提供在线试玩Playground功能。用户无需本地安装环境，直接在网页上传图片、输入文本或录音，即可立即看到推理效果，大幅降低体验门槛。

3）Notebook开发环境

平台提供集成好的云端免费Jupyter Notebook开发环境，基于PAI-DSW，预装ModelScope SDK及常用依赖。用户可在浏览器中直接打开Notebook，编写少量代码即可加载运行模型，进行原型开发与实验。

4）ModelScope Library（Python SDK）

这是ModelScope的核心组件，一个开源Python库。开发者可用极简代码，通常2～3行，即可在本地或云端下载、加载、推理模型。

示例代码如下：

[PRE6]

5）ModelScope Studio

类似Hugging Face Spaces，ModelScope Studio即创空间，是低代码/无代码的应用构建平台。用户可借助Gradio、Streamlit等框架，快速为模型搭建界面友好演示应用，并一键部署分享。

6）数据集与学习资源

（1）平台除模型外，还提供配套的训练与评测数据集。
（2）包含丰富的教程、文档、技术博客与视频，助用户从入门到精通。

### 2.2.2 Qwen3的本地调用

开展大模型应用开发时，可通过ModelScope官网模型库检索，例如搜索Qwen3关键词，结果如图2.6所示。

图示中可找到多个Qwen3变体。点击“通义千问3-32B”进入模型介绍页，如图2.7所示。

[PRE7]

[PRE8]

图2.7 “通义千问3-32B”标签界面

页面对“通义千问3-32B”进行介绍，下拉左侧说明框可看到基于ModelScope本地模型的读取与处理示例。

需注意，Qwen3代码已纳入最新版Hugging Face Transformers，建议使用最新版本。若使用Transformers低于4.51.0，可能出现KeyError: 'qwen3'错误。

【示例2.1】基于ModelScope本地模型的读取与处理，本示例演示如何根据给定输入使用模型生成内容，代码如下：

[PRE9]

[PRE10]

该调用对内存与算力消耗较大，结果暂不展示。部署时可使用sglang>=0.4.6.post1或vllm>=0.8.5创建OpenAI兼容的API端点。

- SGLang:

[PRE11]

- vLLM:

[PRE12]

上述代码展示了用ModelScope的AutoModelForCausalLM与AutoTokenizer加载Qwen3-1.7B并进行推理的完整流程。若使用更大模型如Qwen3-32B，实际运行需A100、H100等更高性能GPU。

本地运行方面，Ollama、LMStudio、MLX-LM、llama.cpp和KTransformers等应用均支持Qwen3，读者可按需选择。

### 2.2.3 Qwen3的在线调用