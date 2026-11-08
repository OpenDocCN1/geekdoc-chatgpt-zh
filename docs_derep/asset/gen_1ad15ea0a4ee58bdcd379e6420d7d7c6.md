# BERT 情感分类实战：推理、微调与部署加速

下面是一套可直接复用的二分类情感分析流程，先看模型输出的后处理通用写法，再演示中文推理、批量输入、IMDB 微调以及推理加速的完整链路。

    probabilities = torch.nn.functional.softmax(logits, dim=-1)

# 5. 解码推理结果

labels = ["消极", "积极"]  # 自定义标签

predicted_label = torch.argmax(probabilities).item()

print(f"推理结果: {labels[predicted_label]}, 概率分布: {probabilities}")

# 6. 可视化推理过程

print("详细输出:")

print(f"Logits: {logits}")

print(f"Probabilities: {probabilities}")

print(f"Predicted Label: {labels[predicted_label]}")

下面是中文模型在 GPU 环境下的单条推理实测记录。

[PRE0]
当前设备: cuda

加载预训练模型和分词器: bert-base-chinese

输入文本: 这个产品非常好，我很喜欢！

将输入文本转化为模型输入格式...

编码后的输入: {'input_ids': tensor([[ 101, 6821, 5431, 2523, 4415, 802, 738, 622, 2769, 2523, 1962, 511,
         102]]), 'token_type_ids': tensor([[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]]), 'attention_mask': tensor([[1, 1, 1, 1,
         1, 1, 1, 1, 1, 1, 1, 1, 1]])}

开始推理...

推理结果: 积极, 概率分布: tensor([[0.0235, 0.9765]], device='cuda:0')

详细输出:

Logits: tensor([[-2.1543,  3.5482]], device='cuda:0')

Probabilities: tensor([[0.0235, 0.9765]], device='cuda:0')

Predicted Label: 积极

批量处理时只需一次性喂入多条文本，分词器会自动对齐长度。

[PRE1]
texts = ["产品很好", "质量很差"]

inputs = tokenizer(texts, return_tensors="pt", padding=True, truncation=True, max_length=128).to(device)

接下来的部分展示在 IMDB 数据集上用 Trainer 完成微调的完整脚本，包含数据采样、预处理、评指标与训练参数。

[PRE2]python
from transformers import AutoTokenizer, AutoModelForSequenceClassification, Trainer, TrainingArguments

from datasets import load_dataset, DatasetDict

import numpy as np

from sklearn.metrics import accuracy_score, precision_recall_fscore_support

# 加载 IMDB 数据集

print("加载数据集...")

dataset = load_dataset("imdb")

dataset = dataset.shuffle(seed=42)

dataset = DatasetDict({

    "train": dataset["train"].select(range(5000)),           # 选择部分训练数据

    "test": dataset["test"].select(range(2000))             # 选择部分测试数据

})

# 加载分词器

model_name = "bert-base-uncased"

tokenizer = AutoTokenizer.from_pretrained(model_name)

# 数据预处理

def preprocess_function(examples):

    return tokenizer(examples["text"], padding="max_length",

                     truncation=True, max_length=128)

print("预处理数据...")

encoded_dataset = dataset.map(preprocess_function, batched=True)

encoded_dataset = encoded_dataset.remove_columns(["text"])     # 删除原始文本列

encoded_dataset = encoded_dataset.rename_column("label", "labels")

encoded_dataset.set_format("torch")

# 加载预训练模型

print("加载预训练模型...")

model = AutoModelForSequenceClassification.from_pretrained(

    model_name, num_labels=2)

# 定义评估指标

def compute_metrics(eval_pred):

    logits, labels = eval_pred

    predictions = np.argmax(logits, axis=-1)

    precision, recall, f1, _ = precision_recall_fscore_support(

        labels, predictions, average="binary")

    acc = accuracy_score(labels, predictions)

    return {"accuracy": acc, "precision": precision,

            "recall": recall, "f1": f1}

# 设置训练参数

training_args = TrainingArguments(

    output_dir="./results",                                # 输出目录

    evaluation_strategy="epoch",                           # 每轮评估一次

    learning_rate=2e-5,                                    # 学习率

    per_device_train_batch_size=8,                         # 训练批量大小

    per_device_eval_batch_size=8,                          # 测试批量大小

    num_train_epochs=3,                                    # 训练轮数

    weight_decay=0.01,                                     # 权重衰减

    logging_dir="./logs",                                  # 日志目录

    logging_steps=50,                                      # 日志打印间隔

    save_strategy="epoch",                                 # 每轮保存一次模型

    save_total_limit=2,                                    # 最多保存 2 个检查点

    load_best_model_at_end=True,                           # 训练结束时加载最佳模型

    metric_for_best_model="accuracy"

)

# 初始化 Trainer

print("开始训练...")

trainer = Trainer(

    model=model,

    args=training_args,

    train_dataset=encoded_dataset["train"],

    eval_dataset=encoded_dataset["test"],

    tokenizer=tokenizer,

    compute_metrics=compute_metrics

)

# 训练模型

trainer.train()

# 测试模型

print("开始评估...")

results = trainer.evaluate()

print("评估结果:", results)

训练与评估的日志如下，可看到数据规模、检查点保存与最终指标。

[PRE3]
加载数据集...

DatasetDict({

    train: Dataset({

        features: ['text', 'label'],

        num_rows: 25000

    })

    test: Dataset({

        features: ['text', 'label'],

        num_rows: 25000

    })

})

预处理数据...

  0%|          | 0/25 [00:00<?, ? ba/s]

  0%|          | 0/10 [00:00<?, ? ba/s]

加载预训练模型...

Some weights of the model checkpoint at bert-base-uncased were not used when initializing BertForSequenceClassification: ['cls.predictions.bias', 'cls.predictions.transform.dense.weight', 'cls.predictions.transform.dense.bias', ...]

- This IS expected if you are initializing BertForSequenceClassification from the checkpoint of a model trained on another task or with different labels...

- This IS NOT expected if you are initializing BertForSequenceClassification from the checkpoint of a model that you expect to be exactly identical (initializing a model on the same task, with the same number of labels, etc).

开始训练...

***** Running training *****

  Num examples = 5000

  Num Epochs = 3

  Instantaneous batch size per device = 8

  Total train batch size (w. parallel, distributed & accumulation) = 8

  Gradient Accumulation steps = 1

  Total optimization steps = 1875

...

Epoch 3: 100% ████████████████ | 625/625 [01:35<00:00, 6.54it/s]

Saving model checkpoint to ./results/checkpoint-1875

Configuration saved in ./results/checkpoint-1875/config.json

Model weights saved in ./results/checkpoint-1875/pytorch_model.bin

Deleting older checkpoint [./results/checkpoint-1250] due to args.save_total_limit

***** Running Evaluation *****

  Num examples = 2000

  Batch size = 8

开始评估...

***** Running Evaluation *****

  Num examples = 2000

  Batch size = 8

{'eval_loss': 0.2351, 'eval_accuracy': 0.925, 'eval_precision': 0.926, 'eval_recall': 0.924, 'eval_f1': 0.925, 'eval_runtime': 4.56, 'eval_samples_per_second': 438.52}

最后给出推理加速的准备代码，演示如何加载模型并导出为 ONNX 以便后续量化与部署。

[PRE4]python
from transformers import AutoTokenizer, AutoModelForSequenceClassification

import torch

from torch.quantization import quantize_dynamic

import onnx

import onnxruntime

import numpy as np

# 检查 CUDA 是否可用

device = torch.device("cuda" if torch.cuda.is_available() else "cpu")

print(f"当前设备：{device}")

# 加载预训练模型和分词器

model_name = "bert-base-uncased"

print(f"加载预训练模型和分词器：{model_name}")

tokenizer = AutoTokenizer.from_pretrained(model_name)

model = AutoModelForSequenceClassification.from_pretrained(

    model_name, num_labels=2).to(device)

# 定义示例文本

text = "This is an example text for inference acceleration."

print(f"输入文本: {text}")

# 文本编码

inputs = tokenizer(text, return_tensors="pt", padding=True,

                   truncation=True, max_length=128).to(device)

# 导出为 ONNX 格式

onnx_model_path = "bert_base_uncased.onnx"

print(f"导出模型为 ONNX 格式: {onnx_model_path}")

torch.onnx.export(

    model,

    (inputs["input_ids"], inputs["attention_mask"],

     inputs["token_type_ids"]),

    onnx_model_path,