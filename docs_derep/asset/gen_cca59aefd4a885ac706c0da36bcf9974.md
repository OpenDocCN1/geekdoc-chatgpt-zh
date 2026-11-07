LangChain 提示词模板组合实战笔记

先把项目里常用的模板类一次性导进来，之后就能快速拼装对话式的 Prompt。

[PRE0]python

from langchain.prompts import (

    ChatPromptTemplate,

    PromptTemplate,

    SystemMessagePromptTemplate,

    AIMessagePromptTemplate,

    HumanMessagePromptTemplate,

)

接下来演示如何用系统消息设定角色，再用人类消息传入可变内容。

[PRE1]python

from langchain import PromptTemplate

template = """

You are an expert data scientist

with an expertise in building deep learning models.

"""

system_message_prompt = SystemMessagePromptTemplate.from_template(template)

human_template="Explain the concept of {concept} in a couple of lines"

human_message_prompt = HumanMessagePromptTemplate.from_template(human_template)

把前面创建好的系统模板和人类模板一起交给 ChatPromptTemplate.from_messages 即可完成组装，示例代码如下：

chat_prompt=ChatPromptTemplate.from_messages(

    [system_message_prompt,

    human_message_prompt])

执行后可以看到一个包含 input_variables 的 ChatPromptTemplate 对象，messages 列表里依次是 SystemMessagePromptTemplate 和 HumanMessagePromptTemplate，前者保存了角色设定文本且没有输入变量，后者携带了 concept 变量。

更简洁的写法也可以直接用列表传给 from_messages：

[PRE2]python

messages = [

    SystemMessagePromptTemplate.from_template(system_template),

    HumanMessagePromptTemplate.from_template("question"),

]

CHAT_PROMPT = ChatPromptTemplate.from_messages(messages)

少样本场景下，先定义单个示例的格式：

[PRE3]python

example_prompt=PromptTemplate(input_variables=["input","output"],

template="词语： {input}\n反义词：{output}\n")

基于示例模板再构建 FewShotPromptTemplate，指定前缀、后缀、分隔符和输入变量：

[PRE4]python

few_shot_prompt = FewShotPromptTemplate(

    examples=examples,

    example_prompt=example_prompt,

    example_separator="\n",

    prefix="来玩个反义词接龙游戏，我说词语，你说它的反义词\n",

    suffix="词语:{input}\n反义词：",

    input_variables=["input"],

)