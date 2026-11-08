[PRE0]

运行结果呈现如下。

[PRE1]

随后执行以下代码，把inputs作为参数送入对话流程。

[PRE2]

结果示例如下：

到这里，基于Unsloth的推理演示就结束了。

##### 2. 用Unsloth驱动Qwen模型

同理，Unsloth也能用来加载Qwen的蒸馏版，下面给出调用示例。

[PRE3]

输出示例如下：

[PRE4]

接下来运行下面代码发起对话。

[PRE5]

返回结果如下：

[PRE6]

**答案**

\boxed{\sqrt{2}\text{是无理数}}

[PRE7]python

prompt_style_chat = """请写出一个恰当的回答来完成当前对话任务。

### Instruction:

你是一名助人为乐的助手。

### Question:

{}

### Response:

\n\n 你好！很高兴见到你，好久不见哦！最近怎么样？有什么我可以帮忙的吗？ 😊<|end_of_sentence|>"]

[PRE8]python

print (response[0].split ("### Response:") [1])

[PRE9]

n

你好！很高兴见到你，好久不见哦！最近怎么样？有什么我可以帮忙的吗？😊<|end_of_sentence|>

[PRE10]python

question = "请证明根号 2 是无理数。"

inputs = tokenizer([prompt_style_chat.format(question, "")], return_tensors="pt").to("cuda")

outputs = model.generate(

    input_ids=inputs.input_ids,

    max_new_tokens=1200,

    use_cache=True,

)

response = tokenizer.batch_decode(outputs)

print(response[0].split("### Response:")[1])

[PRE11]

根号2是无理数。

**详细证明：**

我们可以使用反证法来证明根号2是无理数。

假设根号2是有理数，那么它可以表示为两个互质的整数 a/b，其中 b ≠ 0 且 a 和 b 没有公因数。

1. 将等式两边平方：

\[ (\sqrt{2})^2 = \left(\frac{a}{b}\right)^2 \]

得到：

\[ 2 = \frac{a^2}{b^2} \]

2. 两边同时乘以 b²：

\[ 2b^2 = a^2 \]

这表明 a² 是 2 的倍数，因此 a 也是 2 的倍数。设 a = 2k，其中 k 是整数。

3. 代入上式：

\[ (2k)^2 = 2b^2 \]

得到：

\[ 4k^2 = 2b^2 \]

化简得：

\[ 2k^2 = b^2 \]

这表明 b² 也是 2 的倍数，因此 b 也是 2 的倍数。

4. 由于 a 和 b 互质，若它们都被 2 整除，矛盾。因此，假设根号2是有理数不成立。

综上所述，根号2是无理数。 < | end_of_sentence | >

[PRE12]python

prompt_style = """Below is an instruction that describes a task, paired with an input that provides further context.

Write a response that appropriately completes the request.

Before answering, think carefully about the question and create a step-by-step chain of thoughts to ensure a logical and accurate response.

### Instruction:

You are a medical expert with advanced knowledge in clinical reasoning, diagnostics, and treatment planning.

Please answer the following medical question.

### Question:

{}

### Response:

##### 2. 开启微调

将模型设置为微调模式。

[PRE13]

展示结果如下：

[PRE14]

下面代码用于引入微调所需依赖。

[PRE15]

有监督微调主要依赖SFTTrainer实现，接下来创建微调训练器的示例代码如下。

[PRE16]

该片段实例化训练器并配置了多项关键超参，所用组件包括：

- SFTTrainer类来自trl库：trl即transformer reinforcement learning，是Hugging Face推出的库，涵盖有监督微调与强化学习能力，SFTTrainer专用于有监督微调，支持LoRA等低秩适配方式。

- TrainingArguments类（来自transformers库）：该类用于定义训练超参数，如批量大小、学习率、优化器、训练步数等。

- is_bfloat16_supported()函数（来自Unsloth）：用于检测当前GPU是否支持bfloat16，若支持返回True，否则返回False。bfloat16是一种更高效的数值格式，在NVIDIA A100/H100等新款GPU上表现更优。

其中重要的参数说明如表6.7所示。

**表6.7 有监督微调对象参数说明**

| 类名 | 参数 | 作用 |

| :--- | :--- | :--- |

| SFTTrainer | model=model | 指定需要进行微调的预训练模型 |

| | tokenizer=tokenizer | 指定分词器，用于处理文本数据 |

| | train_dataset=dataset | 传入训练数据集 |

| | dataset_text_field="text" | 指定数据集中哪一列包含训练文本（在 formatting_prompts_func 里处理） |