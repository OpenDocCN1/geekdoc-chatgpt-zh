Anaconda 属于 Python 生态里广受欢迎的数据科学与机器学习一体化平台，自带常用科学计算库并附带简便的包管理机制。接下来演示在 Windows 上完成部署的完整流程。

第一步，获取安装程序。前往官网下载 Anaconda 安装包，本案例采用 Anaconda3-2020.07-Windows-x86_64 这个版本。

第二步，执行安装。下载完毕后按向导提示完成部署。具体操作如下。

双击安装执行文件，在欢迎页点 Next 开始，如图 10-1 所示。

点 I Agree 同意许可协议，如图 10-2 所示。

[PRE0]

[PRE1]

在安装范围选择页勾选 Just Me 后继续点 Next，如图 10-3 所示。

点 Browse 指定安装目录再点 Next，如图 10-4 所示。

[PRE2]

[PRE3]

在高级选项里同时勾选 Add Anaconda3 to my PATH environment variable 与 Register Anaconda3 as my default Python 3.8，随后点 Install 启动部署，如图 10-5 所示。

第三步，校验部署。装好之后在开始菜单找到 Anaconda Prompt(anaconda3) 并打开，得到命令行界面，如图 10-6 所示。

在命令行输入 conda --version，若能返回版本号则说明部署 OK，如图 10-7 所示。

[PRE4]

[PRE5]

[PRE6]

图 10-7 中显示的 4.8.3 即 Anaconda 的版本号。

#### 2. PyCharm 安装

PyCharm 是 JetBrains 出品的专业 Python IDE，功能丰富能显著提升编码效率。下面演示安装流程。

首要任务是下载。打开 JetBrains 官网获取安装包，本次演示使用 pycharm-community-2022.1.2。

运行安装向导，下载完成后按提示完成部署。具体如下。

双击安装程序，在启动页点击 Next 开始，如图 10-8 所示。

点击 Browse 选择安装路径后再点 Next，如图 10-9 所示。

[PRE7]

[PRE8]

在配置页全选推荐选项后点 Next，如图 10-10 所示。

点 Install 进入安装环节，如图 10-11 所示。

[PRE9]

[PRE10]

点 Finish 完成安装，如图 10-12 所示。

[PRE11]

图 10-12

启动 PyCharm：部署完成后双击桌面图标即可打开。

### 10.2.2 环境搭建

本节介绍如何为微调 LLM 下游任务准备运行环境，涉及 Python 版本与依赖库。

#### 1. CUDA 环境安装

若要利用 GPU 加速深度学习微调，需先部署 CUDA。CUDA 由 NVIDIA 提供，用于发挥 GPU 并行计算能力。下面是部署步骤。

核查 GPU 兼容性。

首先在 Windows 打开运行对话框，输入 cmd 确定，调出命令提示符，如图 10-13 所示。

[PRE12]

图 10-13

随后在命令行执行 nvidia-smi，回车后会展示 GPU 配置，如图 10-14 和图 10-15 所示。

[PRE13]

图 10-14

[PRE14]

图 10-15

图 10-15 中的 CUDA Version 表示该显卡最高支持的 CUDA 版本。

下载适配的 CUDA Toolkit：所选版本须低于显卡最高支持版本。前往 NVIDIA 官网下载，如图 10-16 和图 10-17 所示。

[PRE15]

图 10-16

[PRE16]

图 10-17 里，Operating System 代表目标操作系统；Architecture 代表处理器指令集；Version 代表系统版本号；Installer Type 代表安装方式，可选本地或网络。

安装 CUDA Toolkit：拿下安装包后按向导安装，全部保持默认即可。

校验安装。装好后在命令行执行 nvcc -V，查看是否成功，如图 10-18 和图 10-19 所示。

[PRE17]

[PRE18]

若输出中出现 cuda_11.8.r11.8，则表示安装成功，其中 11.8 为 CUDA 版本号。

#### 2. PyTorch 环境搭建

LLM 下游任务通常依赖 Python 3.x 与 PyTorch 相关包，按下面步骤搭建。

打开 Anaconda Prompt(anaconda3) 命令行。执行 conda create -n agent python=3.11 新建虚拟环境，如图 10-20 所示。其中 agent 为环境名称，python 为目标 Python 版本。

[PRE19]

按提示输入 y 回车，继续创建，如图 10-21 所示。

[PRE20]

执行 conda activate agent 进入新建环境，如图 10-22 所示。

[PRE21]

访问 PyTorch 官网，顶部导航点 Get Started 进入 START LOCALLY 页面，如图 10-23 所示，点击 install previous versions of PyTorch 进入历史版本。选择 v2.0.1 版本，根据已安装 CUDA 版本复制对应命令 conda install pytorch==2.0.1 torchvision==0.15.2 torchaudio==2.0.2 pytorch-cuda=11.8 -c pytorch -c nvidia，如图 10-24 所示。

[PRE22]

图 10-23

[PRE23]

图 10-24

图 10-24 中的 Linux and Windows 为两系统通用命令。# CUDA 标注对应 CUDA 版本。

在 Anaconda Prompt(anaconda3) 中粘贴复制的命令回车开始安装，如图 10-25 所示。

[PRE24]

图 10-25

验证部署。安装结束后在 Anaconda Prompt(anaconda3) 输入 python，若显示 Python 3.11.8 之类版本号，则 Python 已就位，如图 10-26 所示。

[PRE25]

图 10-26

[PRE26]

随后输入 import torch 与 torch.cuda.is_available()，若返回 True，则 PyTorch 已正常工作，如图 10-27 所示。

图 10-27

#### 3. 安装 PEFT 运行环境并在 PyCharm 中进行配置

安装 PEFT 所需依赖。利用项目提供的 requirements.txt 进行安装。在 Anaconda Prompt(anaconda3) 输入 cd agent 进入文件所在目录，再执行 pip install -r requirements.txt 安装依赖，如图 10-28 所示。

继续执行 pip install fire 安装 fire 工具，如图 10-29 所示。

[PRE27]

[PRE28]

图 10-28

图 10-29

导入项目。

首先打开 PyCharm，选择 File → Open 导入项目，如图 10-30 所示。

[PRE29]

图 10-30

其次挑选案例项目，如图 10-31 所示。

[PRE30]

图 10-31

设置项目运行环境。

选择 File → Settings，如图 10-32 所示。

[PRE31]

图 10-32

在 Settings 左侧展开 Project:Llama-Agent-Chinese-m 节点，选择 Python Interpreter 选项，如图 10-33 所示。

[PRE32]

图 10-33

## 第 10 章 PEFT 微调实战——打造医疗领域 LLM

点 Settings 右上角齿轮图标，在下拉菜单选择 Add，如图 10-34 所示，进入 Add Python Interpreter 界面。

[PRE33]

图 10-34

在 Add Python Interpreter 界面勾选 Existing environment 并点 ... 图标，如图 10-35 所示。

[PRE34]

图 10-35

在弹出的 Select Python Interpreter 界面选择已搭建好的 PyTorch 环境中的 python.exe 并点 OK，如图 10-36 所示。

[PRE35]

至此，微调环境已全部就绪。

## 10.3 模型微调实战

### 10.3.1 模型微调整体流程

模型微调实战分为微调与推理两部分，微调流程如图 10-37 所示，推理流程如图 10-38 所示。

[PRE36]

[PRE37]

图 10-38

### 10.3.2 项目目录结构说明

Agent-Med-Chinese-main 工程目录：

- data 文件夹。

  - agent_data.json：用于微调的文本数据。

  - Infer.json：用于推理阶段的文本数据。

- lora-agent-med-finetune 文件夹。

  该目录用于存放微调过程中生成的模型权重，微调结束后模型保存于此以便后续推理或继续微调。

- templates 文件夹。

  - med_template.json：用于生成提示词的模板，微调时借助该模板辅助文本输出。

- finetune.py：执行模型微调的代码，包含加载预训练模型、加载微调数据、设置参数、执行训练循环、保存模型等。

- infer.py：执行推理的代码，包含加载已微调模型、准备推理数据、执行推理等功能。

整体目录结构如图 10-39 所示。

[PRE38]

图 10-39

### 10.3.3 基础模型选择

合适的基础模型对 LLM 下游任务至关重要。当前主流选择包括 Llama、Mistral、Qwen 与 Yi，可根据任务需求挑选。

#### 1. Llama

发布者：Meta。

特点：Llama 3 为最新版本，基于超过 15 万亿 token 预训练。现提供 80 亿与 700 亿参数两个版本，支持 8K 上下文窗口，在多项行业基准中表现领先。

适用场景：对话系统与聊天任务，尤其适合处理大量人类对话样本。

#### 2. Mistral

发布者：Mistral。

性能表现：Mistral 7B 在多项基准测试中超越 Llama 2 13B，并在多项指标上超过 Llama 1 34B，代码任务接近 CodeLlama 7B。

适用场景：文本生成、指令遵循与代码生成等，兼具性能与灵活性。

#### 3. Qwen

发布者：阿里云。

特点：基于 Transformer 架构，使用网络文本、书籍与代码等大量数据预训练。

适用场景：文本分类、命名实体识别、问答系统等自然语言处理任务。

#### 4. Yi

特点：基于高质量语料库训练的 LLM，兼顾英语与汉语，语料库包含 3 万亿令牌。

适用场景：涉及英语与汉语文本的各类自然语言处理任务，具有较强理解与生成能力。

### 10.3.4 微调数据集构建

构建微调数据集需准备微调数据、提示词模板与推理数据。

微调数据与推理数据的格式均为 JSON，包含以下字段。

- instruction：指令，即问题描述。

- input：输入，描述上下文相关信息。

- output：输出，描述期望结果。

例如：

[PRE39]

提示词模板用于生成模型输入，采用 JSON 格式，包含字段。

- description：描述模板用途。