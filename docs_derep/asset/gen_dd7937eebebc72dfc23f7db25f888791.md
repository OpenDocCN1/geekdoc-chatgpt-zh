[PRE0]

克隆完成后，当前工作目录下会生成 Llama2-Chinese 的项目目录，具体结构可参考图2-2。

[PRE1]

### 2.2.2 下载模型

可以通过 Llama 中文社区获取 Llama 2 大模型。一种方式是按提供的下载链接手动拉取，另一种方式是借助 Hugging Face 直接加载平台上托管的权重。由于文件体积庞大，建议保持网络通畅并耐心等待。可根据表2-3 的说明挑选对应版本进行加载。

#### 表2-3. 模型信息

| 模型名称 | 模型加载名称 | 基础模型版本 |
| --- | --- | --- |
| Llama2-Chinese-7b-Chat | FlagAlpha/Llama2-Chinese-7b-Chat | meta-llama/Llama-2-7b-chat-hf |
| Llama2-Chinese-13b-Chat | FlagAlpha/Llama2-Chinese-13b-Chat | meta-llama/Llama-2-13b-chat-hf |
| Llama2-Chinese-7b-Chat-LoRA | FlagAlpha/Llama2-Chinese-7b-Chat-LoRA | meta-llama/Llama-2-7b-chat-hf |
| Llama2-Chinese-13b-Chat-LoRA | FlagAlpha/Llama2-Chinese-13b-Chat-LoRA | meta-llama/Llama-2-13b-chat-hf |

# 第2章 Llama 2 大模型部署实战

# Hugging Face 托管模型调用示例

[PRE2]

示例代码里向模型提问“如何向一个六岁的孩子解释什么是机器学习？”，用于评估推理能力。下面展示对应的生成结果：

[PRE3]

5.28s/it]

<s><unk> ：如何向一个六岁的孩子解释什么是机器学习？

</s><s>Assistant: 向一个六岁的孩子解释什么是机器学习可能需要用简单的语言和例子来说明。你可以这样解释:

"你知道电脑吗？它们就像是很聪明的机器，可以帮助我们做很多事情。机器学习就是让电脑变得更聪明的一种方法。就像我们学习新东西一样，电脑也可以通过机器学习来学习新东西。

想象一下，如果你给电脑看一堆图片，有一些是猫的图片，还有一些是狗的图片。一开始，电脑不知道怎么区分猫和狗，但是通过机器学习，它可以学会看到一些特别的东西，比如猫的耳朵形状或者狗的尾巴形状。然后，当你给电脑看一个新的图片时，它可以用之前学到的知识来猜测这是一只猫还是一只狗。

这就像是你学习认识新朋友一样。一开始你可能不知道他们的名字，但是通过和他们交谈，你会了解到一些特别的东西，比如他们的笑容或者他们的声音。然后，当你再见到他们时，你可以通过这些特征来认出他们是谁。

所以，机器学习就是让电脑通过观察和学习，变得更聪明和有能力做一些事情，就像我们通过观察和学习来认识新朋友一样。

希望这个解释能帮助你向一个六岁的孩子解释什么是机器学习！

若本地运行 demo 程序也能得到类似的推理输出，则说明环境已可正常运行大模型项目。到此 Llama 2 的基础部署与测试完成，接下来介绍其他部署方式。

## 2.3 模型部署

大模型推理时通常需要一个简洁的交互界面。本节介绍两种本地部署方案，即 API 部署和 text-generation-webui 部署，两种方式都适用于本地运行，可根据设备条件和个人喜好选择。

### 2.3.1 API 部署

API 部署是较为常见的部署方式，通过网络请求与模型进行交互。本节使用 FastAPI 和 Uvicorn 两个库搭建服务。

部署前需先安装依赖，这两个库均可通过 pip 安装：

Plaintext

pip install fastapi uvicorn

安装完成后，可运行仓库中的 `accelerate_server.py` 脚本启动 API 服务：

[PRE4]

该命令包含以下参数：

+ `model_path`: 指定模型在本地的路径，需要替换为实际的 Llama 2 模型文件位置。
+ `gpus`: 指定使用的显卡编号，单卡使用填“0”，双卡同时使用填“0,1”。
+ `infer_dtype`: 指定模型加载后的数据类型，可选 int4、int8、float16，会影响运行效率和精度。
+ `model_source`: 指定模型来源，可选 llama2_chinese 和 llama2_meta，需按下载来源选择，例如 FlagAlpha 的模型应设为 “llama2_chinese”。

启动后 API 服务默认部署在本地 8001 端口，通过 POST 方法调用。可使用 `accelerate_client.py` 脚本向服务发送请求：

[PRE5]

该脚本会向 API 服务发出请求并打印响应，用户可自行修改以发送自定义请求。

### 2.3.2 text-generation-webui 部署

text-generation-webui 是基于 Gradio 开发的可视化工具包，用于大模型的会话交互。其主要特性如下：

+ 支持三种交互模式：分列模式、Notebook 模式、聊天模式。
+ 支持多种模型后端：Transformers、llama.cpp、ExLlama、AutoGPTQ、GPTQ-for-LLaMa、CTransformers 等。
+ 提供模型快速切换的下拉菜单。
+ LoRA 支持即时加载与卸载，并可使用 QLoRA 训练新 LoRA。
+ 提供精确的聊天指令模板：Llama2-Chat、Alpaca、Vicuna、WizardLM、StableLM 等。
+ 支持 Transformers 库的 4 位、8 位及 CPU 推理。
+ 支持 Transformers 采样器的 llama.cpp 模型，加载器为 llamacpp_HF。
+ 支持多模态管道：LLaVA 与 MiniGPT-4。
+ 支持带 LaTeX 渲染的 Markdown 输出，可与 Galactica 等配合使用。
+ 支持自定义聊天角色。
+ 提供高效的文本流式输出。

## Llama 大模型实践指南

+ 支持 Transformers 库进行 4 位、8 位及 CPU 推理。
+ 支持 Transformers 采样器的 llama.cpp 模型（llamacpp_HF 加载器）。
+ 多模态管道：LLaVA 和 MiniGPT-4。
+ 支持 LaTeX 渲染的 Markdown 输出，如可以与 Galactica 一起使用。
+ 自定义聊天角色。
+ 高效的文本流式处理。

1. 直接安装 text-generation-webui

text-generation-webui 对硬件要求较低，CPU 或 6GB 显存显卡即可安装，安装流程也较为简便。

(1) 打开 text-generation-webui 的项目页面，如图 2-3 所示。

[PRE6]

图 2-3 text-generation-webui 的项目页面

### 2.3.3 使用 text-generation-webui

1. 准备模型权重

首先，将下载好的 LoRA 权重和 Hugging Face 格式的 llama-7B 模型权重分别放入 loras、models 文件夹，目录结构如下：

[PRE7]

[PRE8]

[PRE9]

[PRE10]

[PRE11]

[PRE12]

[PRE13]

其次，将 LoRA 权重的 tokenizer.model 复制到 models/llama-7b-hf 目录下，并修改 `/modules/LoRA.py` 文件，text-generation-webui 默认从 models 目录加载 tokenizer.model，因此需要使用扩充中文词表的 tokenizer.model 并修改原 llama-7b-hf 的 embed_size：

[PRE14]

[PRE15]

### 2.3.3 使用 text-generation-webui

2. 加载模型并启动 text-generation-webui

执行以下命令即可开始与 Llama 2 大模型对话：

[PRE16]

对话界面截图如图 2-6 所示。

[PRE17]

图 2-6 对话截图

## 第 3 章 微调 Llama 2 大模型

预训练完成后，大模型具备完成各类任务的通用能力。大量研究表明，通过针对特定目标的微调可进一步提升能力。微调能让模型适配特定任务，提升性能与效率，使其更懂特定领域的语言术语，预测更准确，也能更好地适应不同数据集和任务，提升泛化与鲁棒性。因此微调已成为大模型应用的关键环节。

目前主流的微调技术主要分为两种：

+ 指令微调：旨在提升模型的能力。
+ 对齐微调：旨在让模型行为更符合人类价值观或偏好。

OpenAI 的 ChatGPT 就应用了微调技术取得优异效果。本章重点介绍指令微调。

指令微调是在自然语言格式的实例集合上对预训练模型进行微调的技术，与有监督微调和多任务 Prompt 训练密切相关。实施步骤首先是收集或构建指令格式数据，来源包括人工标注、自动生成或从已有数据抽取；其次以有监督方式用格式化实例训练模型，如使用序列到序列损失进行训练。

以 Llama 2 为代表的开源大模型经指令微调后泛化能力极强，即使在多语言场景也表现良好。泛化能力使其可适配多种任务和数据集，提升性能与效率，同时帮助模型更好地理解特定领域语言和术语，提高预测准确性和可靠性。因此指令微调已成为大模型落地的必备环节。

## 3.1 微调的数据集准备和标注

在当前 AI 领域，开源大模型如 Llama 2 已在各类 NLP 任务中取得显著成果。为发挥其潜力，往往需要微调以适配特定应用场景，其中数据集准备和标注至关重要。本节以 Llama 2 为例介绍相关流程。

### 3.1.1 数据集准备

一个指令格式实例通常包含任务描述即指令、输入和输出。指令说明模型需完成的任务，输入为待处理数据，输出为预测结果。此类格式有助于模型理解任务需求并提升表现。

数据集构建主要有三种途径：

+ 格式化已有数据集。将传统 NLP 数据集按特定格式调整用于指令微调。为降低成本，可用 ChatGPT 生成指令，常用 Prompt 为“请为这段内容生成一个合理的问题”，用户据此交互生成符合要求的数据。
+ 人工标注数据集。此类数据更贴近人类语言习惯，对齐效果更好。为降低成本，多数数据集由 ChatGPT 生成，包括用户分享的 ChatGPT 对话历史如 ShareGPT 数据集，以及用 ChatGPT 蒸馏生成的数据集。
+ 复用开源数据集。复用开源数据以增加任务多样性，提升模型性能。

数据集可分为通用和专用。通用数据集目前开源资源丰富，可训练模型获得通用任务处理能力；专用数据集针对特定领域如法律、医疗等，需按需求自行构建，以满足领域任务的理解与处理。通过专用数据可提供更精准定制化的指令，提升领域性能。

除常规数据集外，引入多样性数据如 CoT 也能有效提升性能，帮助模型更好地理解复杂语言结构和推理任务，从而增强泛化和性能。

因此综合使用通用、专用及多样性数据集已被广泛采用并取得良好效果。

一般而言，获取数据集后还需进行后续处理以获得高质量数据。

### 3.1.2 数据集标注

通过上述方法获得数据后，还需将其转换为大模型特定的输入格式，处理方式依模型而异。以 Llama 2 为例，通常将数据处理为 json 或 csv 格式文件，典型格式如下：

[PRE18]

## Llama 2大模型的指令微调格式示例如下：