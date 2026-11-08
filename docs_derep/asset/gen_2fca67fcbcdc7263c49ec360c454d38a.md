[PRE0]
处理结束后，每条视频的信息都会按统一的字段顺序整理成结构化记录，方便后续封装成 LangChain 的 Document 对象做向量嵌入。`get_bilibili.py` 完整执行时的输出示例见图8.9。
[PRE1]
#### 8.1.6 数据转换器与基础 RAG 搭建
本节要介绍的是项目整体架构中数据层里的另一个关键模块，如图8.10所示。核心工作是搭建一个数据转换器，把从外部获取的原始信息，也就是 Bilibili API 返回的数据，转换成大模型可理解、可调用的向量形式，为 RAG 系统提供支撑。
[PRE2]
图8.10 BiliAgent 项目数据转换模块示意
在完整工程 `BiliAgent-main/bili_server/document_loader.py` 中，我们实现的 `DocumentLoader` 类实现如下。
[PRE3]
python

print("开始进行向量数据库存储")

vector_store = await self.create_vector_store(docs)

print("成功完成向量数据库的存储")

print("-------------------")

print("开始进行文本检索")

retriever = vector_store.as_retriever(search_kwargs={"k": 10})

retriever_result = retriever.invoke(str(keywords))

print(f"检索到的数据为：{retriever_result}")

return retriever_result

if __name__ == '__main__':

    import asyncio

    # 创建 DocumentLoader 实例并调用 get_docs

    async def main():

        loader = DocumentLoader()

        await loader.get_retriever(keywords=["ChatGLM3-6b"], page=1)

    asyncio.run(main())
[PRE4]
generate_template = """

You are an AI personal assistant named FuFan. Users will pose questions related to BiliBili website data, which are presented in the parts enclosed by <context></context> tags.

Use this information to formulate your answers.

When a user's question requires fetching data using the BiliBili API, you may proceed accordingly.

If you cannot find an answer, please respond honestly that you do not know. Do not attempt to fabricate an answer.

If the question is unrelated to the context, politely respond that you can only answer questions related to the context provided.

For questions involving data analysis, please write the code in Python and provide a detailed analysis of the results to offer as comprehensive an answer as possible.

<context>

{context}

</context>

<question>

{input}

</question>

"""

generate_prompt = PromptTemplate(template=generate_template, input_variables=["context", "input"])
[PRE5]
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(api_key=os.getenv('OPENAI_API_KEY'),

                 model=os.getenv('model'),

                 )
[PRE6]
from langchain_core.output_parsers import StrOutputParser
[PRE7]
generate_chain = generate_prompt | llm | StrOutputParser()
[PRE8]
# 创建一个生成链

generate_chain = create_generate_chain(llm)

final_answer = generate_chain.invoke({

    "context": "这是我查询到的热门视频的描述：ChatGLM3-6B 的安装部署、微调、训练智能客服。文档、数据集、微调脚本获取方式：麻烦一键三连，评论后，我会找到评论私发源码，谢谢大家。",

    "input": "请帮我梳理一下热门视频的描述信息"