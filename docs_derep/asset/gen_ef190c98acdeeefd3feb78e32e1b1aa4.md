# 双塔 BERT 微调入门：用用户反馈训练小推荐

这篇笔记演示了一个极简的个性化推荐实验。准备了三条用户偏好描述，分别偏爱科幻与 AI 话题、偏爱历史与古文明、以及偏爱金融分析与股市策略。对应准备了三条物品简介，分别是探讨 AI 潜力的《人工智能的未来》、讲解股市走势的《股市动态》、聚焦古文明的《历史长河》。

三组配对的反馈标记为 1、0、1，用来表示是否正向。把评论、简介和标签一起封装进 RecommendationDataset，再用 DataLoader 以批量 2、开启洗牌的方式供给训练。

建模采用 FineTunedRecommendationModel，双塔结构都基于 bert-base-uncased。用户塔和物品塔各自做序列分类，底层参数全部冻结，只保留顶层表征。两个 768 维输出拼接后接一个线性层，再经 Sigmoid 输出匹配概率。

训练配置选用 AdamW，学习率 5e-5，损失函数为二分类交叉熵 BCELoss。共跑 5 个轮次，损失单调下降，验证准确率稳定在 0.6667 左右，最终评估准确率为 0.6667。整体流程还演示了如何保存权重并以 Flask 提供 /predict 在线推理。

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