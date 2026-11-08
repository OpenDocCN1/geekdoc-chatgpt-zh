# 从加载到评估：让大模型训练与评测更可控

（6）Logging Steps（logging_steps）：训练时指定一个步长间隔，系统会按此频率打印日志并展示 loss 等核心数值，便于实时掌握训练进展。

### 4.4.5 模型训练

下面以代码4-18为例说明模型的导入方式。

### 代码4-18

[PRE0]
在载入预训练权重时显式传入 num_labels=2，实质是对模型尾部结构进行改造，使其适配二分类任务。这一设定把输出维度调整为两路概率，分别对应两个互斥的类别，能够满足正负情感判断、二元决策等典型场景的需求。与此前做法不同，这里不能再使用 half 进行模型量化。

随后进入训练主流程。先对原始文本做清洗、分词、向量化等预处理，把数据转成模型可读的格式。然后基于已构建好的数据集，选择合适的优化器、损失函数和学习率调度策略，开始迭代训练。训练期间要同步观察训练损失和验证集表现，必要时下调学习率或触发早停，防止过拟合以保障泛化。借助交叉验证、学习曲线等手段监控过程，有助于提升分类准确率。最后在测试集上检验效果，完成从导入定制到训练再到评估的闭环，并可按需微调或部署。

以代码4-19为例，Transformers 提供的 Trainer 组件把神经网络微调的复杂步骤高度封装，整体可归纳为三步。

### 代码4-19

[PRE1]
1) 创建 Trainer 实例：实例化时需要传入已加载的模型 model、训练配置 arguments，通常封装在 TrainingArguments 中用于控制训练策略与日志记录；训练集和验证集；以及一个可选的 compute_metrics 函数，该函数定义评估指标，也可写入数据集定义中以定制度量。后续章节会对该函数进行说明。

2) 前置准备：在实例化前确认所有输入已就绪。模型需完成加载并配置参数，数据集需完成预处理，评估指标函数也应准备好。

3) 启动微调：调用实例的 train 方法即可一键开始微调。内部会自动完成数据加载、批次划分、前向、反向、参数更新等操作，直至达到设定的轮次或停止条件。

如图4-30所示，想要了解 Trainer 的高级用法和完整配置项，可查阅官方文档。文档详细解释了各参数作用，并提供了精细化调参的指导，帮助实现高效定制化微调。

实际启动训练只需执行 trainer.train()。运行结果如图4-31所示，系统会按预设频率输出训练损失，该频率由 logging_steps 控制，用户可自主调节信息反馈的密度，以便及时跟踪学习进度。

## Trainer

[PRE2]

[PRE3]

| Step | Training Loss |
|---|---|
| 500 | 0.501900 |
| 1000 | 0.274300 |

为避免训练中断导致成果丢失，并方便后续评估调优，训练流程内置了自动保存检查点的机制。用户可以按 epoch 数或其他自定义条件设定保存间隔，达到条件时系统会自动持久化当前模型状态。这一设计提升了资源利用率，也让模型迭代与对比更便利，可随时从任意检查点恢复训练或进行推理。

### 4.4.6 模型评估

与 Datasets 库思路一致，如图4-32所示，评估环节也有专门的库来极简流程。理想情况下一行代码就能调用多种评估方法。该库整合了大量技术与指标，从基础准确率到复杂性能分析，为研究者和开发者提供统一高效的工具集。抽象掉底层复杂性后，专业评估变得触手可及，促进了模型分析的标准化与可比性，加快开发到部署的迭代。

评估是检验模型性能的核心。基于数据集中的标签或训练验证集的标注，系统化衡量预测结果的准确性与可靠性。评估不仅适用于监督学习，也在无监督和强化学习中发挥关键作用。

## Evaluate

Evaluate

A library for easily evaluating machine learning models and datasets.

With a single line of code, you get access to dozens of evaluation methods for different domains (NLP, Computer Vision, Reinforcement Learning, and more!). Be it on your local machine or in a distributed training setup, you can evaluate your models in a consistent and reproducible way!

Visit the Evaluate organization for a full list of available metrics. Each metric has a dedicated Space with an interactive demo for how to use the metric, and a documentation card detailing the metrics limitations and usage.

[PRE4]
图4-32

评估思路大致分为两类。

1. 直接用目标函数与损失函数衡量表现，得到的 training_loss 直观看出预测与真实值的偏差。调参与训练深入后，损失应逐步趋近于零，说明拟合度提升，学习趋于收敛。

2. 依赖各类评估指标 metrics，例如 accuracy、F1 score 等，用于衡量分类或回归任务。它们能从多维度综合评价模型，比单一损失更丰富。Datasets 库自带的 load_metric 函数让应用标准指标变得极简，一行代码即可加载预定义方法，提升效率与规范性。

代码4-20 的做法是先让模型在验证集上预测，再用 argmax 从 predictions.predictions 中取出每个样本最可能的类别。np.argmax(..., axis=-1) 沿最后一个轴寻找最大值索引，axis=-1 即对每个样本的各类别概率求最大，得到的索引即为模型选择的类别。

代码4-20

[PRE5]
结果如图4-33所示，preds 是一个一维数组，长度等于样本数，元素为各样本的预测类别索引。

[PRE6]
图4-33

训练完成后，下一步是利用验证集评估泛化能力，这对理解模型有效性、调参防过拟合至关重要。目前已能监控训练损失，但为了更全面把握进展，需要补充 accuracy、score 等指标。

Datasets 库提供的 load_metric 方法正好满足这一需求。它能便捷地接入主流评估指标，无论是 accuracy、precision、recall，还是 F1、AUC-ROC 等，Hugging Face 生态几乎全覆盖。使用者无需自行实现评估逻辑，直接调用预置函数即可把精力放在模型创新与业务优化上。如代码4-21所示，评估阶段因此变得更简洁、专业、可靠，也加快了不同版本模型的迭代对比。

代码4-21

[PRE7]
下面进一步说明 metric 的实用价值。在 NLP 任务中，load_metric 的核心作用是从评估库加载成熟准则。只需指定指标名称即可自动加载对应算法。该函数接收两组输入：predictions 为模型预测输出，references 为真实标签，基于两者计算并返回 accuracy 等关键指标，直观反映模型表现。