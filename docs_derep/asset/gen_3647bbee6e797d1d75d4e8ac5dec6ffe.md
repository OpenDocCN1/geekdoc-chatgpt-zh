Anaconda 是数据科学与机器学习领域常用的一体化 Python 环境，内置大量常用库并提供易用的包管理。下面说明部署 Anaconda 的具体做法。

（1）获取安装包：进入 Anaconda 官网进行下载。本案例采用的发行版是 Anaconda3-2020.07-Windows-x86_64。

（2）执行安装：下载完成后按向导提示完成安装。具体操作如下。

运行下载好的安装程序，在欢迎界面点击 “Next” 启动安装，如图 10-1 所示。

点击 “I Agree” 同意最终用户许可协议，如图 10-2 所示。

[PRE0]

[PRE1]

选中 “Just Me” 单选按钮后点击 “Next” 继续，如图 10-3 所示。

点击 “Browse” 按钮指定安装目录后点击 “Next”，如图 10-4 所示。

[PRE2]

[PRE3]

分别勾选 “Add Anaconda3 to my PATH environment variable” 与 “Register Anaconda3 as my default Python 3.8” 两项后点击 “Install” 开始安装，如图 10-5 所示。

（3）验证安装：安装结束后在 Windows 开始菜单中找到 “Anaconda Prompt(anaconda3)” 图标并打开，得到命令行界面，如图 10-6 所示。

在命令行中输入 “conda --version” ，若显示版本号则说明安装成功，如图 10-7 所示。

[PRE4]

[PRE5]

[PRE6]

图 10-7 中出现的 4.8.3 即为 Anaconda 的版本号。

#### 2. PyCharm 安装

PyCharm 是一款功能完善的 Python 集成开发环境，能够提供丰富工具提升编码效率。下面给出安装步骤。

（1）下载 PyCharm：前往 JetBrains 官网获取安装包。本案例使用版本为 pycharm-community-2022.1.2。

（2）安装 PyCharm：下载后按向导指示完成安装。具体如下。

双击下载完成的安装程序，在向导界面点击“Next”开始，如图 10-8 所示。

点击“Browse”选择安装路径后再点击“Next”，如图 10-9 所示。

[PRE7]

[PRE8]

在配置界面勾选全部配置选项后点击“Next”，如图 10-10 所示。

点击“Install”按钮执行安装，如图 10-11 所示。

[PRE9]

[PRE10]

点击“Finish”按钮结束 PyCharm 安装，如图 10-12 所示。

[PRE11]

图 10-12

（3）启动 PyCharm：安装完成后可双击启动图标打开 PyCharm。

### 10.2.2 环境搭建

本节介绍搭建微调 LLM 下游任务所需环境的流程，涵盖 Python 版本与必备库。

#### 1. CUDA 环境安装

若计划使用 GPU 加速深度学习模型微调，则需部署 CUDA。CUDA 由 NVIDIA 提供，用于调动 GPU 并行计算能力。下面是安装步骤。

（1）检查 GPU 兼容性。

首先在 Windows 中打开“运行”对话框，输入“cmd”并确定，打开命令提示符，如图 10-13 所示。

[PRE12]

图 10-13

随后在命令行中输入“nvidia-smi”回车，可查看设备 GPU 配置信息，如图 10-14 和图 10-15 所示。

[PRE13]

图 10-14

[PRE14]

图 10-15

图 10-15 中的“CUDA Version”表示该 GPU 所能支持的最高 CUDA 版本。

（2）下载适配 GPU 的 CUDA Toolkit：所选版本需低于 GPU 最大支持的 CUDA 版本。访问 NVIDIA 官网下载，如图 10-16 和图 10-17 所示。

[PRE15]

图 10-16

[PRE16]

图 10-17 中，“Operating System”表示目标操作系统类型；“Architecture”表示处理器指令集架构；“Version”表示系统版本号；“Installer Type”表示安装方式，可选本地或网络类型。

（3）安装 CUDA Toolkit：下载后按向导进行安装，全部保持默认设置即可。

（4）验证安装：完成后在命令行输入“nvcc -V”检查是否成功，如图 10-18 和图 10-19 所示。

[PRE17]

[PRE18]

若输出信息中出现“cuda_11.8.r11.8”，则表示安装成功，其中 11.8 为 CUDA 版本号。

#### 2. PyTorch 环境搭建

LLM 下游任务通常依赖 Python 3.x 与 PyTorch 相关包，按以下步骤搭建。

（1）打开 “Anaconda Prompt(anaconda3)” 命令行。执行 “conda create -n agent python=3.11” 新建环境，如图 10-20 所示。图中 “agent” 为环境名，“python” 为待安装的 Python 版本。

[PRE19]

按提示输入 “y” 回车，继续创建环境，如图 10-21 所示。

[PRE20]

（2）执行 “conda activate agent” 进入新建环境，如图 10-22 所示。

[PRE21]

（3）登录 PyTorch 官网，选择导航栏 “Get Started” 进入 “START LOCALLY” 页面，如图 10-23 所示，点击链接 “install previous versions of PyTorch” 进入历史版本选择。选用 v2.0.1 版本，根据已安装的 CUDA 版本复制对应安装命令 “conda install pytorch==2.0.1 torchvision==0.15.2 torchaudio==2.0.2 pytorch-cuda=11.8 -c pytorch -c nvidia”，如图 10-24 所示。

[PRE22]

图 10-23

[PRE23]

图 10-24

图 10-24 中的 “Linux and Windows” 为两系统安装命令。“# CUDA” 标识了对应的 CUDA 版本。

在 “Anaconda Prompt(anaconda3)” 中粘贴复制的安装命令回车开始安装，如图 10-25 所示。

[PRE24]

图 10-25

（4）验证安装。安装完成后在 “Anaconda Prompt(anaconda3)” 中输入 “python” ，若显示 Python 版本号如 “Python 3.11.8”，则 Python 安装成功，如图 10-26 所示。

[PRE25]

图 10-26

[PRE26]

继续输入 “import torch” 与 “torch.cuda.is_available()”，若返回 “True”，则 PyTorch 安装成功，如图 10-27 所示。

图 10-27

#### 3. 安装 PEFT 运行环境并在 PyCharm 中进行配置

（1）安装 PEFT 所需包。利用项目中提供的 requirements.txt 进行安装。在 “Anaconda Prompt(anaconda3)” 中执行 “cd agent” 进入文件所在目录，再执行 “pip install -r requirements.txt” 安装 PEFT 依赖，如图 10-28 所示。

继续执行 “pip install fire” 安装 fire 包，如图 10-29 所示。

[PRE27]

[PRE28]

图 10-28

图 10-29

（2）导入项目。

首先打开 PyCharm，选择 “File” → “Open” 导入项目，如图 10-30 所示。

[PRE29]

图 10-30

其次选择案例项目，如图 10-31 所示。

[PRE30]

图 10-31

（3）设置项目开发运行环境。

选择“File” → “Settings”，如图 10-32 所示。

[PRE31]

图 10-32

在“Settings”左侧选择“Project:Llama-Agent-Chinese-m”节点下的“Python Interpreter”选项，如图 10-33 所示。

[PRE32]

图 10-33

## 第 10 章 PEFT 微调实战——打造医疗领域 LLM

点击“Settings”右上角“齿轮”图标，在下拉列表中选择“Add”，如图 10-34 所示，进入“Add Python Interpreter”界面。

[PRE33]

图 10-34

在“Add Python Interpreter”界面中勾选“Existing environment”并点击“...”图标，如图 10-35 所示。

[PRE34]

图 10-35

在弹出的 “Select Python Interpreter” 界面中选择已完成的 PyTorch 环境中的 “python.exe” 文件并点击 “OK”，如图 10-36 所示。

[PRE35]

至此，微调环境全部搭建完成。

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

  用于存放微调过程中生成的模型权重，微调结束后模型保存在此处以便后续推理或继续微调。

- templates 文件夹。

  - med_template.json：用于生成提示词的模板，微调时借助该模板辅助文本输出。

- finetune.py：执行模型微调的代码，包含加载预训练模型、加载微调数据、设置参数、执行训练循环、保存模型等。

- infer.py：执行推理的代码，包含加载已微调模型、准备推理数据、执行推理等功能。

整体目录结构如图 10-39 所示。

[PRE38]

图 10-39

### 10.3.3 基础模型选择

合适的预训练模型对 LLM 下游任务至关重要。当前主流选择包括 Llama、Mistral、Qwen 与 Yi，可根据任务需求挑选。

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