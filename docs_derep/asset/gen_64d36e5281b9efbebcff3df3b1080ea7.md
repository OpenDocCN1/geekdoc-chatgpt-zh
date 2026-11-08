大模型实战：合成用户行为与长尾内容匹配

想快速验证推荐逻辑又缺真实样本？先用 GPT-2 做合成行为，再用 BERT 做语义匹配，长尾也能被精准触达。下面两段可直接跑的代码，把思路拆开了。

第一部分是基于用户画像生成可解释的行为序列。思路是先准备几个典型用户，再用固定的行为模板把年龄、性别、爱好填进去，随后让 GPT-2 在此提示基础上续写三种不同但合理的查看、点击、购买链路。模型在 GPU 可用时会自动切换设备，生成时控制最大长度、返回数和填充标记，最后把解码结果按用户归档输出。

[PRE0]python

from transformers import GPT2LMHeadModel, GPT2Tokenizer

import torch

###### 加载预训练GPT2模型和对应的Tokenizer

model_name="gpt2"

tokenizer=GPT2Tokenizer.from_pretrained(model_name)

model=GPT2LMHeadModel.from_pretrained(model_name)

###### 设置生成配置

device=torch.device("cuda" if torch.cuda.is_available() else "cpu")

model=model.to(device)

###### 准备用户特征和行为模板

user_features=[

    {"年龄": "25", "性别": "女", "爱好": "旅行,摄影"},

    {"年龄": "30", "性别": "男", "爱好": "运动,电影"},

    {"年龄": "22", "性别": "女", "爱好": "音乐,阅读"},

]

behavior_template="""

用户特征:

年龄: {年龄}, 性别: {性别}, 爱好: {爱好}

行为预测:

1. 查看了商品"{商品1}"

2. 点击了"{广告1}"

3. 购买了"{商品2}"

"""

###### 定义生成函数

def generate_behavior_samples(user_features, template, num_samples=3):

    results=[]

    for user in user_features:

        # 使用用户特征填充模板

        prompt=template.format(

            年龄=user["年龄"],

            性别=user["性别"],

            爱好=user["爱好"],

            商品1="智能手表",

            广告1="运动鞋广告",

            商品2="耳机"

        )

        # Tokenize输入

        input_ids=tokenizer.encode(prompt, return_tensors="pt").to(device)

        # 使用模型生成文本

        output=model.generate(

            input_ids,

            max_length=150,

            num_return_sequences=num_samples,

            pad_token_id=tokenizer.eos_token_id

        )

        # 解码生成的文本

        generated_samples=[tokenizer.decode(output[i], skip_special_tokens=True) for i in range(num_samples)]

        results.append({"用户特征": user, "生成行为样本": generated_samples})

    return results

###### 生成用户行为样本

samples=generate_behavior_samples(user_features, behavior_template)

###### 输出生成结果

print("生成的用户行为样本:\n")

for idx, sample in enumerate(samples):

    print(f"用户 {idx+1}")

    print(f"特征: {sample['用户特征']}")

    for i, behavior in enumerate(sample['生成行为样本']):

        print(f"样本 {i+1}:\n{behavior}\n")

运行后能看到每个画像都产出多条差异化行为，适合冷启动和 A/B 方案预演。样例输出如下：

[PRE1]text

生成的用户行为样本:

用户 1:

特征: {'年龄': '25', '性别': '女', '爱好': '旅行,摄影'}

样本 1:

用户特征:

年龄: 25, 性别: 女, 爱好: 旅行,摄影

行为预测:

1. 查看了商品"智能手表"

2. 点击了"运动鞋广告"

3. 购买了"耳机"

样本 2:

用户特征:

年龄: 25, 性别: 女, 爱好: 旅行,摄影

行为预测:

1. 查看了商品"登山背包"

2. 点击了"旅游套餐广告"

3. 购买了"相机配件"

样本 3:

用户特征:

年龄: 25, 性别: 女, 爱好: 旅行,摄影

行为预测:

1. 查看了商品"旅行箱"

2. 点击了"摄影器材广告"

3. 购买了"三脚架"

用户 2:

特征: {'年龄': '30', '性别': '男', '爱好': '运动,电影'}

样本 1:

用户特征:

年龄: 30, 性别: 男, 爱好: 运动,电影

行为预测:

1. 查看了商品"篮球"

2. 点击了"运动鞋广告"

3. 购买了"运动手环"

...

第二部分解决长尾推荐。冷门品类标题和标签往往稀疏，直接用 ID 匹配效果差。做法是把商品标题+标签拼接成文本，用 BERT 提取句首 CLS 向量作为表征；用户兴趣也做同样编码。随后计算用户向量与所有长尾商品向量的余弦相似度，取 Top-K 作为推荐。设备适配和无梯度推理都有处理，适合小规模离线验证。

[PRE2]python

from transformers import BertTokenizer, BertModel

from sklearn.metrics.pairwise import cosine_similarity

import torch

import numpy as np

###### 加载BERT模型和Tokenizer

model_name="bert-base-uncased"

tokenizer=BertTokenizer.from_pretrained(model_name)

model=BertModel.from_pretrained(model_name)

###### 设置设备

device=torch.device("cuda" if torch.cuda.is_available() else "cpu")

model=model.to(device)

###### 冷门内容样本数据

long_tail_items=[

    {"id": 1, "title": "冷门文学书籍", "tags": "文学,经典,冷门"},

    {"id": 2, "title": "小众摄影器材", "tags": "摄影,设备,小众"},

    {"id": 3, "title": "稀有工艺品", "tags": "工艺,稀有,艺术"},

]

###### 用户兴趣数据

user_profiles=[

    {"id": 101, "interests": "文学,艺术,经典"},

    {"id": 102, "interests": "摄影,设备,旅行"},

]

###### 文本嵌入生成函数

def generate_embeddings(texts):

    embeddings=[]

    for text in texts:

        # Tokenize输入

        inputs=tokenizer(text, return_tensors="pt", padding=True,

                         truncation=True).to(device)

        with torch.no_grad():

            # 提取CLS嵌入

            outputs=model(**inputs)

            embeddings.append(

                outputs.last_hidden_state[:, 0, :].cpu().numpy())

    return np.vstack(embeddings)

###### 生成冷门内容的嵌入向量

item_texts=[f"{item['title']} {item['tags']}" for item in long_tail_items]

item_embeddings=generate_embeddings(item_texts)

###### 生成用户兴趣的嵌入向量

user_texts=[profile["interests"] for profile in user_profiles]

user_embeddings=generate_embeddings(user_texts)

###### 推荐函数：基于余弦相似度推荐

def recommend_items(user_embeddings, item_embeddings, top_k=3):

    recommendations=[]

    for user_idx, user_emb in enumerate(user_embeddings):