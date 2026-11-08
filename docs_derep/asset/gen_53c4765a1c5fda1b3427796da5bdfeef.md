# 让 AI 自己搭框架、查资料、写稿、改稿的 LangGraph 实战

想做出一个能从零到交付、并且会自己复盘改稿的写作 Agent，核心就是把“立意、查证、写作、审稿、再查证”拆成可循环的节点，用状态把中间产物串起来。下面这套实现展示了完整思路，从定义状态到跑通示例，流程闭环清晰，特别适合做内容生产自动化。

先定好数据结构，让每个环节知道自己能读什么、写什么。状态里同时保留题目、大纲、初稿、审稿意见、参考资料以及当前修改轮次和最大轮次上限，这样整个图可以在不同节点间稳定传递信息。

角色提示词分别对应编辑、作家、研究员三类人格。编辑负责根据题目出大纲并给每节一句话指引；作家在拿到题目、大纲和资料后撰写专业且可读的文章，也能按修改意见重写；审稿编辑对已写文章给出具体可执行的修改建议；研究员在立项阶段和修改阶段各生成三条面向搜索引擎的优化查询，用来补充外部信息。

节点层面一共六个功能单元：生成大纲、基于大纲检索资料、写作、反思给出修改意见、基于修改意见检索资料、以及决定是否继续循环的判断函数。写作完成后会自增修改计数，判断函数对比当前轮次和上限，超限就结束，否则进入反思。

流程的编排是：从大纲节点起步 → 立项检索 → 写作 → 条件分支。进入反思后会先做针对性检索，再回到写作，形成写-评-查-改的闭环。最后用 MemorySaver 编译图并可视化，演示时以“中国的茶文化”为题，最多改两轮，直接流式输出中间状态。

[PRE0]python

from langgraph.checkpoint.memory import MemorySaver

from langgraph.graph import StateGraph, END

from typing import TypedDict, Annotated, List

import operator

from langchain_core.messages import AnyMessage, SystemMessage, HumanMessage
[PRE1]python

class AgentState(TypedDict):

    title: str  #文章题目

    outline: str  #大纲

    draft: str  #草稿

    critique: str  #评价

    information: List[str]  #参考信息

    revision_number: int  #文章修改次数

    max_revisions: int  #文章最大修改次数
[PRE2]python

outline_prompt="""你是一名经验丰富的编辑会根据用户的文章题目,拟定一个详细大纲,

并给每节用一句话说明应该描写什么具体内容。

"""
[PRE3]python

writer_prompt="""你是一名优秀的作家会根据提供的题目和大纲写出优秀的文章。

也可以严格地按照修改意见对文章进行修改。

你写的文章内容专业有深度,而且读起来非常有趣。

可以参考下边提供的信息来进行文章的撰写。

----------------

{information}

"""
[PRE4]python

reflection_prompt="""你是一名优秀的编辑,可以对作家提交的文章提出专业的修改意见。

你的修改意见总是专业而且具体的。

"""
[PRE5]python

outline_search_prompt="""你是一名研究员,负责为撰写文章提供相关信息。

你根据下边提供的文章题目和文章大纲,利用搜索引擎来检索撰写文章需要的相关信息。

请生成 3 个不同的针对搜索引擎优化的查询。

"""
[PRE6]python

critique_search_prompt="""你是一名研究员,负责为修改文章提供相关信息。

你根据下边提供的文章和修改意见,利用搜索引擎来检索改写需要的相关信息。

请生成 3 个不同的针对搜索引擎优化的查询。

"""
[PRE7]python

from langchain_core.pydantic_v1 import BaseModel

class Queries(BaseModel):

    queries: List[str]
[PRE8]python

def outline_node(state: AgentState):

    content = "请为《{title}》生成文章大纲。"

    messages=[

        SystemMessage(content=outline_prompt),

        HumanMessage(content=content.format(title=state["title"]))

    ]

    response = model.invoke(messages)

    return {"outline": response.content}
[PRE9]python

def outline_search_node(state: AgentState):

    content = "文章题目：{title}\n\n 文章大纲：{outline}"

    queries = model.with_structured_output(Queries).invoke([

        SystemMessage(content=outline_search_prompt), HumanMessage(content=content.format(title=

state["title"],outline=state["outline"]))

    ])

    information = []

    for q in queries.queries:

        response = tavily.search(query=q, max_results=5)

        for r in response["results"]:

            information.append(r["content"])

    return {"information": information}
[PRE10]python

def write_node(state: AgentState):

    information = "\n\n".join(state["information"])

    content = """请写一篇文章：

题目为{title}

大纲为{outline}

"""

    messages = [

        SystemMessage(

            content=writer_prompt.format(information=information)

        ),

        HumanMessage(

            content=content.format(title=state["title"], outline=state["outline"])

        )

    ]

    response = model.invoke(messages)

    return {"draft": response.content, "revision_number":state["revision_number"]+1}
[PRE11]python

def reflection_node(state:AgentState):

    content = "请对下边的文章提出修改意见：{draft}"

    messages = [

        SystemMessage(content=reflection_prompt),

        HumanMessage(content=content.format(draft=state["draft"]))

    ]

    response = model.invoke(messages)

    return {"critique":response.content}
[PRE12]python

def critique_search_node(state:AgentState):

    content = """文章：{draft}

    修改意见：{critique}

    """

    queries = model.with_structured_output(Queries).invoke([

        SystemMessage(content=critique_search_prompt),

        HumanMessage(content=content.format(draft=state["draft"],critique=state["critique"]))

    ])

    information = state["information"] or []

    for q in queries.queries:

        response = tavily.search(query=q, max_results=5)

        for r in response["results"]:

            information.append(r["content"])

    return {"information": information}
[PRE13]python

def should_continue(state:AgentState):

    if state["revision_number"] > state["max_revisions"]:

        return END

    return "reflect"
[PRE14]python

builder = StateGraph(AgentState)

#增加节点

builder.add_node("outline_node", outline_node)

builder.add_node("write_node",write_node)

builder.add_node("reflection_node", reflection_node)

builder.add_node("outline_search_node", outline_search_node)

builder.add_node("critique_search_node",critique_search_node)

#将生成大纲的节点作为起始节点

builder.set_entry_point("outline_node")

#增加条件边

builder.add_conditional_edges(

    "write_node",

    should_continue,

    {END:END,"reflect":"reflection_node"}

)

#连接各个节点

builder.add_edge("outline_node","outline_search_node")

builder.add_edge("outline_search_node","write_node")

builder.add_edge("reflection_node","critique_search_node")

builder.add_edge("critique_search_node","write_node")
[PRE15]python

memory = MemorySaver()

graph = builder.compile(checkpointer=memory)

from IPython.display import Image

Image(graph.get_graph().draw_png())
[PRE16]python

config={"configurable":{"thread_id":"1"}}

events = graph.stream({

    "title": "中国的茶文化",

    "max_revisions":2,

    "revision_number":1

}, config)

for event in events:

    print(event)