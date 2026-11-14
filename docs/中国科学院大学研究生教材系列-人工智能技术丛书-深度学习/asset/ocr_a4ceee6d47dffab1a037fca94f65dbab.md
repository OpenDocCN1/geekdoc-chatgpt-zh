期望的答案，用这些 Prompt 和对应的答案作为数据，实现 GPT 模型的有监督微调。

2）使用微调后的模型，根据更多的 Prompt 生成答案，一次 Prompt 多次采样生成多个答案，然后请人工标注生成内容的相对顺序，根据这些带有顺序的 Prompt 和答案数据训练一个奖励模型，模型能够根据输入的 Prompt 和答案输出一个得分。

3）采样更多的 Prompt，采用强化学习的方式不断优化生成模型，在强化学习中，使用 2) 得到的奖励模型为生成内容打分。

在这个过程中，2) 和 3) 是一个持续迭代的过程。

InstructGPT 首先使用有监督学习在标签上微调 GPT3，然后训练一个奖励模型来获取提示、响应和对应的奖励。在这个过程中的奖励模型（Reward Model）是一个有监督的微调模型，输入是 Prompt 和生成的答案内容，输出一个标量作为打分结果，奖励模型的损失函数如下：

\(\text{loss}(\boldsymbol{\theta}) = -\frac{1}{\binom{K}{2}} E_{(x, y_w, y_l) \sim D} [\log(\sigma(r_{\theta}(x, y_w) - r_{\theta}(x, y_l)))] \tag{4-27}\)

其中，\(K\) 是模型生成的答案的个数，\(r_{\theta}(\boldsymbol{x}, \boldsymbol{y})\) 是提示 \(\boldsymbol{x}\) 和生成 \(\boldsymbol{y}\) 对应的奖励标量输出，\(\boldsymbol{\theta}\) 是参数，\(\boldsymbol{y}_w\) 表示 \((\boldsymbol{y}_t, \boldsymbol{y}_w)\) 模型所生成的序列中的偏好输出。OpenAI 聘请的标注人员对 \(K\) 个模型生成的答案进行排序，目标函数的意义是使得奖励模型与人工标注的答案相对顺序一致。

接下来，使用强化学习并利用奖励模型在环境中微调 GPT 模型，给定提示和响应，它产生由奖励模型确定的奖励得分，在强化学习的训练中，InstructGPT 尝试最大化以下组合目标函数：

\(\text{obj}(\theta) = E_{(x, s) \sim D^{\text{RL}}_{\pi_{\theta}^{\text{RL}}}} \left[ r_{\theta}(x, y) - \beta \log \left( \frac{\pi_{\theta}^{\text{RL}}(y \mid x)}{\pi^{\text{SFT}}(y \mid x)} \right) \right] + \gamma E_{(s) \sim D_{\text{pretrain}}} [\log(\pi_{\theta}^{\text{RL}}(x))] \tag{4-28}\)

其中，\(\pi_{\theta}^{\text{RL}}\) 是强化学习策略，\(\pi^{\text{SFT}}\) 是有监督训练的模型，\(D_{\text{pretrain}}\) 是数据的预训练分布。\(\beta\) 是 KL（KL 散度距离）奖励系数，\(\gamma\) 是预训练损失系数。

ChatGPT 是目前影响最大的 GPT 模型，它在人机对话领域超过了以往任何一个对话模型，取得了令人振奋的成绩。ChatGPT 的工作模式与 InstructGPT 大致相似，只是使用的数据集有些区别。OpenAI 目前尚未公布 ChatGPT 的详细技术细节，官方发布的博客中的大致描述是 ChatGPT 使用了对话精调的方式，即多轮 Prompt 和上下文，在训练 InstructGPT 模型中用到的数据集也被转换成对话的形式合并使用。基于反馈的强化学习方法仍然被使用。

# 4.4 BERT 系列模型

纯 Encoder 模型只使用 Transformer 中的 Encoder 部分，也就是说，在这类模型中，注意力机制可以访问到原始输入句子中的所有单词。基于 Transformer 的双向编码器表示模型（Bidirectional Encoder Representations from Transformer，BERT）[10] 就是一种基于 Transformer 的纯 Encoder 模型，2018 年由 Goolge 公司的 Jacob Devlin 等人提出，广泛应用于自然语言处理领域的各种任务，比如句子分类、句子相似度计算、自然语言推理、命名实体识别、基于机器阅读