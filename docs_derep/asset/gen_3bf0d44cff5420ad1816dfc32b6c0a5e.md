Prompt Tuning 在 BERT 上的实战入门

2. BertWithPromptTuning 模型构建：在 BERT 输入嵌入层之前接入 Prompt 嵌入，让模型能够学习到与任务强相关的提示表征。为保证注意力计算与提示对齐，需要同步扩充注意力掩码。
3. 训练流程：在每个 Epoch 内部仅更新 Prompt 的可训练参数，BERT 主体权重保持不变。每个轮次结束后记录当轮平均损失与准确率。

代码运行结果如下：

[PRE0]

运行日志直观呈现了 Prompt Tuning 的调优轨迹。随着 Prompt 嵌入的不断优化，模型对新任务的契合度逐步提升，最终达到了较为理想的准确水平。

### 5.3.2

### P-Tuning 可训练提示生成机制
P-Tuning 是一种基于可训练参数生成提示的方法，通过在输入中植入一组可学习的 Prompt 嵌入来增强模型对任务的适应。与 Prompt Tuning 直接对文本输入做提示不同，P-Tuning 不改动原始词面，而是借助参数化的提示序列实现更灵活的适配。在前向传播时引入额外的嵌入参数，这些参数经训练后能够针对特定任务发挥优化作用。

下面以 BERT 文本分类为例演示 P-Tuning 的具体落地。

[PRE1]

### 5.3.3 组合微调：Prompt Tuning 与 P-Tuning 联合使用
本节演示如何在同一个 BERT 架构中同时挂载 Prompt Tuning 和 P-Tuning，实现更精细的微调，并在具体文本上检验效果。通过组合使用，可以更清晰地观察模型对任务的理解过程。做法是先用 Prompt Tuning 在序列前方补上提示信息，再用 P-Tuning 对这部分提示的表示进行二次训练，从而帮助模型更好地把握任务目标。

[PRE2]

代码拆解如下：

- (1) 两个提示模块的设计：分别实现 PromptTuning 与 PTuning 类，提供各自的可训练参数。PromptTuning 负责在输入序列前拼接提示，PTuning 则进一步对提示表示进行精炼。
- (2) BertWithPromptAndPTuning 整合模型：将 BERT 主干、Prompt Tuning、P-Tuning 三者串联。forward 过程先执行 Prompt Tuning，再执行 P-Tuning，最后把扩充后的嵌入与掩码喂给 BERT。
- (3) 前向传播与结果展示：在前向传播结束后将 logits 映射为标签预测，用以呈现微调后的表现。

示例输出如下：

[PRE3]

借助 Prompt Tuning 与 P-Tuning 的联合微调，模型在提示引导下对文本情感倾向的判断更加精准，体现出很强的任务适配能力。

### 5.3.4 长文本情感分类微调与验证