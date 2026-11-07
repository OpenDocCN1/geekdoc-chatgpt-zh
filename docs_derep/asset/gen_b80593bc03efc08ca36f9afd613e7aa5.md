# LangChain LLMChain 速上手指南

想把大模型变成可复用的链路，LLMChain 是最直接的切入口。下面用两个最小例子说明从装包、建模型、写模板到调用的完整流程，以及 run、__call__、return_only_outputs 的不同用法。

先准备环境并实例化一个基础 LLM，温度调高一点让输出更有变化：
[PRE0]python

pip -q install openai langchain

from langchain.llms import OpenAI

from langchain.prompts import PromptTemplate

llm = OpenAI(temperature=0.9)

定义一个带占位符的 PromptTemplate，变量名要和后续输入对应：
[PRE1]python

prompt = PromptTemplate(

    input_variables=["product"],

    template="What is a good name for a company that makes {product}?",

)

把模型和模板组装成链：
[PRE2]python

from langchain.chains import LLMChain

chain = LLMChain(llm=llm, prompt=prompt)

最简调用就是 run，传入产品名即可得到名称建议：
[PRE3]python

chain.run("colorful socks")

如果你更习惯像函数一样传参，链其实实现了 __call__，参数结构如下：
[PRE4]python

def __call__(

    self,

    inputs: Union[Dict[str, Any], Any],

    return_only_outputs: bool = False,

    callbacks: Callbacks = None,

    *,

    tags: Optional[List[str]] = None,

    include_run_info: bool = False,

) -> Dict[str, Any]:

开启 return_only_outputs 只返回输出部分：
[PRE5]python

result = chain(inputs, return_only_outputs=True)

下面换成对话模型演示更完整的输入输出形态。温度设为 0 让结果稳定：
[PRE6]python

chat = ChatOpenAI(temperature=0)

prompt_template = "Tell me a (adjective) joke"

llm_chain = LLMChain(llm=chat,

    prompt=PromptTemplate.from_template(prompt_template))

llm_chain(inputs={"adjective": "corny"})

默认返回包含输入和输出的字典：
[PRE7]json

{'adjective': 'corny',

  'text': 'Why did the tomato turn red? Because it saw the salad dressing!'}

只想要纯结果时，加上 return_only_outputs：
[PRE8]python

llm_chain("corny", return_only_outputs=True)

这时只剩下 text 字段：
[PRE9]json

{'text': 'Why did the tomato turn red? Because it saw the salad dressing!'}

当链只有一个输出键时，可以直接用 run，省去字典封装：
[PRE10]python

######### llm_chain only has one output key, so we can use run

llm_chain.output_keys['text']

同上效果，用 run 传字典：
[PRE11]python

llm_chain.run({"adjective": "corny"})

掌握这几步，就能快速把提示词和模型封装成可复用的链，并在 run 与 __call__ 之间按需切换。