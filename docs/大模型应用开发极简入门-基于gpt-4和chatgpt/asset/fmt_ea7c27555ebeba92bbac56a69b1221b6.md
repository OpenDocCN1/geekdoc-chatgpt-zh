
```
{
    "schema_version": "v1",
    "name_for_human": "TODO Plugin",
    "name_for_model": "todo",
    "description_for_human": "Plugin for managing a TODO list. \
        You can add, remove and view your TODOs.",
    "description_for_model": "Plugin for managing a TODO list. \
        You can add, remove and view your TODOs.",
    "auth": {
        "type": "none"
    },
    "api": {
        "type": "openapi",
        "url": "http://localhost:3333/openapi.yaml",
        "is_user_authenticated": false
    },
    "logo_url": "http://localhost:3333/logo.png",
    "contact_email": "support@thecompany.com",
    "legal_info_url": "http://thecompany-url/legal"
}
```

表 5-1 详细列出了该文件中的一些字段。

表 5-1：`ai-plugin.json` 文件中的一些字段

| 字段名称 || 类型 || 描述 |
| --- | --- | --- |
| `name_for_human` || 字符串 || 人们看到的名称。这可以是公司的全名，但必须少于 20 个字符 |
| --- | --- | --- |
| `name_for_model` || 字符串 || 模型用于识别插件的简短名称。它只能包含字母和数字，并且不能超过 50 个字符 |
| `description_for_human` || 字符串 || 对插件功能的简单说明，供人们阅读，应少于 100 个字符 |
| `description_for_model` || 字符串 || 详细说明，帮助 AI 理解插件。因此，向模型解释插件的目的至关重要。该字段最多可以有 8000 个字符 |
| `logo_url` || 字符串 || 插件标识的 URL。标识的尺寸最好为 512 像素 × 512 像素 |
| `contact_email` || 字符串 || 人们在需要帮助时可以联系的电子邮件地址 |
| `legal_info_url` || 字符串 || 通过该 URL，人们可以查找有关插件的更多详细信息 |

### 5.2.4 OpenAPI 规范

创建插件的下一步是创建 `openapi.yaml` 文件。该文件必须遵循 OpenAPI 标准（详见后文）。GPT 模型只通过此文件和插件清单文件中的详细信息来了解你的 API。

以下是一个示例，包含待办事项列表定义插件的 `openapi.yaml` 文件的第一行：

```
openapi: 3.0.1
info:
  title: TODO Plugin
  description: A plugin that allows the user to create and manage a
  TODO list using ChatGPT. If you do not know the user's username,
  ask them first before making queries to the plugin. Otherwise,
  use the username "global".
  version: 'v1'
servers:
  - url: http://localhost:5003
paths:
  /todos/{username}:
    get:
      operationId: getTodos
      summary: Get the list of todos
      parameters:
      - in: path
        name: username
        schema:
            type: string
        required: true
        description: The name of the user.
      responses:
        "200":
          description: OK
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/getTodosResponse'
[...]
```

可以将 OpenAPI 规范视为描述性文档，它足以理解和使用 API。在 GPT-4 中进行搜索时，使用 `info` 部分中的描述来确定插件与搜索内容的相关性。OpenAPI 规范的其余内容遵循标准的 OpenAPI 格式。许多工具可以根据现有的 API 代码自动生成 OpenAPI 规范。

> **理解 OpenAPI 规范**
>
> OpenAPI 规范（以前被称为 Swagger 规范）是描述 HTTP API 的标准。OpenAPI 定义允许消费者与远程服务进行交互，而无须提供额外的文档或访问源代码。OpenAPI 文档可为各种有价值的用例打下基础，比如生成 API 文档、通过代码生成工具以多种编程语言创建服务器和客户端、促进测试过程等。
>
> OpenAPI 文档以 JSON 格式或 YAML 格式定义或描述 API 及其元素。OpenAPI 文档的内容一般从标题、描述和版本号开始。
>
> 若想深入了解这个主题，请查看 OpenAPI GitHub repo，其中包含文档和各种示例。

### 5.2.5 描述

当插件有助于响应用户请求时，模型会扫描 OpenAPI 规范中的端点描述及插件清单文件中的 `description_for_model` 字段。你的目标是创建最合适的响应，这通常涉及测试不同的请求和描述。

OpenAPI 文档应该提供关于 API 的各种细节，比如可用的函数及其参数。它还应包含特定于属性的描述字段，提供有价值的解释，说明每个函数的作用及查询字段期望的信息类型。这些描述指导模型以最恰当的方式使用 API。

在这个过程中的关键要素是 `description_for_model` 字段。通过它，你可以通知模型如何使用插件。我们强烈建议为该字段创建简明、清晰和描述性强的说明。

在撰写描述时，必须遵循以下最佳实践。

* 不要试图影响 GPT 的“情绪”、个性或确切回应。
* 避免指示 GPT 使用特定的插件，除非用户明确要求使用该类别的服务。
* 不要为 GPT 指定特定的触发器来使用插件，因为它旨在自主确定何时使用插件。

回顾一下，开发 GPT-4 插件涉及创建一个 API，使用 OpenAPI 规范来指定其行为，并在插件清单文件中描述插件及其用法。通过这种设置，GPT-4 可以有效地充当智能的 API 调用者，从而获得超越文本生成的能力。

## 5.3 小结

LangChain 框架和 GPT-4 插件有助于大幅提升 LLM 的潜力。

凭借其强大的工具和模块套件，LangChain 已成为 LLM 领域的核心框架。

它在集成不同模型、管理提示词、组合数据、为链排序、处理智能体和管理记忆等方面的多功能性为开发人员和 AI 爱好者开辟了新的道路。第 3 章中的示例证明，使用 GPT-4 和 ChatGPT 从头开始编写复杂指令存在局限性。请记住，LangChain 的真正潜力在于创造性地利用各种功能来解决复杂问题，并将通用语言模型转化为功能强大且具体的应用程序。

GPT-4 插件是语言模型和实时可用的上下文信息之间的桥梁。如本章所述，开发插件需要结构良好的 API 和描述性文件。因此，开发人员必须在这些文件中提供详细和自然的描述。这将有助于 GPT-4 充分利用 API。

LangChain 框架和 GPT-4 插件证明，LLM 等 AI 领域正在迅猛发展，本章仅展示了其颠覆性潜力的冰山一角。

## 5.4 总结

本书为你提供了必要的基础知识和进阶知识，以帮助你在真实的应用程序中利用 LLM 的潜力。我们一起学习了基本原理和 API 集成知识，了解了提示工程和微调，并研究了 GPT-4 和 ChatGPT 的使用案例。在本书的末章中，我们详细了解了如何利用 LangChain 框架和 GPT-4 插件 2充分发挥 LLM 的潜力，并真正构建创新的应用程序。

22023 年 11 月 7 日，OpenAI 在首届开发者大会上发布了可定制版本的 ChatGPT，称为 GPTs。与此同时，OpenAI 宣布将在 2024 年初上线 GPTs 应用商店，允许用户发布自己创建的 GPT 并获得收益。GPTs 应用商店发布后将替代原插件市场。——译者注

有了这些工具，我们就可以在 AI 领域中进一步开拓，开发利用这些先进语言模型的应用程序。但请记住，AI 领域的发展是动态的，要时刻关注进展并相应地进行调整。进入 LLM 世界只是开始，你的探索不应止步于此。我们鼓励你利用新知识探索 AI 技术的未来。

# 术语表

术语表旨在定义和解释本书涉及的关键术语，其中许多关键术语在各章中反复出现。术语表有助于快速查看相关概念。

你可以在术语表中找到对于理解 GPT-4 和 ChatGPT 及使用 OpenAI 库至关重要的技术术语、缩略词和概念。

**agent（智能体）**

　　一种以大语言模型驱动的人工智能程序，能够自主感知环境并采取行动以实现目标，拥有自主推理决策、规划行动、检索记忆、选择工具执行任务等能力。

**AI hallucination（AI 幻觉）**

　　AI 生成的内容与现实世界的知识不一致或与实际数据显著不同的现象。

**application program interface（API，应用程序接口）**

　　应用程序交互所需的一组定义和协议。API 描述了程序必须使用的方法和数据格式，以与其他软件进行通信。比如，OpenAI 允许开发人员通过 API 使用 GPT-4 和 ChatGPT。

**artificial intelligence（AI，人工智能）**

　　计算机科学的一个领域，专注于创建算法以执行传统上由人类执行的任务，比如处理自然语言、分析图像、解决复杂问题和做出决策。

**artificial neural network（人工神经网络）**

　　受人脑结构启发的计算模型，用于处理复杂的机器学习任务。它由相互连接的神经元层组成，通过加权连接来转换输入数据。一些类型的人工神经网络（如循环神经网络）可用于处理具有记忆元素的顺序数据，而其他类型的人工神经网络（如基于 Transformer 架构的模型）则使用注意力机制来衡量不同输入的重要性。大语言模型是人工神经网络的一个显著应用。

**attention mechanism（注意力机制）**

　　神经网络架构的一个组件，它使模型在生成输出时能够关注输入的不同部分。注意力机制是 Transformer 架构的关键，使其能够有效地处理长数据序列。

**catastrophic forgetting（灾难性遗忘）**

　　这是模型的一种倾向，具体指模型在学习新数据时忘记先前学到的信息。这种限制主要影响循环神经网络。循环神经网络在处理长文本序列时难以保持上下文。

**chain of thought（CoT，思维链）**

　　一种提示工程技术，核心思想是通过向大语言模型展示少量的示例，在示例中将具体问题拆分成多个推理步骤，并要求模型遵循多步，比如“让我们逐步思考”。这会改善模型在执行复杂的推理任务（算术推理、常识推理和符号推理）时的表现。

**chatbot（聊天机器人）**

　　用于通过文本（或文本转语音）进行聊天式对话的应用程序。聊天机器人通常用于模拟人类的讨论和互动。现代聊天机器人是使用大语言模型开发的，并且拥有较强的语言处理能力和文本生成能力。

**context window（上下文窗口）**

　　大语言模型在生成信息时可以处理的目标标记周围的文本范围。上下文窗口大小对于理解和生成与特定上下文相关的文本至关重要。一般而言，较大的上下文窗口可以提供更丰富的语义信息。

**deep learning（DL，深度学习）**

　　机器学习的一个子领域，专注于训练具有多层的神经网络，从而实现对复杂模式的学习。

**embedding（嵌入）**

　　表示词语或句子且能被机器学习模型处理的实值向量。对于值较为接近的向量，它们所表示的词语或句子也具有相似的含义。在信息检索等任务中，嵌入的这种特性特别有用。

**Facebook AI Similarity Search（Faiss，Facebook AI 相似性搜索）**

　　Facebook AI 团队开源的针对聚类和相似性搜索的库，为稠密向量提供高效的相似性搜索和聚类，支持十亿级别向量的搜索，是目前较为成熟的近似近邻搜索库。

**few-shot learning（少样本学习）**

　　一种仅用很少的示例训练机器学习模型的技术。对于大语言模型而言，这种技术可以根据少量的输入示例和输出示例来引导模型响应。

**fine-tuning（微调）**

　　在微调过程中，预训练模型（如 GPT-3 或其他大语言模型）在一个较小、特定的数据集上进一步训练。微调旨在重复使用预训练模型的特征，并使其适应于特定任务。对于神经网络来说，这意味着保持结构不变，仅稍微改变模型的权重，而不是从头开始构建模型。

**foundation model（基础模型）**

　　一类 AI 模型，包括但不限于大语言模型。基础模型是在大量未标记数据上进行训练的。这类模型可以执行各种任务，如图像分析和文本翻译。基础模型的关键特点是能够通过无监督学习从原始数据中学习，并能够通过微调来执行特定任务。

**function call（函数调用）**

　　OpenAI 开发的一项功能，它允许开发人员在调用 GPT 模型的 API 时，描述函数并让模型智能地输出一个包含调用这些函数所需参数的 JSON 对象。利用它，我们可以更可靠地将 GPT 的能力与外部工具和 API 相结合。

**Generative AI（GenAI，生成式人工智能）**

　　人工智能的一个子领域，专注于通过学习现有数据模式或示例来生成新的内容，包括文本、代码、图像、音频等，常见应用包括聊天机器人、创意图像生成和编辑、代码辅助编写等。

**Generative Pre-trained Transformer（GPT，生成式预训练 Transformer）**

　　由 OpenAI 开发的一种大语言模型。GPT 基于 Transformer 架构，并在大量文本数据的基础上进行训练。这类模型能够通过迭代地预测序列中的下一个单词来生成连贯且与上下文相关的句子。

**inference（推理）**

　　使用训练过的机器学习模型进行预测和判断的过程。

**information retrieval（信息检索）**

　　在一组资源中查找与给定查询相关的信息。信息检索能力体现了大语言模型从数据集中提取相关信息以回答问题的能力。

**LangChain**

　　一个 Python 软件开发框架，用于方便地将大语言模型集成到应用程序中。

**language model（语言模型）**

　　用于自然语言处理的人工智能模型，能够阅读和生成人类语言。语言模型是对词序列的概率分布，通过训练文本数据来学习一门语言的模式和结构。

**large language model（LLM，大语言模型）**

　　具有大量参数（参数量通常为数十亿，甚至千亿以上）的语言模型，经过大规模文本语料库的训练。GPT-4 和 ChatGPT 就属于 LLM，它们能够生成自然语言文本、处理复杂语境并解答难题。

**long short-term memory（LSTM，长短期记忆）**

　　一种用于处理序列数据中的短期及长期依赖关系的循环神经网络架构。然而，基于 Transformer 的大语言模型（如 GPT 模型）不再使用 LSTM，而使用注意力机制。

**machine learning（ML，机器学习）**

　　人工智能的一个子领域，其主要任务是创建智能算法。这些算法就像学生一样，它们从给定的数据中自主学习，无须人类逐步指导。

**machine translation（机器翻译）**

　　使用自然语言处理和机器学习等领域的概念，结合 Seq2Seq 模型和大语言模型等模型，将文本从一门语言翻译成另一门语言。

**multimodal model（多模态模型）**

　　能够处理和融合多种数据的模型。这些数据可以包括文本、图像、音频、视频等不同模态的数据。它为计算机提供更接近于人类感知的场景。

***n*\-gram**

　　一种算法，常用于根据词频预测字符串中的下一个单词。这是一种在早期自然语言处理中常用的文本补全算法。后来，*n*\-gram 被循环神经网络取代，再后来又被基于 Transformer 的算法取代。

**natural language processing（NLP，自然语言处理）**

　　人工智能的一个子领域，专注于计算机与人类之间的文本交互。它使计算机程序能够处理自然语言并做出有意义的回应。

**OpenAI**

　　位于美国的一个人工智能实验室，它由非营利实体和营利实体组成。OpenAI 是 GPT 等模型的开发者。这些模型极大地推动了自然语言处理领域的发展。

**OpenAPI**

　　OpenAPI 规范是描述 HTTP API 的标准，它允许消费者与远程服务进行交互，而无须提供额外的文档或访问源代码。OpenAPI 规范以前被称为 Swagger 规范。

**parameter（参数）**

　　对大语言模型而言，参数是它的权重。在训练阶段，模型根据模型创建者选择的优化策略来优化这些系数。参数量是模型大小和复杂性的衡量标准。参数量经常用于比较大语言模型。一般而言，模型的参数越多，它的学习能力和处理复杂数据的能力就越强。

**plugin（插件）**
