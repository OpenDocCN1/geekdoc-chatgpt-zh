# 实战干货：5 个专业级翻译 Prompt 模版直接拿走

做过跨语言内容的人都知道，扔给 AI 一句“帮我翻译一下”，出来的往往是“翻译腔”重、术语错漏的半成品。今天整理了我私藏的 5 套结构化提示词，覆盖从基础互译到专业领域、深度解析的不同场景，**复制即用**，建议先收藏再阅读。

---

### 基础热身：指定目标语言的简单指令

最基础的单轮对话模版，适合快速验证模型多语言能力，或处理无专业术语的日常短文。

[PRE0]

翻译：天空很蓝。

翻译成法语：天空很蓝。

[PRE1]

翻译：The sky is blue.

---

### 进阶实战：垂直领域「术语表 + 缩写表」强约束

处理 AI、科技、金融等垂类长文时，**「不翻译清单」与「强制译词表」**是保证专业度的生命线。这个模版预设了大模型常见术语的标准中文对应，你只需把业务专有名词追加进去即可。

[PRE2]

你的任务是将 AI 领域的一篇英文文章翻译成中文。缩写词和词汇表如下：

缩写词：以下缩写词、组合词不翻译

·GPT、GPU、AI、ChatGPT

词汇表：如下词汇请按词汇表翻译，无中文则表示该词无须翻译

·Large Language Model, LLM，大语言模型

·Transformer，转换器

·Generative AI，生成式 AI

·Token

---

### 复杂场景：马斯克与 OpenAI 早期恩怨还原

真实世界的素材往往夹杂人名、时间线、机构名。这段英文材料考验模型对实体识别的准确度，以及对复杂从句逻辑的梳理能力（如 “cleared the way for”、“reportedly warned” 等）。

[PRE3]

Elon Musk's 2018 departure from OpenAI cleared the way for Microsoft. Musk was one of the co-founders of OpenAI in 2015, and one of its backers. He reportedly warned OpenAI CEO Sam Altman, also a co-founder, in 2018 that OpenAI was falling behind Google's AI development. Musk wanted to take over OpenAI and run it himself, but Altman and the other OpenAI co-founders refused.

---

### 专家模式：化身「词典级」翻译引擎

单纯给译文不够？这个 Prompt 让模型输出 **「多译文对照 + 核心词汇拆解 + 语境标注 + 双语例句 + 音标词性」** 的结构化卡片。做内容出海、制作语言学习素材、深度研读原文时，这个模版堪称「外挂」。

[PRE4]

你是一个翻译引擎，请将给到的文本翻译成中文。请列出 3 种（如果有）最常用的翻译结果，提炼出其中的关键单词或短语，并列出对应的适用语境（用中文阐述）、音标、词性、双语示例。

按照下面的格式用中文阐述：

<序号><单词或短语> · /<音标>[<词性缩写>] <适用语境（用中文阐述）>

例句：<例句>（例句翻译）

翻译这句话：You can't connect the dots looking forward; you can only connect them looking backwards. So you have to trust that the dots will somehow connect in your future.

---

### 教学模式：定向词义精讲

遇到一词多义、语法结构卡壳？指定「句子 + 目标单词」，让模型化身 **私教**，只讲该词在当下语境下的义项、同义改写句及中文解析，拒绝无关信息干扰，查漏补缺效率极高。

[PRE5]

你是一位英语词义语法专家，正在教我英语。

我给你一句“英文”句子，并给出句中的一个单词，请用“中文”帮我解释该单词在句子中的意思。同时，请再列举几个有相同意思的英文例句，并用中文解释。

---

## 怎么用最顺手？

1.  **建立 Prompt 库**：把上述 5 个 `[PRE*]` 块存入 Notion、Obsidian 或提示词管理插件（如 ChatGPT Shortcut、PromptBox）。
2.  **动态替换变量**：用时把 `[PRE2]` 里的词汇表换成你当下项目的术语表；把 `[PRE4]` 后的乔布斯名言换成你要深度解析的目标句子。
3.  **组合拳打法**：长文先用 `[PRE2]` 产出初译，疑难句再丢给 `[PRE4]` 拆解，生词再扔给 `[PRE5]` 精讲，**三步闭环**从“看懂”到“吃透”。

别再裸奔式对话了，给 AI 穿上结构化提示词的「铠甲」，翻译质量立竿见影。