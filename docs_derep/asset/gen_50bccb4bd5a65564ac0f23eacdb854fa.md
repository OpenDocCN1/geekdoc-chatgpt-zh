raise ValueError("Badly formed question!")

        return field

[PRE0]python

output = chain("ice cream flavors")

[PRE1]python

output_parser.parse(output['text'])

[PRE2]python

[

    'Vanilla', 

    'Chocolate', 

    'Strawberry', 

    'Mint Chocolate Chip', 

    'Cookies and Cream']

[PRE3]

把用户输入的 "ice cream flavors" 填进提示模板的 query 占位符里，通过 LLM 包装器向模型发起请求。同时把解析器生成的格式说明注入模板的 format_instructions 占位符：

[PRE4]

接下来调用解析器的 parse 方法，把模型返回的内容转成 Pydantic JSON 结构：

[PRE5]

处理完后得到的数据完全遵循 Joke 模型的定义：

[PRE6]

######## 3.4.4 结构化输出解析

OutputParsers 是一套用于处理和规范化模型返回内容的工具集合。整体功能比较多，但落到工程实践里最核心的就是结构化输出解析器 StructuredOutputParser。它能把模型原本以纯文本返回的结果，映射成代码里可以直接操作的数据结构。关键做法是先给出期望的数据结构定义，把这个定义转成提示词中的输出指引，让模型按照该指引生成结构化结果。通俗说，就是不再接受一段随意文字，而是让模型按指定结构返回抽象数据。

在实际使用时，第一步是明确想要的输出格式。解析器会依据这个格式自动生成提示内容，从而引导模型产出符合预期的结果，比如一次返回多个字段。需要注意的是，虽然 Pydantic/JSON 解析器功能更强，但在早期试验阶段我们只用了包含纯文本字段的简易结构。

第一步是先从 LangChain 引入所需的基础类与函数：

[PRE7]

随后描述期望的响应结构：

[PRE8]

再调用方法生成 format_instructions，里面包含了格式化的具体指示，把它填入提示模板对应位置：

[PRE9]

[PRE10]

把 "ice cream flavors" 这个用户输入赋给模板里的 query 变量，借助 LLM 包装器完成与模型平台的对话；同时把解析器提供的格式指令赋给 format_instructions 变量：

[PRE11]

之后执行解析器的 parse 方法，把原始输出转换成 Pydantic JSON 形式：

[PRE12]

最终得到的结果严格符合 Joke 模型的字段定义：

[PRE13]

**OutputParsers** 属于一类专门用来清洗和格式化模型输出的工具。功能模块很多，但在真实开发场景里，最常用的是结构化输出解析器。

###### LangChain 入门指南

打造高复用、高扩展性的 LLM 应用体系

[PRE14]python

from langchain.document_loaders import WebBaseLoader

loader = WebBaseLoader("http://developers.minil.cn/wiki/lawa.html")

data = loader.load()

[PRE15]python

from langchain.embeddings.openai import OpenAIEmbeddings

embedding = OpenAIEmbeddings(openai_api_key="填入你的 openai 密钥")

[PRE16]python