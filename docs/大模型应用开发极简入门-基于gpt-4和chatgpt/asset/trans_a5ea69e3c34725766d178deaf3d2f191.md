
```
from langchain import OpenAI, ConversationChain
chatbot_llm = OpenAI(model_name='text-ada-001')
chatbot = ConversationChain(llm=chatbot_llm , verbose=True)
chatbot.predict(input='Hello')
```

在以上代码的最后一行，我们执行了 `predict(input='Hello')`。这要求聊天机器人回复我们的 `'Hello'` 消息。模型的回答如下所示：

```
> Entering new ConversationChain chain...
Prompt after formatting:
The following is a friendly conversation between a human and an
AI. The AI is talkative and provides lots of specific details from
its context. If the AI does not know the answer to a question, it
truthfully says it does not know.
Current conversation:
Human: Hello
AI:
> Finished chain.
' Hello! How can I help you?'
```

由于将 `ConversationChain` 中的 `verbose` 设置为 `True`，因此我们可以查看 `LangChain` 使用的完整提示词。当我们执行 `predict(input='Hello')` 时，`text-ada-001` 模型收到的不仅仅是 `'Hello'` 消息，而是完整的提示词。该提示词位于标签 `> Entering new ConversationChain chain...` 和 `> Finished chain` 之间。

如果继续对话，我们会发现该函数在提示词中保留了对话的历史记录。如果我们接着问模型是不是 AI，那么这个问题也将被包含在提示词中：

```
> Entering new ConversationChain chain...
Prompt after formatting:
The following [...] does not know.
Current conversation:
Human: Hello
AI:  Hello! How can I help you?
Human: Can I ask you a question? Are you an AI?
AI:
> Finished chain.
'\n\nYes, I am an AI.'
```

`ConversationChain` 对象使用提示工程技巧和记忆技巧，将进行文本补全的 `LLM` 转换为聊天工具。

> ![](Images/image00120.jpeg)　尽管 `LangChain` 让所有语言模型拥有了聊天能力，但这个解决方案并不像 `GPT-3.5 Turbo` 和 `GPT-4` 这样强大，后两者已经专门针对聊天任务进行了优化。此外，`OpenAI` 已经宣布弃用 `text-ada-001` 模型。

### 5.1.4　嵌入

将语言模型与你自己的文本数据相结合，这样做有助于将应用程序所用的模型知识个性化。原理与第 3 章讨论的相同：首先检索信息，即获取用户的查询并返回最相关的文档；然后将这些文档发送到模型的输入上下文中，以便它响应查询。本节展示使用 `LangChain` 和嵌入技术实现这一点是多么简单。

`document_loaders` 是 `LangChain` 中的一个重要模块。通过这个模块，你可以快速地将文本数据从不同的来源加载到应用程序中。比如，应用程序可以加载 CSV 文件、电子邮件、PowerPoint 文档、Evernote 笔记、Facebook 聊天记录、HTML 页面、PDF 文件和许多其他格式。要查看完整的加载器列表，请查阅 `LangChain` 文档。每个加载器设置起来都非常简单。本示例复用了第 3 章中的《塞尔达传说：旷野之息》PDF 文件。

如果 PDF 文件位于当前工作目录下，则以下代码将加载文件内容并按页进行划分。

```
from langchain.document_loaders import PyPDFLoader
loader = PyPDFLoader("ExplorersGuide.pdf")
pages = loader.load_and_split()
```

> ![](Images/image00119.jpeg)　在使用 PDF 加载器之前，需要安装 `pypdf` 包。这可以通过 `pip install pypdf` 来完成。

进行信息检索时，需要嵌入每个加载的页面。正如我们在第 2 章中讨论的那样，在信息检索中，嵌入是用于将非数值概念（如单词、标记和句子）转换为数值向量的一种技术。这些嵌入使得模型能够高效地处理这些概念之间的关系。借助 `OpenAI` 的嵌入端点，开发人员可以获取输入文本的数值向量表示。此外，`LangChain` 提供了一个包装器来调用这些嵌入，如下所示。

```
from langchain.embeddings import OpenAIEmbeddings
embeddings = OpenAIEmbeddings()
```

> ![](Images/image00119.jpeg)　要使用 `OpenAIEmbeddings`，请先使用 `pip install tiktoken` 安装 `tiktoken` 包。

索引保存页面嵌入并使搜索变得容易。`LangChain` 以向量数据库为中心。有许多向量数据库可供选择，详见 `LangChain` 文档。以下代码片段使用 `Faiss` 向量数据库，这是一个主要由 `Facebook AI` 团队开发的相似性搜索库。

```
from langchain.vectorstores import FAISS
db = FAISS.from_documents(pages, embeddings)
```

> ![](Images/image00119.jpeg)　在使用 `Faiss` 向量数据库之前，需要使用 `pip install faiss-cpu` 命令安装 `faiss-cpu` 包。

图 5-3 展示了 PDF 文件的内容如何被转换为嵌入向量并存储在 `Faiss` 向量数据库中。

![{%}](Images/image00159.jpeg)

**图 5-3：创建并保存来自 PDF 文件的嵌入**

现在很容易搜索相似内容：

```
q = "What is Link's traditional outfit color?"
db.similarity_search(q)[0]
```

我们得到以下内容：

```
Document(page_content='While Link’s traditional green
              tunic is certainly an iconic look, his
              wardrobe has expanded [...] Dress for Success',
          metadata={'source': 'ExplorersGuide.pdf', 'page': 35})
```

这个问题的答案是，Link 的服装颜色是绿色。我们可以看到，答案就在选定的内容中。输出显示，答案在 `ExplorersGuide.pdf` 的第 35 页。请记住，`Python` 从 0 开始计数。因此，如果查看原始 PDF 文件，你会发现答案在第 36 页，而非第 35 页。

图 5-4 显示了信息检索过程如何使用查询的嵌入和向量数据库来识别与查询最相似的页面。

![{%}](Images/image00160.jpeg)

**图 5-4：通过信息检索过程寻找与查询最相似的页面**

你可能希望将嵌入整合到聊天机器人中，以便在回答问题时使用它检索到的信息。再次强调，使用 `LangChain`，只需几行代码即可轻松实现。我们使用 `RetrievalQA`，它接受 `LLM` 和向量数据库作为输入。然后，我们像往常一样向所获得的对象提问：

```
from langchain.chains import RetrievalQA
from langchain import OpenAI
llm = OpenAI()
chain = RetrievalQA.from_llm(llm=llm, retriever=db.as_retriever())
q = "What is Link's traditional outfit color?"
chain(q, return_only_outputs=True)
```

这一次，我们得到以下答案：

```
{'result': " Link's traditional outfit color is green."}
```

图 5-5 显示了 `RetrievalQA` 如何使用信息检索来回答用户的问题。正如我们从图中看到的，“提供上下文”将信息检索系统找到的页面和用户最初的查询进行分组。然后，上下文被发送给 `LLM`。`LLM` 可以利用上下文中的附加信息正确回答用户的问题。

![{%}](Images/image00161.jpeg)

**图 5-5：为了回答用户的问题，我们把检索到的信息添加到 LLM 的上下文中**

你可能会问：为什么在将信息添加到 `LLM` 的上下文中之前需要进行信息检索？事实上，目前已有的语言模型无法处理包含数百页的大型文件。因此，如果输入文件过大，那么我们会对其进行预过滤。这是信息检索过程的任务。在不久的将来，随着输入上下文的不断增加，可能就不再需要使用信息检索技术了。

## 5.2　GPT-4 插件

尽管包括 `GPT-4` 在内的 `LLM` 在各种任务上都表现出色，但它们仍然存在固有的局限性。比如，这些模型只能从训练数据中学习，这些数据往往过时或不适用于特定的应用。此外，它们的能力仅限于文本生成。我们还发现，`LLM` 不适用于某些任务，比如复杂的计算任务。

本节关注 `GPT-4` 的一个开创性特点：插件。在 AI 的发展过程中，插件已经成为一种新型的革命性工具，它重新定义了我们与 `LLM` 的互动方式。插件的目标是为 `LLM` 提供更广泛的功能，使 `LLM` 能够访问实时信息，进行复杂的数学运算，并利用第三方服务。

我们在第 1 章中看到，模型无法执行复杂的计算，如计算 3695 × 123 548。如图 5-6 所示，我们激活了计算器插件。可以看到，当需要进行计算时，模型会自动调用计算器，从而得到正确的结果。

![{%}](Images/image00162.jpeg)

**图 5-6：GPT-4 使用计算器插件**

通过迭代部署方法，`OpenAI` 逐步向 `GPT-4` 添加插件，这使得 `OpenAI` 能够考虑插件的实际用途及可能引入的安全问题和定制化挑战。虽然自 2023 年 5 月以来，插件功能已对所有付费用户开放，但在我们撰写本书之时，`OpenAI` 尚未面向所有开发人员提供创建新插件的功能。

`OpenAI` 的目标是创建一个生态系统，插件可以帮助塑造 AI 与人类互动的未来。如今，我们很难想象一家企业没有自己的网站会是什么样，但也许很快，每家企业都需要有自己的插件。事实上，Expedia、FiscalNote、Instacart、KAYAK、Klarna、Milo、OpenTable、Shopify 和 Zapier 等公司已经率先推出了几款插件。

除了主要功能，插件还在几个方面扩展了 `GPT-4` 的功能。在某种意义上，插件与 5.1.2 节讨论的智能体及工具存在一些相似之处。比如，插件可以使 `LLM` 检索体育比分和股票价格等实时信息，从企业文档等知识库中提取数据，并根据用户的需求执行任务，如预订航班或订餐。两者都旨在帮助 AI 获取最新信息并进行计算。然而，`GPT-4` 中的插件更专注于第三方服务，而不是 `LangChain` 工具。

本节从概念上介绍通过探索 `OpenAI` 网站所提供的示例来创建插件。我们将以待办事项列表定义插件为例进行说明。由于在我们撰写本书之时，插件仍处于有限的测试阶段，因此我们建议你访问 `OpenAI` 网站以了解最新信息。同时请注意，在测试阶段，用户必须在 ChatGPT 的用户界面中手动启用插件，并且作为开发人员，你最多只能与 100 个用户共享你的插件。

### 5.2.1　概述

在开发插件前，你必须创建一个 `API` 并将其与两个描述性文件关联起来：一个插件清单和一个 `OpenAPI` 规范。当你开始与 `GPT-4` 进行交互时，`OpenAI` 会向 `GPT-4` 发送一条隐藏消息，以检查你的插件是否已安装。这条消息会简要介绍你的插件，包括其描述信息、端点和示例。

这样一来，模型就成了智能的 `API` 调用者。当用户询问关于插件的问题时，模型可以调用你的插件 `API`。是否调用插件是基于 `OpenAPI` 规范和关于应该使用 `API` 的情况的自然语言描述所做出的决策。一旦模型决定调用你的插件，它就会将 `API` 的结果合并到上下文中，以向用户提供响应。因此，插件的 `API` 响应必须返回原始数据而不是自然语言响应。这使得 `GPT-4` 可以根据返回的数据生成自己的自然语言响应。

如果用户问模型“我在纽约可以住哪里”，那么模型可以使用酒店预订插件，然后将插件的 `API` 响应与其文本生成能力结合起来，提供既含有丰富信息又对用户友好的回答。

### 5.2.2　API

以下是 `OpenAI` 在 `GitHub` 上提供的待办事项列表定义插件的简化版本：

```
import json
import quart
import quart_cors
from quart import request
app = quart_cors.cors(
    quart.Quart(__name__), allow_origin="https://chat.openai.com"
)
# 跟踪待办事项。如果 Python 会话重新启动，则不会持久保存
_TODOS = {}
@app.post("/todos/<string:username>")
async def add_todo(username):
    request = await quart.request.get_json(force=True)
    if username not in _TODOS:
        _TODOS[username] = []
    _TODOS[username].append(request["todo"])
    return quart.Response(response="OK", status=200)
@app.get("/todos/<string:username>")
async def get_todos(username):
    return quart.Response(
        response=json.dumps(_TODOS.get(username, [])), status=200
    )
@app.get("/.well-known/ai-plugin.json")
async def plugin_manifest():
    host = request.headers["Host"]
    with open("./.well-known/ai-plugin.json") as f:
        text = f.read()
        return quart.Response(text, mimetype="text/json")
@app.get("/openapi.yaml")
async def openapi_spec():
    host = request.headers["Host"]
    with open("openapi.yaml") as f:
        text = f.read()
        return quart.Response(text, mimetype="text/yaml")
def main():
    app.run(debug=True, host="0.0.0.0", port=5003)
if __name__ == "__main__":
    main()
```

这段 `Python` 代码是一个简单的插件示例，用于管理待办事项列表。首先，变量 `app` 被初始化为 `quart_cors.cors()`。这行代码创建了一个新的 `Quart` 应用程序，并配置它允许来自 [https://chat.openai.com](https://chat.openai.com) 的**跨源资源共享**（cross-origin resource sharing，CORS）。`Quart` 是一个 `Python` Web 微框架，`Quart-CORS` 则是一个扩展，可以控制 CORS。这个设置允许插件与指定 URL 上托管的 `ChatGPT` 应用程序进行交互。

然后，代码定义了几个 HTTP 路由，分别对应待办事项列表定义插件的不同功能：`add_todo` 函数与 `POST` 请求相关联，`get_todos` 函数与 `GET` 请求相关联。

接着，代码定义了两个额外的端点：`plugin_manifest` 和 `openapi_spec`。这两个端点分别用于提供插件清单文件和 `OpenAPI` 规范，这对于 `GPT-4` 和插件之间的交互至关重要。这些文件包含有关插件及其 `API` 的详细信息，`GPT-4` 使用这些信息来了解何时及如何使用插件。

### 5.2.3　插件清单

每个插件都需要在 `API` 域上有一个 `ai-plugin.json` 文件。举例来说，如果你的公司在 `iTuring.cn` 上提供服务，那么必须将此文件放在 `iTuring.cn/.well-known/` 下。在安装插件时，`OpenAI` 将按照路径 `/.well-known/ai-plugin.json` 查找此文件。如果没有该文件，则无法安装插件。

以下是 `ai-plugin.json` 文件的极简定义：
