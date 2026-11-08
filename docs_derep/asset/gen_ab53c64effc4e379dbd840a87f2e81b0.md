在多轮交互里，维持上下文的一致性是决定实用性的关键。像要求模型保持简洁的输出、或始终扮演某个指定公众人物这类指令，必须贯穿整个对话始终。早期的 Llama 系列往往在聊几轮后就丢掉了最初的设定，导致体验急剧下降。

为此 Llama 2 引入了 Ghost Attention，简称 GAtt。这是一种实现上相当轻量的思路，受 Context Distillation 启发，通过对微调数据的改造，让模型在长时间对话中仍能紧盯指令，从而显著提升多轮一致性。

GAtt 的做法是先准备一个多轮对话集，可记作 [u1, a1 … un, an]，其中 u 表示用户消息，a 表示助手的回复。随后定义一个需要在整段会话中持续生效的指令 instruct，比如特定角色扮演。把这条指令拼接到每一轮的用户输入上，得到新序列 [instruct+u1, a1, …, instruct+un, an]，再用最新的 RLHF 模型在此合成数据上进行采样。

构造训练指令时可以加入各种合成限制，例如固定的兴趣爱好、使用语言、模仿的公众人物等，保证模型在多轮对话中始终受约束。为了让指令更丰富多样，系统还会随机组合多项限制来生成最终的指令。

效果验证聚焦在模型的最大注意力激活值上。实验显示，采用 GAtt 的模型在整段对话里都能持续保持对系统消息的注意力激活，对比未使用 GAtt 的版本优势明显。GAtt 目前的效果已经不错，但实现仍偏初级，后续的开发和迭代有望把它做得更强。

## 8.3 面向算法竞赛的代码生成模型
从本质上看，代码生成模型只是语言模型的特殊分支，因为代码本身也是一种语言表示。因此这类模型既要具备自然语言生成能力，也要具备代码生成能力。理论上主要有两条路线：一是先有预训练大语言模型再做代码微调，二是先有代码大模型再补自然语言训练。还有一种思路是把代码当作自然语言处理的一部分，直接进行混合训练。

现有的代码大模型大多同时具备这两种基础能力，但在专业场景下的代码质量仍显不足。ChatGPT 在 GRE、SAT、LSAT 等考试中表现突出，但在 Codeforces 竞赛中仅拿到 260 分，GPT-4 稍好也只有 392 分。也有研究用 Codex 来解 Codeforces 题目，简单题直接喂描述就能得到正确答案，复杂题则需要先参考解题思路才能生成可用代码。可见尽管大家担心模型会像 AlphaGo 击败职业棋手那样冲击竞赛编程，Codeforces 目前仍是它们难以逾越的难关。本节将在 Code Llama 的基础上从零微调一个代码生成模型，专门生成 Codeforces 算法竞赛题目的代码，提升解题水平。

### 8.3.1 数据集构建方案
目前公开的代码类数据集中，并不存在同时覆盖 Codeforces 所有题目和全部参赛者提交记录的数据集，因此需要自建。目标结构是：最外层按比赛 ID 分文件夹，其下按问题 ID 再分文件夹，每个问题文件夹内包含三种文件：problem.json 用来保存题面相关信息，test.json 用来保存测试用例，submission.json 用来保存参赛者的代码提交等信息。

[PRE0]

### 1. 官方接口概览
Codeforces 官方提供了若干 API，可以获取比赛和题目相关的基础介绍数据，虽然不包含题面正文和选手提交代码的详细内容，但在其基础上可以继续开发爬取所需数据。下面介绍本项目会用到的几个接口。

#### （1）查询全部比赛列表
`https://codeforces.com/api/contest.list` 会返回所有可用比赛，是一个 Contest 对象列表。

使用 requests 库获取比赛列表的 Python 示例：

[PRE1]

Contest 在 Codeforces 中代表一场比赛，包含比赛唯一标识 id、名称 name、计分体系 type 如 CF、IOI、ICPC 等、当前阶段 phase 如 BEFORE、CODING、SYSTEM_TEST、FINISHED 等、是否冻结排名的 frozen 标志、比赛时长 durationSeconds、开始时间 startTimeSeconds、比赛开始后已过去的秒数 relativeTimeSeconds 等核心信息。还有一些可选字段，例如创建者 preparedBy、官网链接 websiteUrl 等，整体数据较为完整。

#### （2）查询全部题目列表
`https://codeforces.com/api/problemset.problems` 会返回所有比赛的题目，是一个 Problem 对象列表。

使用 requests 库获取题目列表的 Python 示例：

[PRE2]

在 Codeforces 中，每个编程题目都以 Problem 对象表示。包含题目所属比赛的 contestId、所属题集简称 problemsetName、题目在比赛中的编号 index 通常为字母或字母加数字、题目名称 name、题目类型 type 可为 PROGRAMMING 或 QUESTION 等关键字段。其它可选字段包括最大得分 points、难度评级 rating、标签 tags 等。

#### （3）查询选手提交记录
平台上每场比赛都会产生大量选手代码提交。借助 Codeforces 提供的 `contest.status` API，可以方便地获取特定比赛的提交信息，用于分析选手表现、所用语言、判定结果等。因为单场比赛提交量可能很大，该接口支持筛选参数，核心参数有 contestId 即比赛 ID、asManager 是否返回管理员可见数据、handle 用于限定特定用户、from 与 count 分别控制起始位置和返回数量。

使用 requests 库获取选手提交信息的 Python 示例：

[PRE3]

每次提交在 Codeforces 上用 Submission 对象描述。包含提交标识 id、比赛标识 contestId、创建时间 creationTimeSeconds、比赛开始后到提交时的经过秒数 relativeTimeSeconds 等，还附带本次提交所对应的 Problem 对象和提交者 Party 对象。此外还能提供编程语言 programmingLanguage、判定结果 verdict、测试集 testset、通过测试数 passedTestCount 等信息。

### 2. 手动爬取题面内容
官方 API 不提供题面正文的获取能力，这部分需要自行实现。通过观察可知，每道题目的 URL 遵循固定模式，例如 `https://codeforces.com/contest/1800/problem/A` 即比赛 1800 的题目 A。下载该页面并解析即可提取所需内容。

主体代码如下所示：

[PRE4]

该函数接收三个参数：比赛编号 contest_idx、题目编号 problem_idx 以及 problem_dict。problem_dict 用于存放爬取到的题目数据，初始可以是 Problem 对象或空字典。函数先检查目标题目是否已有数据，若已有则跳过。随后访问对应题目的 Codeforces 页面，用 BeautifulSoup 解析内容，从页面中抽取标题、时间限制、内存限制、题目描述、输入输出格式等信息并写入 problem_dict，最后以 JSON 形式保存。其中 Tomd 是将 HTML 转为 Markdown 的 Python 库。

处理题目中的输入输出样例时，因可能存在多个样例，需单独循环处理，其中的 strip_html_tags 函数定义如下。

[PRE5]

最终得到的题面信息如下所示。