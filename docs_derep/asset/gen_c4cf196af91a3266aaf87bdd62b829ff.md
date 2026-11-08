[PRE0]

这个案例展示了借助 SentenceTransformer 引入向量模型，把文本信息映射成向量表示，再搭配 FAISS 索引实现快速检索的思路。新增的数据可以即时完成向量化并写入索引，从而让知识库保持动态演进。

### 8.1.4 自动化数据管道

要让实时知识刷新既高效又可靠，就需要一套自动化的数据管道把数据采集、清洗预处理、知识库更新和模型推理串联成一条闭环。下面以 Apache Kafka 来搭建这条管道的思路如下：

[PRE1]

借助 Kafka 的生产者-消费者机制，可以把从数据采集到知识库更新的全过程自动化。生产者侧实时把新数据推送到指定主题，消费者侧订阅主题并自动触发知识库的更新。

经过上述关键环节的打通，RAG 系统已具备初步的跨领域适应力，能够动态抓取并融合最新信息。后续还可以引入更多高效的检索算法以及大规模实时流处理框架例如 Flink，进一步提升数据处理与检索效率，让 RAG 在金融、新闻等快速变化的场景里表现得更高效、更智能。

## 8.2 跨模态能力增强

多模态 RAG 的核心在于把图像、文本等异构数据统一映射到高维向量空间，在同一空间里检索与生成，因而能产出更丰富、更准确的结果。比如电商里可结合商品图与用户评价做个性化推荐；医疗里能融合医学影像与诊断报告，为医生提供辅助决策。这种跨模态融合既提升了理解深度，也极大拓展了生成形式与应用边界。

当前要攻克的两大问题是多模态向量嵌入和多模态内容生成，下面分别给出可行的实现思路。

### 8.2.1 多模态向量嵌入

在多模态 RAG 里，所有数据类型最终都变成向量，并在向量空间里检索。无论查询是图还是文，系统都能通过相似度搜索定位到相关内容。根据对文本与图像的不同处理方式，嵌入策略大致可归为三类。

#### 1. 单一多模态模型

该思路使用一个多模态模型统一编码图像、文本等不同模态，将其投射到同一向量空间。此法跨模态检索与生成效率高，但对编码模型的质量依赖较强。

下面以预训练的多模态模型如 CLIP 为例，演示如何完成跨模态数据的统一嵌入与检索：

[PRE2]

以上实现用 CLIP 同时对文本和图像做统一编码，得到可以直接比较的嵌入向量。通过计算余弦相似度即可衡量图文相关性。该方式实现了跨模态数据的紧密融合，为 RAG 系统的查询与检索提供了基础支撑。

#### 2. 基于文本的模态转换

这是一种把非文本数据转译为文本描述的策略。所有非文本内容先被转成自然语言描述，再交由文本模型检索与生成。方法依托了当前文本模型的能力，转换过程中或有信息损失，但在多数场景下仍具很强实用性。

下面展示如何先用 BLIP 把图像转成描述，再用文本检索模型进行查询：

[PRE3]

该片段先借助 BLIP 模型把图像描述成文字，随后用 SentenceTransformer 对生成文本进行检索。模态转换让非文本数据与文本数据建立关联，从而简化多模态处理的复杂度。

#### 3. 多编码器方法

此法为不同数据类型配备专属编码器，例如文本编码器、图像编码器等。实现复杂度会上升，但能为每类数据选取最合适的编码器，提升准确性与灵活性。

下面展示同时使用文本编码器和图像编码器进行检索的做法：

[PRE4]
text_embeddings = text_model.encode(text_corpus)

# 处理图像并获取图像嵌入

image_embeddings = []

for img_path in image_corpus:

    img = Image.open(img_path)

    img_inputs = processor(images=img, return_tensors="pt")

    img_embeds = image_model.get_image_features(**img_inputs)

    image_embeddings.append(img_embeds.squeeze().detach().numpy())

image_embeddings = np.vstack(image_embeddings)

# 用户查询

query_text = "A warm place with a couch"

query_embedding = text_model.encode([query_text])

# 检索

text_similarities = cosine_similarity(query_embedding, text_embeddings)

image_similarities = cosine_similarity(query_embedding, image_embeddings)

top_text = text_corpus[np.argmax(text_similarities)]

top_image = image_corpus[np.argmax(image_similarities)]

print("Top Matching Text:", top_text)

print("Top Matching Image:", top_image)

### 8.2.2 多模态内容生成

传统 RAG 生成结果多以文本为主。随着需求多样化，单一模态已难以覆盖复杂场景。

在多模态 RAG 中，内容生成指系统能根据用户输入同时输出文本和图像。这种能力在多处有明显价值：智能助手回答问题时可附带配图；电商平台可依据描述生成商品图文介绍；教育场景可产出图文并茂的教学材料，帮助理解。

多模态生成不仅增强表现力，也提升交互性和实用性。核心难点在于协调文本生成模型与图像生成模型，保证两者语义一致，同时要兼顾生成质量、语义对齐与模型效率等。以下是几种可行的实现方案。

#### 1. 基于文本生成图像

一种直接的做法是先生成文本，再基于该文本用图像生成模型产出对应画面。这样能确保图文在语义层面保持一致，代码示例如下：

[PRE5]