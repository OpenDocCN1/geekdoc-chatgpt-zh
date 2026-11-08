"input": "请帮我梳理一下热门视频的描述信息"

})

# RAG 评测链路拆解笔记

最近在搭问答评测体系时，常会把检索准不准、回答是否跑偏、代码对不对、问题够不够清晰这四件事拧成一条流水线。下面这组示例就是在 LangChain 里落地的典型做法，顺带用 ChatGLM3-6B 的热门视频描述做了一次演示。

检索质量这块先从判分器说起，思路是把文档和用户提问喂给模型，让它按固定模板输出结构化结果。

[PRE0]

def create_retrieval_grader(self):

    grade_prompt = PromptTemplate(

        template="""... (省略部分) ... Here is the retrieved document: {document} ... Here is the user question: {input}""",

        input_variables=["document", "input"],

    )

    retriever_grader = grade_prompt | self.model | JsonOutputParser()

    return retriever_grader

落地调用时就很直观，先实例化判分器，再把待测文档和问题丢进去跑。示例里故意用了一个无意义的文档去匹配关于 ChatGLM3-6B 热门视频描述的提问，正是为了看判分器能否识别出不相关。

[PRE1]

# 创建一个检索的评估器

retrieval_grader = grader.create_retrieval_grader()

retrieval_grader_results = retrieval_grader.invoke({

    "document": "哈哈哈",

    "input": "请问关于 ChatGLM3-6B 热门视频的描述有哪些？"

})

再往下是幻觉检测。核心是把已知的事实集合和模型生成的答案放一起比对，模板里明确写出事实和答案两个位置，同样走 JSON 解析器得到可机器读的结论。

[PRE2]

def create_hallucination_grader(self):

    hallucination_prompt = PromptTemplate(

        template="""... Here are the facts: {documents} ... Here is the answer: {generation}""",

        input_variables=["generation", "documents"],

    )

    hallucination_grader = hallucination_prompt | self.model | JsonOutputParser()

    return hallucination_grader

实操片段里用的事实段落正是关于 ChatGLM3-6B 热门视频的描述：安装部署、微调、训练智能客服，以及文档、数据集、微调脚本的获取方式说明。而生成的内容只有“你好”，属于典型的跑题样例，用来验证幻觉判分器的敏感度。

[PRE3]

hallucination_grader = grader.create_hallucination_grader()

#这是出现幻觉的回答

hallucination_grader_results = hallucination_grader.invoke({

    "documents": "这是我查询到的热门视频的描述：ChatGLM3-6B 的安装部署、微调、训练智能客服。文档、数据集、微调脚本获取方式：麻烦一键三连，评论后，我会找到评论私发源码，谢谢大家。",

    "generation": "你好"

})

代码类任务的评估需要多一个维度：问题本身、检索到的资料、模型生成的代码三者同时进模板。

[PRE4]

def create_code_evaluator(self):

    eval_template = PromptTemplate(

        template="""... Here is the generated code: {generation} ... Here is the question: {input} ... Here are the relevant documents: {documents}""",

        input_variables=["generation", "input", "documents"],

    )

    code_evaluator = eval_template | self.model | JsonOutputParser()

    return code_evaluator

提问本身也需要打磨，于是有了问题重写器。提示词固定住“你是问句改写者…给出更优问句”的角色，只需要一个输入变量，经过模型后用字符串解析器拿出改写结果。

[PRE5]

def create_question_rewriter(self):

    re_write_prompt = PromptTemplate(

        template="""You are a question re-writer ... Formulate an improved question.""",

        input_variables=["input"],

    )

    question_rewriter = re_write_prompt | self.model | StrOutputParser()

    return question_rewriter

除了评测组件，顺带放一个最基础的链式翻译示例，展示 PromptTemplate、ChatOpenAI 与链式调用的组合方式。模型选用 gpt-4o，系统指令里带入源语言和目标语言占位符，人类消息直接传原文。

[PRE6]

import getpass

import os

from langchain_openai import ChatOpenAI

from langchain_core.prompts import ChatPromptTemplate

if not os.environ.get("OPENAI_API_KEY"):

    os.environ["OPENAI_API_KEY"] = getpass.getpass("Enter your OpenAI API key: ")

llm = ChatOpenAI(model="gpt-4o")

prompt = ChatPromptTemplate.from_messages(

    [

        ("system","You are a helpful assistant that translates {input_language} to {output_language}.",),

        ("human", "{input}"),

    ]

)

chain = prompt | llm | output | pr

chain.invoke(

    {

        "input_language": "English",

        "output_language": "Chinese",

        "input": "I love programming.",

    }

)

这段调用返回的 AIMessage 很典型，内容是“我热爱编程。”，同时带了 token 消耗、模型名、系统指纹等元数据，方便后续做成本和性能分析。

[PRE7]

AIMessage(content='我热爱编程。', additional_kwargs={'refusal': None}, response_metadata={'token_usage': {'completion_tokens': 6, 'prompt_tokens': 26, 'total_tokens': 32, 'prompt_tokens_details': {'cached_tokens': 0}, 'completion_tokens_details': {'reasoning_tokens': 0}}, 'model_name': 'gpt-4o-2024-08-06', 'system_fingerprint': 'fp_a20a4ee344', 'finish_reason': 'stop', 'logprobs': None}, id='run-fe5518d1-7987-49bb-bbea-0200cad33dd1-0', usage_metadata={'input_tokens': 26, 'output_tokens': 6, 'total_tokens': 32, 'input_token_details': {'cache_read': 0}, 'output_token_details': {'reasoning': 0}})

最后补充一个函数调用的输出样式示例，方便对照工具调用链的返回格式。

[PRE8]

  输出示例：function': {'arguments': '{"query":"什么是快乐星球？"}', 'name': 'web_search', 'type': 'function'}。