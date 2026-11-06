[PRE0]
把程序跑起来后，当前工作目录会多出一个 data.txt，同时终端会打印出一串用逗号分隔的单词，例如

apple,banana,banana,cherry,mango,melon,orange,orange,peach,pear.

需要注意的是，这些单词是随机挑选的，data.txt 里会出现重复项，而且每次在终端看到的序列都不一样。

# 3.1.2 跨语言代码转换
ChatGPT 具有会话记忆，只要不另开对话，它会保留之前的内容。于是用一句话就能把刚生成的 Python 实现改写成可在 Node.js 下执行的 JavaScript。

将这些Python代码转换为JavaScript代码

发出这条指令后，ChatGPT 会给出转换后的完整输出，代码、注释和说明一并呈现，见图3-2。

转换完成的源文件如下。

代码位置：/src/others_ai_tools/data.js

[PRE1]
在 Node.js 中运行它，终端会输出类似这样的一行

banana,cherry,grape,grape,kiwi,mango,melon,melon,orange,orange.

# 3.1.3 自动补全注释
如果手头有一段没有任何说明的代码，也可以请 ChatGPT 来补注。比如针对一段 Python 版冒泡排序及其测试代码，输入如下提示即可获得详细的中文注释。

下面是一段用Python实现的冒泡排序算法程序及相关的测试程序，为这些代码添加详细的中文注释

[PRE2]
提交后，ChatGPT 会给出如图3-3 的结果。

添加注释后的完整代码如下。

代码位置: /src/others_ai_tools/bubble_sort.py

[PRE3]
# 3.1.4 漏洞检测与修复
ChatGPT 还能做代码安全审查，把代码交给它后，常见的安全隐患就能被揪出来。例如输入“检查下面的Python代码是否存在安全漏洞，并给出解决方案”，它就会对一段 Python 代码进行 SQL 注入风险排查。

[PRE4]
发送上述提示后，ChatGPT 会给出如图3-4 的回复，既指出 SQL 注入漏洞的危害，也提供修复思路。

ChatGPT 给出的修复后代码如下。

代码位置：/src/others_ai_tools/db.py

[PRE5]
从结果可以看出，解决 SQL 注入的关键是使用“？”占位符来传参，而不是用字符串拼接的方式组装 SQL。

# 3.2 New Bing 实时搜索优势
New Bing 是微软发布的带生成式 AI 的新搜索产品，底层使用 GPT-4 系列模型，训练语料与 ChatGPT 不同。它的核心优势在于能实时检索互联网并把最新信息作为参考，这对需要 2021 年 9 月之后数据的任务尤其有用，比如爬虫类开发，否则 ChatGPT 往往得不到理想答案。

正如工作需要团队协作，使用生成式 AI 时也适合组合使用。以下这段 Python 版冒泡排序就是由 ChatGPT 生成的

[PRE6]
代码本身没有错误，但并非最优实现。可以继续让 ChatGPT 优化，也建议另起会话或换用别的模型，比如 New Bing 来改进。在 New Bing 中输入如下要求即可让它进行优化

[PRE7]
回车后，New Bing 会输出如图3-5 的内容。

经过 New Bing 优化后的代码如下。

代码位置: /src/others_ai_tools/bubble_sort_new.py

[PRE8]
# 3.3 Bard 循环优化思路
a[j], a[j+1] = a[j+1], a[j]\nswapped = True # 发生了交换，更新标志变量

if not swapped: # 如果没有交换，说明数组已经有序

break # 提前结束循环

return a

这段逻辑的要点是：冒泡扫描时若前面部分已经有序，就无需继续排序，通过 swapped 标志来提前终止最外层循环。

经过Bard优化后的代码如下。

代码位置：/src/others_ai_tcols/bubble_sort_bard.py

[PRE9]
对比可见，Bard 的优化与 New Bing 的做法在原理上一致，都用 swapped 控制最外层循环是否退出，并在序列已有序时直接结束。差别仅在于，Bard 把外层循环改成了 while，而 New Bing 保留了原版的 for 结构。

# 3.4 Claude 想象力与编码
Claude 名气不如 ChatGPT、New Bing 和 Bard，但上手感不错，能力接近 GPT-3.5。它的代码生成稍弱于 GPT-3.5，因为模型想象力过强，有时会“创造”不存在的函数、方法或类，导致用法不通。正是这份丰富的想象力，使 Claude 更适合探讨哲学类话题，比如我们是谁、从哪里来、要去向何方。

上述描述对应的是旧版 Claude。近期发布的 Claude2 在编程能力上提升明显，已接近 GPT-4，有望成为强劲对手。下面用同样的问题测试 Claude 的优化结果

[PRE10]
按下回车后，会得到如图3-7 的回复。

## 3.5 免费 Copilot 替代方案 CodeGeeX
[PRE11]
经过 Claude 优化后的代码如下。

代码位置: /src/others_ai_tools/bubble_sort_claude.py

[PRE12]
总体来看，Claude、Bard、New Bing 对冒泡排序的优化思路基本相同，区别只是 Claude 把 swapped 改名为 is_sorted，其他实现原理一致。若任务更复杂，生成的代码准确率会下降，尤其在需要调用大量 API 时，使用 ChatGPT、New Bing、Bard、Claude 都要格外注意。

## 第3章 更多AI代码生成方案
## 3.5 免费 Copilot 替代方案 CodeGeeX
本节主要介绍 CodeGeeX 的发展轨迹、核心特点，以及安装、注册与登录的流程。此外还...

## 3.5.1 CodeGeeX 项目介绍
CodeGeeX 由清华大学计算机系唐杰教授团队与华为诺亚方舟实验室杨志林博士团队联合研发，是一个拥有 130 亿参数的多语言代码生成预训练模型。

CodeGeeX 的目标是借助大规模预训练模型实现程序合成，即根据自然语言描述或代码片段生成可运行代码。

训练始于 2022 年 6 月，使用鹏城实验室“鹏城云脑 II”上的 192 个节点，共 1536 个国产昇腾 910 AI 处理器，在 23 种编程语言、8500 亿个代码 token 上完成预训练。

2022 年 9 月，团队开源了代码、模型权重、API、插件以及 HumanEval-X 基准测试，以推动多语言程序合成研究。2023 年 3 月，团队发表论文，详述架构、数据、实现与评测，并在 HumanEval-X 上展示了多语言生成与翻译的优异表现。

主要特点如下：

- 高精度代码生成：支持 Python、C++、Java、JavaScript、Go 等主流语言，在 HumanEval-X 生成任务上的求解率达 47%~60%，平均性能优于其他开源基线。
- 跨语言代码翻译：可一键将代码从一种语言迁移到另一种语言，保持高准确率。
- 可定制化编程助手：免费提供 Visual Studio Code 扩展，具备补全、解释、摘要等功能，提升编码体验。
- 开源与跨平台：代码和权重均对研究开放，支持 Ascend 与 NVIDIA 平台，可在单块 Ascend 910、NVIDIA V100 或 A100 上推理。