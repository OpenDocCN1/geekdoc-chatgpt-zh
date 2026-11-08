BERT增量微调：既吃新数据也不忘旧知识

做持续学习最怕的就是灾难性遗忘，模型一学新样本就把旧能力丢了。下面这套基于 bert-base-chinese 的增量训练思路，用很小的旧数据回放 + 参数约束来稳定边界，同时用 L2 正则化防止过拟合。核心是让新旧损失一起算，再加一个拉回初始参数的约束项，alpha 控制新旧平衡，beta 控制约束强度。

第一种做法是把老新批次配对喂入，每轮同时算两份交叉熵再合成总损失。

[PRE0]python

import torch

from torch.utils.data import DataLoader, Dataset

from transformers import AutoModelForSequenceClassification, AutoTokenizer, AdamW

import torch.nn.functional as F

# 定义模型和分词器

model_name = "bert-base-chinese"

tokenizer = AutoTokenizer.from_pretrained(model_name)

model = AutoModelForSequenceClassification.from_pretrained(model_name, num_labels=3)

# 模拟旧数据和新数据

old_texts = ["旧数据样本1", "旧数据样本2", "旧数据样本3"]

new_texts = ["新数据样本1", "新数据样本2"]

old_labels = [0, 1, 0]

new_labels = [1, 2]

# 自定义数据集类

class CustomTextDataset(Dataset):

    def __init__(self, texts, labels, tokenizer, max_length=128):

        self.texts = texts

        self.labels = labels

        self.tokenizer = tokenizer

        self.max_length = max_length

    def __len__(self):

        return len(self.texts)

    def __getitem__(self, idx):

        inputs = self.tokenizer(

            self.texts[idx],

            padding="max_length",

            truncation=True,

            max_length=self.max_length,

            return_tensors="pt"

        )

        return {

            "input_ids": inputs["input_ids"].squeeze(),

            "attention_mask": inputs["attention_mask"].squeeze(),

            "labels": torch.tensor(self.labels[idx], dtype=torch.long)

        }

# 加载新旧数据集并创建DataLoader

old_dataset = CustomTextDataset(old_texts, old_labels, tokenizer)

new_dataset = CustomTextDataset(new_texts, new_labels, tokenizer)

old_dataloader = DataLoader(old_dataset, batch_size=2, shuffle=True)

new_dataloader = DataLoader(new_dataset, batch_size=2, shuffle=True)

# 定义优化器并加入L2正则化

optimizer = AdamW(model.parameters(), lr=2e-5,

                  weight_decay=0.01)  # weight_decay控制L2正则化强度

# 保存初始模型参数用于约束计算

initial_params = {name: param.clone() for name,

                  param in model.named_parameters() if param.requires_grad}

# 训练循环：结合正则化和约束技术

device = torch.device("cuda" if torch.cuda.is_available() else "cpu")

model.to(device)

num_epochs = 3

alpha = 0.5        # 控制新旧数据损失的平衡系数

beta = 0.1         # 控制参数约束的强度

for epoch in range(num_epochs):

    model.train()

    total_loss = 0

    for (old_batch, new_batch) in zip(old_dataloader, new_dataloader):

        # 获取旧数据和新数据的批次

        old_input_ids = old_batch["input_ids"].to(device)

        old_attention_mask = old_batch["attention_mask"].to(device)

        old_labels = old_batch["labels"].to(device)

        new_input_ids = new_batch["input_ids"].to(device)

        new_attention_mask = new_batch["attention_mask"].to(device)

        new_labels = new_batch["labels"].to(device)

        optimizer.zero_grad()

        # 计算旧数据的损失

        old_outputs = model(old_input_ids, attention_mask=old_attention_mask)

        old_loss = F.cross_entropy(old_outputs.logits, old_labels)

        # 计算新数据的损失

        new_outputs = model(new_input_ids, attention_mask=new_attention_mask)

        new_loss = F.cross_entropy(new_outputs.logits, new_labels)

        # 计算参数约束损失

        constraint_loss = 0.0

        for name, param in model.named_parameters():

            if name in initial_params:

                constraint_loss += torch.norm(

                    param - initial_params[name], p=2)  # 计算L2距离

        # 总损失=新旧数据混合损失+参数约束损失

        loss = alpha * new_loss + (1 - alpha) * old_loss + beta * constraint_loss

        loss.backward()

        optimizer.step()

        total_loss += loss.item()

    avg_loss = total_loss / len(new_dataloader)

    print(f"第{epoch+1}轮平均损失: {avg_loss:.4f}")

在 3 个 epoch 的演示里，平均损失是稳定下降的：

[PRE1]
第1轮平均损失：0.5267
第2轮平均损失：0.4378
第3轮平均损失：0.3821

如果想简化流水线，可以把老新样本合并成一个集合，用加权采样来体现偏好。新样本采样权重设为 1.0，旧样本设为 0.5，既保持回放又不让旧数据主导。训练时仍保留 AdamW 的 weight_decay 与基于初始参数的 L2 约束，思路一致，只是数据供给方式变了。

[PRE2]python

import torch

from torch.utils.data import DataLoader, Dataset, WeightedRandomSampler

from transformers import AutoModelForSequenceClassification, AutoTokenizer, AdamW

import torch.nn.functional as F

# 定义模型和分词器

model_name = "bert-base-chinese"

tokenizer = AutoTokenizer.from_pretrained(model_name)

model = AutoModelForSequenceClassification.from_pretrained(

    model_name, num_labels=3)

# 模拟旧数据和新数据

old_texts = ["旧数据样本1", "旧数据样本2", "旧数据样本3"]

new_texts = ["新数据样本1", "新数据样本2"]

old_labels = [0, 1, 0]

new_labels = [1, 2]

# 自定义数据集类

class CustomTextDataset(Dataset):

    def __init__(self, texts, labels, tokenizer, max_length=128):

        self.texts = texts

        self.labels = labels

        self.tokenizer = tokenizer

        self.max_length = max_length

    def __len__(self):

        return len(self.texts)

    def __getitem__(self, idx):

        inputs = self.tokenizer(

            self.texts[idx],

            padding="max_length",

            truncation=True,

            max_length=self.max_length,

            return_tensors="pt"

        )

        return {

            "input_ids": inputs["input_ids"].squeeze(),

            "attention_mask": inputs["attention_mask"].squeeze(),

            "labels": torch.tensor(self.labels[idx], dtype=torch.long)

        }

# 创建旧数据集、新数据集和混合采样器

old_dataset = CustomTextDataset(old_texts, old_labels, tokenizer)

new_dataset = CustomTextDataset(new_texts, new_labels, tokenizer)

combined_texts = old_texts + new_texts

combined_labels = old_labels + new_labels

combined_weights = [0.5 if i < len(old_texts) else 1.0 for i in range(len(combined_labels))]    # 旧数据权重较低

sampler = WeightedRandomSampler(combined_weights, num_samples=len(combined_weights), replacement=True)

combined_dataset = CustomTextDataset(combined_texts, combined_labels, tokenizer)

dataloader = DataLoader(combined_dataset, batch_size=2, sampler=sampler)

# 初始化优化器和正则化

optimizer = AdamW(model.parameters(), lr=2e-5, weight_decay=0.01)

# 保存初始参数用于约束计算

initial_params = {name: param.clone() for name, param in model.named_parameters() if param.requires_grad}

# 训练设置

device = torch.device("cuda" if torch.cuda.is_available() else "cpu")

model.to(device)

num_epochs = 3

alpha = 0.5                    # 新旧数据损失的平衡系数

beta = 0.1                     # 参数约束强度系数

# 增量训练循环

for epoch in range(num_epochs):

    model.train()

    total_loss = 0

    for batch in dataloader:

        input_ids = batch["input_ids"].to(device)

        attention_mask = batch["attention_mask"].to(device)

        labels = batch["labels"].to(device)

        optimizer.zero_grad()

        # 计算输出和损失

        outputs = model(input_ids, attention_mask=attention_mask)

        loss = F.cross_entropy(outputs.logits, labels)

        # 参数约束损失计算

        constraint_loss = 0.0

        for name, param in model.named_parameters():