Unsloth 实战：医疗推理 SFT 微调全记录

想快速把一个大模型调成懂临床推理的医疗助手，整体流程其实可以拆成模板设计、数据格式化、LoRA 配置、训练器启动、推理验证五步。下面是一次基于 FreedomIntelligence medical-o1-reasoning-SFT 数据集的完整落地记录，参数、日志和样例问答全部保留，方便复现。

先把训练时使用的提示模板定下来，要求模型在输出前先做逐步思考，再给出最终回答。模板里同时固定了指令、问题和回答的位置，后续格式化函数会把样本填进去，并加上 EOS 标记。
train_prompt_style = """Below is an instruction that describes a task, paired with an input that provides further context.

Write a response that appropriately completes the request.

Before answering, think carefully about the question and create a step-by-step chain of thoughts to ensure a logical and accurate response.

### Instruction:

You are a medical expert with advanced knowledge in clinical reasoning, diagnostics, and treatment planning.

Please answer the following medical question.

### Question:

{}

### Response:

{}"""
EOS_TOKEN = tokenizer.eos_token  # Must add EOS_TOKEN

def formatting_prompts_func(examples):

    inputs = examples["Question"]

    cots = examples["Complex_CoT"]

    outputs = examples["Response"]

    texts = []

    for input, cot, output in zip(inputs, cots, outputs):

        text = train_prompt_style.format(input, cot, output) + EOS_TOKEN

        texts.append(text)

    return {

        "text": texts,

    }
[PRE0]
数据采用 Hugging Face 上的 medical-o1-reasoning-SFT 英文训练集，使用自定义格式化函数把 Question、Complex_CoT、Response 三列拼成统一文本，批量处理后可以直接喂给 SFT 训练器。
dataset = load_dataset("FreedomIntelligence/medical-o1-reasoning-SFT","en", split = "train",trust_remote_code=True)

dataset = dataset.map(formatting_prompts_func, batched = True,)

dataset["text"][0]
[PRE1]
模型采用 Unsloth 的 PEFT 接口做 LoRA 微调。秩设为 16，覆盖注意力与 MLP 的主要投影模块，LoRA alpha 同步为 16，dropout 保持 0，梯度检查点使用 unsloth 模式，随机种子固定为 3407。
model = FastLanguageModel.get_peft_model(

    model,

    r=16,

    target_modules=[

        "q_proj",

        "k_proj",

        "v_proj",

        "o_proj",

        "gate_proj",

        "up_proj",

        "down_proj",

    ],

    lora_alpha=16,

    lora_dropout=0,

    bias="none",

    use_gradient_checkpointing="unsloth",

    random_state=3407,

    use_rslora=False,

    loftq_config=None,

)
[PRE2]
训练器选择 TRL 的 SFTTrainer，配合 transformers 的 TrainingArguments。每个设备 batch 为 2，梯度累积 4 步，实际有效 batch 8，训练 3 个 epoch，warmup 5 步，学习率 2e-4。fp16/bf16 根据硬件自动切换，使用 adamw_8bit 优化器，weight decay 0.01，线性调度，日志每 10 步输出一次。
from trl import SFTTrainer

from transformers import TrainingArguments

from unsloth import is_bfloat16_supported

trainer = SFTTrainer(

    model=model,

    tokenizer=tokenizer,

    train_dataset=dataset,

    dataset_text_field="text",

    max_seq_length=max_seq_length,

    dataset_num_proc=2,

    args=TrainingArguments(

        per_device_train_batch_size=2,

        gradient_accumulation_steps=4,

        num_train_epochs = 3,

        warmup_steps=5,

        # max_steps=60,

        learning_rate=2e-4,

        fp16=not is_bfloat16_supported(),

        bf16=is_bfloat16_supported(),

        logging_steps=10,

        optim="adamw_8bit",

        weight_decay=0.01,

        lr_scheduler_type="linear",

        seed=3407,

        output_dir="outputs",

    ),

)
[PRE3]
{"model_id":"aa3d1143312447a0ac71a3f7b0308549","version_major":2,"version_minor":0}
[PRE4]
启动训练。
trainer_stats = trainer.train()
[PRE5]
==((=====))==  Unsloth - 2x faster free finetuning | Num GPUs = 1

\      /|    Num examples = 25,371 | Num Epochs = 3

O^O/ \_/ \    Batch size per device = 2 | Gradient Accumulation steps = 4

\        /    Total batch size = 8 | Total steps = 9,513

 "-____-"     Number of trainable parameters = 41,943,040

[ 389/9513 13:44 < 5:24:01, 0.47 it/s, Epoch 0.12/3]

Step  Training Loss

10    1.285900

20    1.262500

30    1.245400

40    1.284500

50    1.319800

60    1.298900

70    1.298400

80    1.251900

90    1.295300

100   1.244500

110   1.238100

120   1.273400

130   1.292000

140   1.228200

150   1.302400

160   1.189700

170   1.303700

180   1.250100

190   1.237600

200   1.230700

210   1.282500

220   1.294500

230   1.244900

240   1.261500

250   1.243900

260   1.251900

270   1.257000

280   1.239800

290   1.241900

300   1.223600

310   1.329700

320   1.289100

330   1.295700

340   1.195800

350   1.250000

360   1.249300

370   1.201200

380   1.215600
[PRE6]
训练结束时的统计信息。
TrainOutput(global_step=9513, training_loss=1.0824475168592858, metrics={'train_runtime': 20193.217, 'train_samples_per_second': 3.769, 'train_steps_per_second': 0.471, 'total_flos': 2.7936033274397737e+18, 'train_loss': 1.0824475168592858, 'epoch': 2.9992117294655527})
[PRE7]
微调完成后先做推理模式切换，然后用同样的 prompt 风格对新问题生成回答。下面是压力性尿失禁相关的检查推断示例。
question = "A 61-year-old woman with a long history of involuntary urine loss during activities like coughing or sneezing but no leakage at night undergoes a gynecological exam and Q-tip test. Based on these findings, what would cystometry most likely reveal about her residual volume and detrusor contractions?"

FastLanguageModel.for_inference(model)  # Unsloth has 2x faster inference!

inputs = tokenizer([prompt_style.format(question, "")], return_tensors="pt").to("cuda")

outputs = model.generate(

    input_ids=inputs.input_ids,

    attention_mask=inputs.attention_mask,

    max_new_tokens=1200,

    use_cache=True,

)

response = tokenizer.batch_decode(outputs)

print(response[0].split("### Response:")[1])
[PRE8]
[PRE9]
第二个验证问题是典型心肌梗死的冠脉定位，同样使用推理模式生成答案。
question = "Given a patient who experiences sudden-onset chest pain radiating to the neck and left arm, with a past medical history of hypercholesterolemia and coronary artery disease, elevated troponin I levels, and tachycardia, what is the most likely coronary artery involved based on this presentation?"

FastLanguageModel.for_inference(model)  # Unsloth has 2x faster inference!

inputs = tokenizer([prompt_style.format(question, "")], return_tensors="pt").to("cuda")

outputs = model.generate(

    input_ids=inputs.input_ids,

    attention_mask=inputs.attention_mask,

    max_new_tokens=1200,

    use_cache=True,

)

response = tokenizer.batch_decode(outputs)

print(response[0].split("### Response:")[1])
[PRE10]
Based on the presentation of sudden-onset chest pain radiating to the neck and left arm, along with the patient's history of hypercholesterolemia and coronary artery disease, the most likely coronary artery involved is the left anterior descending (LAD) artery. This artery supplies the front wall of the heart, and a blockage here can cause the classic symptoms described. The elevated troponin I levels and tachycardia further support the likelihood of a myocardial infarction, with the LAD being a common site for such events.<|end_of_sentence|>