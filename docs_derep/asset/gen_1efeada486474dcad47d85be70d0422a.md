[PRE0]
调用完成后，模板消息的打印效果如下所示。
[PRE1]
### 4.5.3 模型初始化与创建
模型先行，后续的调用才得以进行。LangChain 统一封装了单轮与多轮对话场景下的模型创建能力。通用模型的示例可参考代码清单4-25。
#### 代码清单4-25 创建通用模型
[PRE2]
上述示例通过 OpenAI 类实例化了一个模型，实际运行却会抛出异常。这并非代码本身存在缺陷，而是 DeepSeek 服务并不兼容传统的单轮字符串输入方式。

以 OpenAI 为例，接口路径就能透露模型类型：对话式接口一般为 /model/chat/completion，而单轮文本输入接口则为 /model/completion。通过接口名称大致可以判断服务形态。

当前 GPT-4、DeepSeek、Claude、Gemini 等主流模型大多转向对话式交互，单轮字符串输入的支持正被逐步淘汰。于是接口地址也趋于统一，不再通过路径做功能区分，例如 DeepSeek 的统一入口就是 https://api.deepseek.com/v1。

基于以上变化，适合对话类 AI 和持续交互场景的自然是支持多轮会话的模型。它们以消息列表为输入，能保留上下文，并依赖明确的角色标注如用户、助手。建议放弃最初 OpenAI 提供的单轮字符串输入接口，统一改用对话模型传递 messages 的调用方式。下面改用聊天模型并演示调用，具体见代码清单4-26。
#### 代码清单4-26 创建聊天类模型后调用
[PRE3]
调用结束之后，invoke 的返回结果展示如下。
[PRE4]
### 4.5.4 大模型链的构建
4.5.3 节演示了直接通过 invoke 调用模型的简易做法。实际工程中更推荐采用系统化方案，即大模型链 LLMChain。

链是 LangChain 模块化设计的基石之一。它把多个组件串联成一条连贯的执行流程，让复杂应用的实现更简洁，同时提升可维护性，便于调试与优化。

链式调用遵循逻辑顺序，覆盖大模型交互、工具调用、数据预处理等环节，既能梳理清晰的调用路径，也能支撑更复杂的业务场景。

借助链，可以把输入、提示模板格式化、大模型处理等步骤有机整合。例如最简单的链会接收用户输入，用 PromptTemplate 进行格式化，再把结果送入模型。进阶时可组合多条链或与其他组件配合，实现更复杂的逻辑。

LangChain 涵盖大模型操作、会话管理、文档向量化等，衍生出 LLMChain、ConversationChain、StuffDocumentsChain 等多种链。下面先介绍如何创建大模型操作链 LLMChain，示例见代码清单4-27。
[PRE5]python
from langchain import LLMChain

from langchain_openai import ChatOpenAI

from langchain_core.prompts.chat import ChatPromptTemplate

from langchain.prompts import HumanMessagePromptTemplate, SystemMessagePromptTemplate

system_message_prompt = SystemMessagePromptTemplate.from_template(template=
    "# 角色 \n你是一个智能助手，名字叫聪聪")

human_message_prompt = HumanMessagePromptTemplate.from_template(template=
    "# 用户问题 \n{input}")

chat_prompt_template = ChatPromptTemplate.from_messages(messages=[
    system_message_prompt, human_message_prompt
])

# 创建名为 model 的模型
model = ChatOpenAI(
    model="deepseek-chat",
    temperature=0.5,
    timeout=None,
    max_retries=3,
    api_key="",  # 配置你的 api_key
    base_url="https://api.deepseek.com",
    streaming=False,
)

# 创建大模型链
chain = LLMChain(
    llm=model,
    prompt=chat_prompt_template,
)

# 调用链
print(chain.invoke(input="你的名字叫什么？"))
[PRE6]python
{'input': '你的名字叫什么？', 'text': '你好，我的名字叫聪聪，很高兴认识你！'}
[PRE7]python
# 使用 LCEL 语法创建大模型链
chain = chat_prompt_template | model

# 调用链
print(chain.invoke(input="你的名字叫什么？"))
[PRE8]python
content='你好！我的名字叫聪聪，很高兴认识你！有什么我可以帮你的吗？' response_metadata={'token_usage': {'completion_tokens': 18, 'prompt_tokens': 25, 'total_tokens': 43, 'hed_tokens': 0}, 'prompt_cache_hit_tokens': 0, 'prompt_cache_miss_tokens': 25}, 'model_name': 'deepseek-chat', 'system_fingerprint': 'fp_3a5770e1b4_prod0225', 'finish_reason': 'stop', 'logprobs': None} id='run-1ec3a114-fc13-4e08-8c6b-b05f35b3a6cd-0'
[PRE9]python
from langchain import LLMChain
from typing import Any, Dict, List
from langchain_openai import ChatOpenAI
from langchain_core.outputs import LLMResult
from langchain_core.messages import BaseMessage
from langchain_core.callbacks import BaseCallbackHandler
from langchain_core.prompts.chat import ChatPromptTemplate
from langchain.prompts import HumanMessagePromptTemplate, SystemMessagePromptTemplate

class LoggingHandler(BaseCallbackHandler):
    def on_chat_model_start(
        self, serialized: Dict[str, Any], messages: List[List[BaseMessage]],
        **kwargs
    ) -> None:
        print("回调执行：on_chat_model_start")

    def on_llm_end(self, response: LLMResult, **kwargs) -> None:
        print(f"回调执行：on_llm_end, response: {response}")

    def on_chain_start(
        self, serialized: Dict[str, Any], inputs: Dict[str, Any], **kwargs
    ) -> None:
        print("回调执行：on_chain_start")

    def on_chain_end(self, outputs: Dict[str, Any], **kwargs) -> None:
        print(f"回调执行：on_chain_end, outputs: {outputs}")

system_message_prompt = SystemMessagePromptTemplate.from_template(template=
    "# 角色\n你是一个智能助手，名字叫聪聪")
human_message_prompt = HumanMessagePromptTemplate.from_template(template=
    "# 用户问题\n{input}")
chat_prompt_template = ChatPromptTemplate.from_messages(messages=[
    system_message_prompt, human_message_prompt
])

# 创建名为 model 的模型
model = ChatOpenAI(
    model="deepseek-chat",
    temperature=0.5,
    timeout=None,
    max_retries=3,
    api_key="",  # 配置你的 api_key
    base_url="https://api.deepseek.com",
    streaming=False,
)

# 创建大模型链
chain = LLMChain(
    llm=model,
    prompt=chat_prompt_template,
)

# 调用链
callbacks = [LoggingHandler()]
print(chain.invoke(input="你的名字叫什么？", config={"callbacks": callbacks}))
[PRE10]python
回调执行：on_chain_start
回调执行：on_chat_model_start
回调执行：on_llm_end, response: generations=[[ChatGeneration(text=' 你好！我的名字叫聪聪，很高兴认识你！有什么我可以帮助你的吗？ ', generation_info={'finish_reason': 'stop', 'logprobs': None}, response_metadata={'token_usage': {'completion_tokens': 18, 'prompt_tokens': 25, 'total_tokens': 43, 'prompt_tokens_details': {'cached_tokens': 0}, 'prompt_cache_hit_tokens': 0, 'prompt_cache_miss_tokens': 25}, 'model_name': 'deepseek-chat', 'system_fingerprint': 'fp_3a5770e1b4_prod0225', 'finish_reason': 'stop', 'logprobs': None}, id='run-dab49c4b-68c4-41e4-87b0-4b2a33aefcf4-0')]] llm_output={'token_usage': {'completion_tokens': 18, 'prompt_tokens': 25, 'total_tokens': 43, 'prompt_tokens_details': {'cached_tokens': 0}, 'prompt_cache_hit_tokens': 0, 'prompt_cache_miss_tokens': 25}, 'model_name': 'deepseek-chat', 'system_fingerprint': 'fp_3a5770e1b4_prod0225'} run=None

回调执行：on_chain_end, outputs: {'text': ' 你好！我的名字叫聪聪，很高兴认识你！有什么我可以帮助你的吗？ '}