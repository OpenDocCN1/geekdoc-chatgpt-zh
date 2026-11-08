值得肯定的是，在那个发展阶段，大量研究者各展所长，通过提出建设性的改进思路持续推动自然语言处理模型的效果提升。同期颇具代表性的知名预训练语言模型还有 GPT、XLNet、MPNet 和 ERNIE。以 XLNet 为例，Zhilin Yang 等人发表论文 “XLNet: Generalized Autoregressive Pretraining for Language Understanding”，在 BERT 的基础上做了多项优化，也让部分任务的表现有所提升，具体改进包括用自回归模型替代自编码模型、引入双向注意力机制并借鉴 Transformer-XL 的设计。

需要指出的是，OpenAI 的 GPT 推出时间比 BERT 更早。两者核心差异在于 BERT 采用双向 Transformer 编码器，能捕捉上下文信息，更适合自然语言理解，而 GPT 采用单向 Transformer 解码器，更适合自然语言生成应用。其他预训练语言模型大多在 BERT 或 GPT 这两大范式上做优化，主要集中在方法论层面，而非单纯堆砌参数与复杂度。

总体来看，以上预训练语言模型的参数规模基本都在 4 亿以下，因此在此基础上做垂直任务的调优成本可控，即便是中小型创业公司也能承受，这也是预训练语言模型得以在各行业快速落地的关键原因之一。

# 7. 大模型启航期（2022—）
2022 年 11 月，OpenAI 发布了 ChatGPT。其出色的自然语言生成与推理能力使其迅速走红全球。短短 2 个月内，活跃用户就超过 1 亿人，也掀起了 AGI 发展的新一轮热潮。

与传统数亿参数级的预训练语言模型相比，ChatGPT 参数量高达 1750 亿个，是名副其实的大语言模型。它在聊天、机器翻译、文案撰写、代码撰写等多项自然语言处理任务中表现突出。

尽管大模型在生成、推理、问答、泛化等方面展现出很强的能力，但距离 AGI 仍有较大差距，前路漫长。我们大胆判断，LLM 技术充其量是通向 AGI 征程上的“过客”，或是一个至关重要的技术手段。预计随着算力与计算效率的提升，未来的 AGI 技术也将是复杂的大模型，其参数规模很可能超过现今的 GPT-4。

如果与算力相关的技术能快速演进，让算力性价比大幅提升，就能降低中小企业的建模与微调门槛，使大部分中小企业能够负担高额支出，从而加快 GPT-4 的应用节奏，真正驶入快车道。

从严格意义上说，随着 GPT-4 的推出，AI 已进入多模态大模型时代。如图 2-5 所示，我们认为单模态大模型 ChatGPT 只是过渡产物，其高光时刻停留在 2022 年，大模型的未来属于多模态。从效果来看，GPT-4 的表现和使用体验远超 ChatGPT。换句话说，GPT-4 完全可以替代 ChatGPT，但反之则不行。

多模态大模型将引发各行业的范式革命，AI 将成为各行业的基础设施，推动数智化转型和商业价值的大幅提升。2023 年将是多模态大模型的元年，未来 3～5 年将是其高速发展期。

尽管我们认为相较 GPT-4，单模态大模型 ChatGPT 只是过渡产品，但这并不代表 LLM 没有发展的必要。多模态大模型最核心的两个模态是自然语言和图像信息，这两者的进步至关重要，能为多模态大模型提供有力支撑。

# 2.2 小模型 BERT 到大模型 ChatGPT 的跨越
回想 BERT 流行的“小模型时代”，大概两台低显存的 GPU 服务器就能完成大部分模型的训练。若构建者理解 BERT 算法原理，整个训练与优化过程十分透明可控，也让人更放心。这种“可信”“透明”的特质让 AI 飞入寻常百姓家，让中小企业也能在某个垂直领域轻松驾驭 AI。

进入大模型时代，若要从零训练模型，对算力与财力的要求极高，动辄上千万元的投入，中小企业难以承受，从零到一的自研并不现实。

即使是微调，两台低显存 GPU 服务器也只能以几十亿参数的模型作为底座。若要以千亿参数级的模型做底座进行微调，至少需要数十台显存大于 16GB 的高显存 GPU 服务器，这是一笔巨大开销。

此外，大模型微调对算法工程师而言更像是操作“黑箱”，喂入数据后效果如何只能靠反复测试来判断。没有充足算力支持，单次微调耗时极长，工程师在微调过程中能发挥的作用十分有限。

从结果看，ChatGPT 在文本生成和推理等任务上的效果明显优于 BERT 模型，后续章节会详述。随之而来的后果是中小企业开始玩不起 AI 了。因此不禁要问：ChatGPT 为何能火到如此程度？为何需要如此庞大的参数量？BERT 与 ChatGPT 的差距究竟有多大？具体差别在哪里？

# 2.3 BERT 到底解决了哪些问题
BERT 模型由谷歌于 2018 年发布。其主要创新在于提出预训练思想，并以 Transformer 编码器作为基础架构。

在 BERT 提出之前，OpenAI 已发布 GPT-1。从 Jacob Devlin 等人发表的论文 “BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding” 中可见，他们对 GPT-1 已有关注。

# 表2-2

| 评论 | 情感评分 |
| ---- | -------- |
| Assured, glossy and shot through with brittle desperation. | 0.763 89 |
| It's a big time stinker. | 0.111 11 |
| At best this is a film for the under-7 crowd. But it would be better to wait for the video. And a very rainy day. | 0.277 78 |
| The turntable is now outselling the electric guitar... | 0.5 |
| comes from the heart. | 0.75 |

情感分析是自然语言处理的重要能力，对人类而言只要正确理解评论含义，判断情感就很容易。对机器人来说，情感分析仍是一项重大挑战。

# 3. 语义是否相近的判断
该任务使用 MRPC（Microsoft Research Paraphrase Corpus，微软研究语义解释语料库）数据集。该数据集包含从网络新闻中提取的 5800 个句子对。标注者人工标注句子对是否语义相近，相近则标记为 1，否则为 0。

例如下面两句被标记为语义相近，故标注为 1。
句子 1: Amrozi accused his brother, whom he called "the witness", of deliberately distorting his evidence.
句子 2: Referring to him as only "the witness", Amrozi accused his brother of deliberately distorting his evidence.

再例如下面两句被标记为语义不相近，故标注为 0。
句子 1: A BMI of 25 or above is considered overweight; 30 or above is considered obese.
句子 2: A BMI between 18.5 and 24.9 is considered normal, over 25 is considered overweight and 30 or greater is defined as obese.

# 4. 语义相近程度的评分
该任务使用 STS-B（Semantic Textual Similarity Benchmark，语义文本相似度基准）数据集。该数据集收集了来自图片注释、新闻头条、社区论坛等不同来源的 8628 个句子对。标注者对每对句子的语义相近程度打分，1 分到 5 分，分数越高表示越相近。

例如下面两句语义相近程度被标记为 5 分。
句子 1：Neither was there a qualified majority within this House to revert to Article 272.
句子 2：There was not a majority voting in Parliament to go back to Article 272.

再例如下面两句语义相近程度被标记为 1 分。
句子 1：The man played follow the leader on the grass.
句子 2：The rhino grazed on the grass.

# 5. 问题对的语义相近
该任务使用 QQP（Quora Question Pairs，问答对）数据集。该数据集收集了 Quora 网站上的各种问题对。标注者标注两个问题是否语义相近，相近则为 1，否则为 0。与 MRPC 的差异在于，QQP 专门针对问题对，判断问题对的语义是否相近。

# 6. 句子对关系判断
该任务使用 SNLI（The Stanford Natural Language Inference，斯坦福自然语言推理）数据集，约含 570 000 个句子对。每个句子对的第一个句子为前提，第二个为推断。

同时数据集还包含句子对之间关系的标注，主要有 3 类：蕴含、矛盾、 neutral。用于处理相近任务的数据集还有 MNLI 和 RTE。

例如下面两句关系为蕴含。
前提：A soccer game with multiple males playing.
推断：Some men are playing a sport.

再例如下面两句关系为相互矛盾。
前提：A man inspects the uniform of a figure in some East Asian country.
推断：The man is sleeping.

# 7. 问答
该任务使用 QNLI（Qusetion-answering Natural Language Inference，问答自然语言推理）数据集。该数据集主要用于自然语言推理任务。

每个测试任务包含一个问题和一个语句，模型需判断两者是否存在蕴含关系，蕴含则标注为 1，不蕴含则为 0。

# 8. 实体识别
该任务采用 NER（Named Entity Recognition，命名实体识别）数据集。该数据集包含 20 万个单词，每个单词都被标注为四类实体之一：Person、人，Organization、组织，Location、方位，Miscellaneous、其他实体，整体为四分类任务。

# 9. 阅读理解
该任务使用 SQuAD（The Stanford Question Answering Dataset，斯坦福问答数据集）。该数据集由维基百科文章上提出的问题、包含答案的文字描述和答案组成。任务是预测段落中答案文本范围或输出“No Answer”。目前最新版本为 SQuAD2.0。下面举例说明。

文字描述：Computational complexity theory is a branch of the theory of computation in theoretical computer science that focuses on classifying computational problems according to their inherent difficulty, and relating those classes to each other. A computational problem is understood to be a task that is in principle amenable to being solved by a computer, which is equivalent to stating that the problem may be solved by mechanical application of mathematical steps, such as an algorithm.

问题 1：What branch of theoretical computer science deals with broadly classifying computational problems by difficulty and class of relationship？
回答：computational problems

问题 2：What branch of theoretical computer class deals with broadly classifying computational problems by difficulty and class of relationship？
回答：<No Answer>

# 10. 完形填空
该任务使用 SWAG（Situations With Adversarial Generations，对抗生成的情境）数据集。该数据集包含 113 000 个完形填空句子，每个句子有部分词语空缺。任务是对词语进行补全。

从 Jacob Devlin 等人的研究成果中可见（如表 2-3 所示），从准确性角度...

# 表2-3

| 模型 | 使用MNLI、QQP、QNLI、SST-2、CoLA、STS-B、MRPC和RTE数据集的平均测试效果 | 使用QQP数据集的测试效果 | 使用CoLA数据集的测试效果 | 使用RTE数据集的测试效果 |
| GPT | 75.1 | 70.3 | 45.4 | 56.0 |
| BERT模型 | 79.6 | 71.2 | 52.1 | 66.4 |
| BERT大模型 | 82.1 | 72.1 | 60.5 | 70.1 |

目前很多知名模型使用 CoLA 数据集处理语法对错二分类任务，使用 QQP 数据集处理问题对语义相近任务，使用 RTE 数据集处理语义理解任务的效果都比较差。即使是 BERT 模型，在这几个数据集上的表现也差强人意，这说明在 BERT 诞生之时（2018 年），自然语言理解在语法正确判断和复杂语义理解上仍未得到较好解决。

下面继续看 BERT 大模型在另外两个较高阶、较难的任务中的表现。表 2-4 和 2-5 分别为 BERT 大模型在 SQuAD 和 SWAG 数据集上的测试效果。

# 表2-4

| 模型 | EM（完全匹配）评分 | F1评分 |
| Human（人类） | 86.9 | 89.5 |
| BERT大模型（Single） | 80.0 | 83.1 |

# 表2-5

| 模型 | Accuracy（准确率）评分 |
| Human | 85.0 |
| BERT大模型 | 86.3 |

① 使用数据集的测试效果用 0～100 表示。0 表示效果最差，100 表示效果最好。

从表 2-4 可知，BERT 大模型在阅读理解任务上的表现十分优秀，尽管与人类仍有差距，但差距不大。

从表 2-5 可知，BERT 大模型在完形填空任务上的表现略优于人类。

综上所述，尽管 BERT 整体表现优秀，但在语法对错二分类、问题对语义相近等任务上仍显不足。另外 BERT 大模型在阅读理解上与人类仍有差距。BERT 的这些能力缺陷成为后续模型重点突破的方向，也为 ChatGPT 提供了研究方向。

此外，随着 BERT 的成功，业界总结出提升自然语言处理效果的 3 个方向，也为后续更优秀模型 如 ChatGPT 的研发指明方向。

+ (1) BERT 大模型的效果显著优于 BERT 模型，因此增加深度学习层数、提升参数量成为行业优化方向之一。
+ (2) 预训练模型与微调机制的结合也成为优化自然语言处理效果的重要方向。