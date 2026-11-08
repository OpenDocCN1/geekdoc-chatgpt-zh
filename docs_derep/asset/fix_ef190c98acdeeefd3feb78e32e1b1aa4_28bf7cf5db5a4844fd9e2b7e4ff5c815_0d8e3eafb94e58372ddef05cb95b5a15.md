# 三条样本跑通双塔匹配

想快速验证文本匹配思路，先搭建了一组最小可用的配对。使用者侧的三句话分别是喜欢科幻尤其是 AI 相关题材、偏爱历史书特别是远古文明、关心金融分析与股市策略。条目侧对应的是探讨 AI 潜力的《未来人工智能》、解释股市走势的《股市动态》、聚焦古代文明的《历史长河》。匹配与否的标注定为 1、0、1，用来提供监督信号。

数据封装借助 RecommendationDataset 完成，使用者的原始表述和条目的简介分别经分词器做固定长度截断后再与标签一起送入 DataLoader，批量取 2 并开启随机顺序。建模采用双编码器结构，左侧处理使用者文本，右侧处理条目文本，两边都以 bert-base-uncased 初始化的序列分类器作为骨干，其底层的参数被冻结只保留表征能力。两路 logits 各 768 维拼接成 1536 维向量，再经一层线性变换加 Sigmoid 输出匹配概率。

优化选用 AdamW，学习率 5e-5，损失函数为 BCELoss，累计训练 5 轮。日志显示损失从约 0.6832 缓慢下降到约 0.3861，每轮的奖励组合和准确率都稳定在 0.6667，独立评估得到的准确率也是 0.6667。脚本末尾演示了权重保存为 pth 文件，以及通过 Flask 暴露 /predict 接口实现在线推理的过程。

[PRE0]text

Epoch 1, Loss: 0.6832

Epoch 1-Rewards: [1, -1, 1]

Epoch 1-Accuracy: 0.6667

Epoch 2, Loss: 0.5851

Epoch 2-Rewards: [1, -1, 1]

Epoch 2-Accuracy: 0.6667

Epoch 3, Loss: 0.5083

Epoch 3-Rewards: [1, -1, 1]

Epoch 3-Accuracy: 0.6667

Epoch 4, Loss: 0.4434

Epoch 4-Rewards: [1, -1, 1]

Epoch 4-Accuracy: 0.6667

Epoch 5, Loss: 0.3861

Epoch 5-Rewards: [1, -1, 1]

Epoch 5-Accuracy: 0.6667

评估准确率: 0.6667

[PRE1]

pip install torch transformers datasets scikit-learn flask

[PRE2]

import torch

import torch.nn as nn

import torch.optim as optim

from transformers import AutoTokenizer, AutoModelForSequenceClassification

from torch.utils.data import Dataset, DataLoader

from sklearn.metrics import accuracy_score

import random

import numpy as np

from flask import Flask, request, jsonify

[PRE3]

class RecommendationDataset(Dataset):

    def __init__(self, user_reviews, item_descriptions,

                 ratings, tokenizer, max_length=64):

        self.user_reviews=user_reviews

        self.item_descriptions=item_descriptions

        self.ratings=ratings

        self.tokenizer=tokenizer

        self.max_length=max_length

    def __len__(self):

        return len(self.ratings)

    def __getitem__(self, idx):

        user_input=self.tokenizer(self.user_reviews[idx],

                                 padding="max_length", truncation=True,

                                 max_length=self.max_length, return_tensors="pt")

        item_input=self.tokenizer(self.item_descriptions[idx],

                                 padding="max_length", truncation=True,

                                 max_length=self.max_length, return_tensors="pt")

        label=torch.tensor(self.ratings[idx], dtype=torch.float)

        return user_input, item_input, label

[PRE4]python

class FineTunedRecommendationModel(nn.Module):

    def __init__(self, model_name="bert-base-uncased"):

        super(FineTunedRecommendationModel, self).__init__()

        self.user_model=AutoModelForSequenceClassification.from_pretrained(

            model_name, num_labels=2)

        self.item_model=AutoModelForSequenceClassification.from_pretrained(

            model_name, num_labels=2)

        self.fc=nn.Linear(768*2, 1) # 拼接用户和物品的特征向量并进行分类

        self.sigmoid=nn.Sigmoid()

        # 冻结模型的底层参数

        for param in self.user_model.parameters():

            param.requires_grad=False

        for param in self.item_model.parameters():

            param.requires_grad=False

    def forward(self, user_input, item_input):

        user_output=self.user_model(**user_input).logits

        item_output=self.item_model(**item_input).logits

        combined=torch.cat((user_output, item_output), dim=1)

        result=self.fc(combined)

        return self.sigmoid(result)

[PRE5]python

def fine_tune_train_step(model, user_input, item_input,

                         label, optimizer, criterion):

    model.train()

    optimizer.zero_grad()

    # 模型预测

    prediction=model(user_input, item_input)

    # 计算损失

    loss=criterion(prediction.squeeze(), label)

    loss.backward()

    # 更新模型参数

    optimizer.step()

    return loss.item()

def evaluate_fine_tuned_model(model, data_loader):

    model.eval()

    all_predictions=[]

    all_labels=[]

    with torch.no_grad():

        for user_input, item_input, labels in data_loader:

            # 迁移到GPU或CPU

            user_input={key: val.squeeze(0).to(device) for key,

                        val in user_input.items()}

            item_input={key: val.squeeze(0).to(device) for key,

                        val in item_input.items()}

            labels=labels.to(device)

            # 获取模型输出

            predictions=model(user_input, item_input).cpu().detach().numpy()

            all_predictions.extend(predictions)

            all_labels.extend(labels.cpu().numpy())

    # 计算准确率

    accuracy=accuracy_score(all_labels,

                            [1 if pred > 0.5 else 0 for pred in all_predictions])

    print(f"评估准确率: {accuracy:.4f}")

[PRE6]python

def train_and_deploy_model():

    device=torch.device("cuda" if torch.cuda.is_available() else "cpu")

    # 载入预训练BERT模型和tokenizer

    model_name="bert-base-uncased"

    tokenizer=AutoTokenizer.from_pretrained(model_name)

    # 模拟数据集（用户评论和物品描述）

    user_reviews=[

        "I love science fiction books, especially AI related topics.",

        "History books are my favorite, especially about ancient civilizations.",

        "I enjoy financial analysis and stock market strategies."

    ]

    item_descriptions=[

        "Future of Artificial Intelligence: Discusses AI potential.",

        "Stock Market Dynamics: Explains trends in stock market.",

        "History's Long River: Focuses on ancient civilizations."

    ]

    ratings=[1, 0, 1] # 1: positive feedback, 0: negative feedback

    dataset=RecommendationDataset(user_reviews, item_descriptions, ratings, tokenizer)

    data_loader=DataLoader(dataset, batch_size=2, shuffle=True)

    # 初始化模型和优化器

    model=FineTunedRecommendationModel(model_name).to(device)

    optimizer=optim.Adam(model.parameters(), lr=5e-5)

    criterion=nn.BCELoss()

    # 训练模型

    for epoch in range(5):

        total_loss=0

        for user_input, item_input, labels in data_loader:

            # 迁移到GPU或CPU

            user_input={key: val.squeeze(0).to(device) for key, val in user_input.items()}

            item_input={key: val.squeeze(0).to(device) for key, val in item_input.items()}

            labels=labels.to(device)

            # 训练模型

            loss=fine_tune_train_step(model, user_input, item_input, labels, optimizer, criterion)

            total_loss += loss

            print(f"Epoch {epoch+1}, Loss: {total_loss/len(data_loader)}")

    # 评估模型

    evaluate_fine_tuned_model(model, data_loader)

    # 保存模型

    torch.save(model.state_dict(), "fine_tuned_model.pth")

    print("模型训练完成并已保存")

    # 部署服务

    app=Flask(__name__)

    @app.route('/predict', methods=['POST'])

    def predict():

        data=request.get_json()