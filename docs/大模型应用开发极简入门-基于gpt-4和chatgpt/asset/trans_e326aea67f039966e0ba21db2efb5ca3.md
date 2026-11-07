

| 所需费用 | 用法：按标记（输入 + 输出）定价 | 用法：按标记（输入 + 输出）定价；可能导致提示词很长 | 用法：按标记（输入 + 输出）定价；可能导致提示词很长 | 训练：对于微调后的 davinci 模型，按标记（输入 + 输出）定价的成本大约是 GPT-3.5 Turbo 的 80 倍。这意味着，如果其他技术导致提示词长度是其 80 倍，则微调在经济上更可取 |
|---|---|---|---|---|
| 总结 | 默认使用 | 如果零样本学习因输出要求而不起作用，则使用少样本学习 | 如果零样本学习因任务过于复杂而不起作用，请尝试使用提示工程技巧 | 如果拥有非常专业的大型数据集，并且其他解决方案无法提供足够好的结果，则应将微调作为最后的手段 |

12截至 2023 年 12 月 2 日，OpenAI 已经对微调数据集的需求进行了优化。通常情况下，你只需要提供 50 ～ 100 个训练示例进行微调，就会看到明显的改进效果。但整体而言，微调在技术难度和实现成本上，仍然高于前面几种方法。——译者注

为了确保成功构建 LLM 驱动型应用程序，开发人员应该尝试其他技术并评估模型的响应准确性和相关性。此外，开发人员应该意识到 LLM 的计算限制，并相应地调整提示词以取得更好的结果。通过整合这些先进技术并不断完善方法，开发人员可以构建强大且富有创意的应用程序，真正释放 GPT-4 和 ChatGPT 的潜力。

在第 5 章中，你将了解到将 LLM 集成到应用程序中的另外两种方式：插件和 LangChain 框架。这些工具使开发人员能够构建富有创意的应用程序，访问最新信息，并简化 LLM 驱动型应用程序的开发。我们还将畅想 LLM 的未来及 LLM 对应用程序开发的意义。

# 第 5 章　使用 LangChain 框架和插件增强 LLM 的功能

本章探讨 LangChain 框架和 GPT-4 的插件。我们将研究 LangChain 框架如何实现与不同语言模型的交互，以及插件在扩展 GPT-4 功能方面的重要性。这些高阶知识对于开发复杂、尖端的 LLM 驱动型应用程序至关重要。

## 5.1　LangChain 框架

LangChain 是专用于开发 LLM 驱动型应用程序的框架。你会发现，集成 LangChain 的代码比第 3 章提供的示例代码更优雅。该框架还提供了许多额外的功能。

使用 `pip install langchain` 可以快速、简便地安装 LangChain。

> ![](Images/image00120.jpeg)　在我们撰写本书之时，LangChain 仍处于 beta 版本 0.0.2*XX*，并且几乎每天都有新版本发布。LangChain 的功能可能会有所变化，因此我们建议谨慎使用该框架。

图 5-1 显示了 LangChain 框架的关键模块。

![{%}](Images/image00157.jpeg)

**图 5-1：LangChain 框架的关键模块**

以下概述这些关键模块。

> `Models`（模型）

　　该模块是由 LangChain 提供的标准接口，你可以通过它与各种 LLM 进行交互。LangChain 支持集成 OpenAI、Hugging Face、Cohere、GPT4All 等提供商提供的不同类型的模型。

> `Prompts`（提示词）

　　提示词已成为 LLM 编程的新标准。该模块包含许多用于管理提示词的工具。

> `Indexes`（索引）

　　该模块让你能够将 LLM 与你的数据结合使用 1。

1截至 2023 年 12 月 2 日，LangChain 已将 `Indexes` 模块改名为 `Retrieval` 模块。——译者注

> `Chains`（链）

　　通过该模块，LangChain 提供了 `Chain` 接口。你可以使用该接口创建一个调用序列，将多个模型或提示词组合在一起。

> `Agents`（智能体）

　　该模块引入了 `Agent` 接口。所谓智能体，就是一个可以处理用户输入、做出决策并选择适当工具来完成任务的组件。它以迭代方式工作，采取一系列行动，直到解决问题。

> `Memory`（记忆）

　　该模块让你能够在链调用或智能体调用之间维持状态。默认情况下，链和智能体是无状态的。这意味着它们独立地处理每个传入的请求，就像 LLM 一样。

LangChain 是用于不同 LLM 的通用接口，你可以查阅其文档以了解更多信息。LangChain 的文档包含一份集成列表，其中涉及 OpenAI 和其他许多 LLM 提供商。大多数集成需要 API 密钥才能建立连接。对于 OpenAI 模型，你可以按照第 2 章介绍的方式，在环境变量 `OPENAI_API_KEY` 中设置 API 密钥。

### 5.1.1　动态提示词

要解释 LangChain 的工作原理，最简单的方法就是展示一个简单的脚本。在这个例子中，我们用 OpenAI 模型和 LangChain 来完成一个简单的文本补全任务：

```
from langchain.chat_models import ChatOpenAI
from langchain import PromptTemplate, LLMChain
template = """Question: {question}
Let's think step by step.
Answer: """
prompt = PromptTemplate(template=template,
input_variables=["question"])
llm = ChatOpenAI(model_name="gpt-4")
llm_chain = LLMChain(prompt=prompt, llm=llm)
question = """ What is the population of the capital of the country
where the Olympic Games were held in 2016? """
llm_chain.run(question)
```

输出如下：

```
Step 1: Identify the country where the Olympic Games were held in 2016.
Answer: The 2016 Olympic Games were held in Brazil.
Step 2: Identify the capital of Brazil.
Answer: The capital of Brazil is Brasília.
Step 3: Find the population of Brasília.
Answer: As of 2021, the estimated population of Brasília is around
3.1 million. So, the population of the capital of the country where
the Olympic Games were held in 2016 is around 3.1 million. Note that
this is an estimate and may vary slightly.'
```

`PromptTemplate` 负责构建模型的输入。也就是说，它能以可复制的方式生成提示词。它包含一个名为 `template` 的输入文本字符串，其中的值可以通过 `input_variables` 进行指定。在本例中，我们定义的提示词会自动将“Let's think step by step”部分添加到问题中。

本例使用的 LLM 是 gpt-4。目前，默认模型是 gpt-3.5-turbo。`ChatOpenAI` 函数将模型的名称赋给变量 `llm`。这个函数假定用户在环境变量 `OPENAI_API_KEY` 中设置了 API 密钥，就像前几章的示例所示的那样。

提示词和模型由 `LLMChain` 函数组合在一起，形成包含这两个元素的一条链。最后，我们需要调用 `run` 函数来请求补全输入问题。当运行 `run` 函数时，`LLMChain` 使用提供的输入键值（以及可用的记忆键值）格式化提示词模板，随后将经过格式化的字符串传递给 LLM，并返回 LLM 输出。我们可以看到，模型运用“逐步思考”的技巧自动回答问题。

正如你所见，对于复杂的应用程序来说，动态提示词是简单而又颇具价值的功能。

### 5.1.2　智能体及工具

智能体及工具是 LangChain 框架提供的关键功能：它们可以使应用程序变得非常强大，让 LLM 能够执行各种操作并与各种功能集成，从而解决复杂的问题。

这里所指的“工具”是围绕函数的特定抽象，使语言模型更容易与之交互。智能体可以使用工具与世界进行交互。具体来说，工具的接口有一个文本输入和一个文本输出。LangChain 中有许多预定义的工具，包括谷歌搜索、维基百科搜索、Python REPL、计算器、世界天气预报 API 等。要获取完整的工具列表，请查看 LangChain 文档中的工具页面。除了使用预定义的工具，你还可以构建自定义工具并将其加载到智能体中，这使得智能体非常灵活和强大。

正如第 4 章所述，通过运用“逐步思考”的技巧，你可以在一定程度上提高模型的推理能力。在提示词末尾添加“Let's think step by step”，相当于要求模型花更多时间来回答问题。

本节介绍一种适用于应用程序的智能体，它需要一系列中间步骤。该智能体安排执行这些步骤，并可以高效地使用各种工具响应用户的查询。从某种意义上说，因为“逐步思考”，所以智能体有更多的时间来规划行动，从而完成更复杂的任务。

智能体安排的步骤如下所述。

1. 智能体收到来自用户的输入。
2. 智能体决定要使用的工具（如果有的话）和要输入的文本。
3. 使用该输入文本调用相应的工具，并从工具中接收输出文本。
4. 将输出文本输入到智能体的上下文中。
5. 重复执行步骤 2 ～步骤 4，直到智能体决定不再需要使用工具。此时，它将直接回应用户。

你可能已经注意到，这似乎与我们在第 3 章中所做的事情很接近，比如构建可以回答问题并执行操作的 AI 助理。LangChain 智能体当然有这样的能力，但它更强大。

图 5-2 展示了 LangChain 智能体如何使用工具。

![{%}](Images/image00158.jpeg)

**图 5-2：LangChain 智能体如何使用工具**

就本节而言，我们希望模型能够回答以下问题：2016 年奥运会举办国首都的人口的平方根是多少？这个问题并没有特殊的含义，但它很好地展示了 LangChain 智能体及工具如何提高 LLM 的推理能力。

如果将问题原封不动地抛给 GPT-3.5 Turbo，那么我们会得到以下回答：

```
The capital of the country where the Olympic Games were held in 2016
is Rio de Janeiro, Brazil. The population of Rio de Janeiro is
approximately 6.32 million people as of 2021. Taking the square root
of this population, we get approximately 2,513.29. Therefore, the
square root of the population of the capital of the country where
the Olympic Games were held in 2016 is approximately 2,513.29.
```

这个回答至少有两处错误：巴西的首都是巴西利亚，而不是里约热内卢； 6 320 000 的平方根约等于 2513.96，而不是 2513.29。我们可以通过运用“逐步思考”或其他提示工程技巧来获得更好的结果，但由于模型在推理和数学运算方面存在困难，因此我们很难相信结果是准确的。使用 LangChain 可以给我们更好的准确性保证。

如以下代码所示，LangChain 智能体可以使用两个工具：维基百科搜索和计算器。在通过 `load_tools` 函数创建工具之后，我们使用 `initialize_agent` 函数创建智能体。智能体的推理功能需要用到一个 LLM，本例使用的是 gpt-3.5-turbo。参数 `ZERO_SHOT_REACT_DESCRIPTION` 定义了智能体如何在每一步中选择工具。通过将 `verbose` 的值设置为 `True`，我们可以查看智能体的推理过程，并理解它是如何做出最终决策的。

```
from langchain.chat_models import ChatOpenAI
from langchain.agents import load_tools, initialize_agent, AgentType
llm = ChatOpenAI(model_name="gpt-3.5-turbo", temperature=0)
tools = load_tools(["wikipedia", "llm-math"], llm=llm)
agent = initialize_agent(
    tools, llm, agent=AgentType.ZERO_SHOT_REACT_DESCRIPTION,
    verbose=True
)
question = """What is the square root of the population of the capital
of the Country where the Olympic Games were held in 2016?"""
agent.run(question)
```

> ![](Images/image00119.jpeg)　在使用维基百科搜索工具之前，需要安装相应的 Python 包 wikipedia。可以使用 `pip install wikipedia` 来安装这个包。

正如你所看到的，智能体决定查询维基百科以获取有关 2016 年奥运会的信息：

```
> Entering new chain...
I need to find the country where the Olympic Games were held in 2016
and then find the population of its capital city. Then I can take the
square root of that population.
Action: Wikipedia
Action Input: "2016 Summer Olympics"
Observation: Page: 2016 Summer Olympics
[...]
```

输出的下一行包含维基百科关于奥运会的摘录。接下来，智能体使用维基百科搜索工具又进行了两次额外的操作：

```
Thought:I need to search for the capital city of Brazil.
Action: Wikipedia
Action Input: "Capital of Brazil"
Observation: Page: Capitals of Brazil
Summary: The current capital of Brazil, since its construction in
1960, is Brasilia. [...]
Thought: I have found the capital city of Brazil, which is Brasilia.
Now I need to find the population of Brasilia.
Action: Wikipedia
Action Input: "Population of Brasilia"
Observation: Page: Brasilia
[...]
```

下一步，智能体使用计算器工具：

```
Thought: I have found the population of Brasilia, but I need to
calculate the square root of that population.
Action: Calculator
Action Input: Square root of the population of Brasilia (population:
found in previous observation)
Observation: Answer: 1587.051038876822
```

得出最终答案：

```
Thought: I now know the final answer
Final Answer: The square root of the population of the capital of the
country where the Olympic Games were held in 2016 is approximately 1587.
> Finished chain.
```

正如你所见，该智能体展示了较强的推理能力：在得出最终答案之前，它完成了 4 个步骤。LangChain 框架使开发人员能够仅用几行代码就实现这种推理能力。

> ![](Images/image00118.jpeg)　虽然智能体可用的 LLM 有多个，而 GPT-4 是其中最昂贵的，但我们发现，GPT-4 在复杂问题上的表现与众不同。据我们观察，当智能体使用较小的模型进行推理时，结果很快就会变得不一致。你甚至可能遇到错误，因为模型无法按预期格式回答。

### 5.1.3　记忆

在某些应用程序中，记住之前的交互是至关重要的，无论是短期记忆还是长期记忆。使用 LangChain，你可以轻松地为链和智能体添加状态以管理记忆。构建聊天机器人是这种能力最常见的用例。在 LangChain 中，你可以使用 `ConversationChain` 很快地完成这个过程，只需几行代码即可将语言模型转换为聊天工具。

以下代码使用 text-ada-001 模型创建一个聊天机器人。这是一个只能执行基本任务的小模型。然而，它是 GPT-3 系列中速度最快、成本最低的模型。该模型从未针对聊天任务做过微调，但我们可以看到，只需几行 LangChain 代码，即可使用这个简单的文本补全模型开始聊天：
