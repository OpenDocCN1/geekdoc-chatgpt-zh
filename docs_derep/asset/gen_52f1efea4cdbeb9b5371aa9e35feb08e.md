QLoRA 的核心在于叠加三项技术，把 65B 级大模型的微调显存从超过 780GB 压缩到不足 48GB，同时仍能达到 16bit 微调的水平。对个人开发者而言，这就意味着 100 亿参数量级的模型也能用一套靠谱、成本可控的方案跑起来，否则动辄需要堆叠多张显卡才能动大模型。

6.3 节刻意没有给出实现步骤和实验结果，是为了避免重复，相关内容会放到第 10 章统一展开。

## 6.4 量化技术

### 6.4.1 背景
量化就是拿更少的比特去表达数据，最大价值在于压低内存占用并提速推理，对大语言模型尤其明显。目前 Transformers 生态里常见的量化手段主要有：

- 使用 AWQ，即激活感知权重量化，针对模型权重做优化量化。
- 使用 GPTQ，对生成式预训练 Transformer 的权重矩阵逐行独立量化，以追求精度。
- 调用 BitsAndBytes 库把模型量化到 8bit 或 4bit 精度。
- 使用 AQLM 算法实现 2bit 量化。

第 4 章直接训练亿级模型时就已碰到显存不足的问题，有时几轮 epoch 就要耗掉几十小时。一个 epoch 指用训练集全部样本走完一次训练。于是迫切需要能在有限 GPU 上完成全量微调的办法，量化是其中最关键的一环。量化的本质是用更少信息表征数据并尽量保住准确度，同时带来推理加速。

### 6.4.2 量化技术分类
按执行时机可分成两类。

- 微调完成后量化。模型权重已训练完毕，为部署推理而压缩，目标是让在大硬件上训好的模型能在小设备上跑。代表做法是 GPTQ 和 AWQ。
- 微调过程中量化。训练时就以低精度存储参数，目标是在小硬件上完成微调。典型做法是 BitsAndBytes 库，常与 QLoRA 配合实现量化微调。

第一类属于先训后压，更像推理成本的优化，对大模型微调帮助有限。第二类是在训练循环里直接用低精度，既能省显存也能在自有数据上低成本训练。先微调再量化还是先量化再微调没有绝对优劣，资源充足时通常先完整微调效果更稳。

Transformers 的 Quantization 模块已集成 BitsAndBytes，使用门槛很低，可以先用 NF4 类型保存大模型参数，再结合 QLoRA 完成低成本微调。下面说明在 Transformers 中的具体用法。

### 6.4.3 BitsAndBytes库
BitsAndBytes 是对 CUDA 自定义函数的轻量封装，重点优化了 8bit 优化器、矩阵乘法与量化函数，在硬件层支持混合精度分解和 int8 推理。借助它，Transformers 中做 int8、int4 量化是最直接的路径。将 QLoRA 与 NF4 结合，就能在训练阶段持续以低精度进行微调。

### 6.4.4 实现步骤
通过与 Transformers 的集成，BitsAndBytes 能把模型量化到 8bit 或 4bit，并借助 BitsAndBytesConfig 开启更多选项，例如：

- load_in_4bit=True：加载时直接量化为 4bit。
- bnb_4bit_quant_type="nf4"：对正态分布初始化的权重使用 NF4 这类特殊的 4bit 数据类型。
- bnb_4bit_use_double_quant=True：对已经量化的权重再做嵌套量化。
- bnb_4bit_compute_dtype=torch.bfloat16：计算时用 bfloat16 加速。

用 int4 量化加载模型：本质上在 from_pretrained 中传参即可。load_in_4bit=True 对应 4bit，load_in_8bit=True 对应 8bit，开启后模型会按对应精度进入内存。

代码6-9

[PRE0]

用 NF4 精度加载模型：NF4 的意义在于对接 PEFT 中的 QLoRA。单纯 int4 量化可直接传参，若要启用 NF4 需要先构造 BitsAndBytesConfig，在其中指定 bnb_4bit_quant_type="nf4"。4bit 量化有多种子类型，这里选择 NF4。

代码6-10

[PRE1]

代码6-11 演示双量化加载。QLoRA 论文提到双量化，只需在 BitsAndBytesConfig 中额外加上 bnb_4bit_use_double_quant=True 即可。

代码6-11

[PRE2]

代码6-12 把 QLoRA 的四项经典设置合在一起：混合精度加载、BF16 计算、bnb_4bit_quant_type="nf4"、开启 double_quant。

代码6-12

[PRE3]

量化模型在进入训练前需要预处理。调用 prepare_model_for_kbit_training 即可完成准备。背后的原因会在第 10 章 10.1 节结合 QLoRA 详细解释，这里先把它看作固定步骤。

代码6-13

[PRE4]

预处理后的量化模型可以直接进入训练，具体 QLoRA 流程参见第 10 章。

### 6.4.5 实验结果
图 6-7 对比了不同规模模型、不同数据类型在 MMLU 上的微调表现。蓝色线为 Float 基线，橙色线为论文提出的 Normal Float，即 NFloat，绿色线为 Normal+DQ，其中 DQ 指 Double Quantization 双量化。QLoRA 在 NFloat4+DQ 配置下能与 Lora BFloat16 持平，而 QLoRA 的 FP4 方案则落后约一个百分点。

# 4位 LLaMA

[PRE5]

图6-7

## 6.5 本章小结
本章围绕 LoRA 的实战展开，先梳理了 LoRA 的来龙去脉、关键机制和特性，并给出落地步骤与实验；接着介绍 AdaLoRA，如何在 LoRA 基础上补足缺陷及其核心思路与实测；然后解析 QLoRA 的背景与原理，展示其微调效果；最后聚焦量化技术的应用价值，包括背景、两类量化思路及基于 BitsAndBytes 的具体实现。

整体为读者提供了从 LoRA 到 QLoRA 再到量化的完整操作经验，帮助掌握大模型低成本微调的方法论。

# 第7章 提示工程入门与实践
提示工程又叫指令工程，是一个相对较新的领域，关注 Prompt 也就是提示词的设计与优化，让大语言模型能在不同场景下发挥作用。掌握提示工程有助于更清醒地认识大模型的能力边界。Prompt 看似简单，却是 AGI 时代的“编程语言”和“软件工程”，提示工程师可被看作 AGI 时代的程序员，提示能力就像鼠标键盘一样将成为基本功，专职提示工程师不会长期存在，因为每个人终将掌握。

程序员写伪代码时先用约定俗成的逻辑化描述把思路理清，而不是立刻写机器可识别的代码。Prompt 的最高境界也接近伪代码，只是形式更宽松。未来写程序的关键不再是语法熟练度，而是能写出强逻辑性的 Prompt。

本章将提供更多应对时代挑战的技巧，帮助读者成为新时代机器能力的主人，掌握 AGI 时代的“编程语言”。

## 7.1 探索大模型潜力边界

### 7.1.1 潜力的来源
GPT 爆火前的主流认知是模型越大，下游任务表现越好。大模型预训练的初衷是生成自然连贯的文本，见过海量文本后天生具备补全提示和创作文本的能力，例如写小说、新闻、诗歌。早期的 GPT-3 主要就用在这些创作任务上。

仅靠文本创作能力不足以触发新一轮技术浪潮，真正关键的是“涌现能力”。当模型参数和训练数据规模足够大时，会出现超出预期的能力。比如模型学过中英文语料，却从未系统学习过翻译，最终仍能自然完成中英互译。

涌现能力指模型在未针对特定任务训练的情况下，只要提示合理就能处理该任务。在某种意义上这就是模型潜力，也是大模型成功的根源。对 Completion 类模型而言，它们未接受对话训练，对话能力也是涌现的一种体现。翻译、编程、推理、语义理解等常见应用都属于涌现能力范畴。

正确的提示工程和微调是激发涌现能力的两条有效路径。

### 7.1.2 Prompt的六个建议
如图7-1所示，OpenAI 官网给出了提升结果的六个策略。

## Six strategies for getting better results

## Write clear instructions

These models can't read your mind. If outputs are too long, ask for brief replies. If outputs are too simple, ask for expert-level writing. If you dislike the format, demonstrate the format you'd like to see. The less the model has to guess at what you want, the more likely you'll get it.

Tactics:

- Include details in your query to get more relevant answers

- Ask the model to adopt a persona

- Use delimiters to clearly indicate distinct parts of the input

- Specify the steps required to complete a task

- Provide examples

- Specify the desired length of the output

其中前三条建议可概括为：

- 在查询中加入细节以获得更相关的答案。
ChatGPT 的回答基于对输入的理解和已有知识，Prompt 信息不清晰或不充分时容易产生偏差。通过补充背景、关键词和具体细节，能帮助模型把握上下文、理解意图，从而给出更贴切的回应。不同详细程度的 Prompt 对比如表7-1所示。

表7-1 不同详细程度的Prompt对比