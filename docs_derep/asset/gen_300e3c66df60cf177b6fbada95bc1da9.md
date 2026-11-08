# RAG 工作流图谱搭建全流程

想把检索增强生成跑通，关键就在于把检索、评级、改写、生成这些环节拆成可控的节点，用状态图串联起来。下面这套结构就是一套可复用的实现思路，先看核心节点的代码形态，再理解它是怎么串起来的。

第一步是把用户问题喂进检索器拿资料。
[PRE0]python

async def retrieve(self, state):

    question = state["input"]

    documents = await self.retriever.get_retriever(keywords=[question], page=1)

    return {"documents": documents, "input": question}
检索完成后，需要对拿到的文档做相关性打分，过滤掉噪声。
[PRE1]python

def grade_documents(self, state):

    question = state["input"]

    documents = state["documents"]

    ...

    for d in documents:

        score = self.retrieval_grader.invoke({"input": question, "document": d.page_content})
如果相关文档为空，就让问题改写器重写一次查询，提升召回机会。
[PRE2]python

def transform_query(self, state):

    question = state["input"]

    better_question = self.question_rewriter.invoke({"input": question})

    return {"documents": documents, "input": better_question}
文档合格后进入生成环节，结合上下文调用生成链输出答案。
[PRE3]python

def generate(self, state):

    question = state["input"]

    documents = state["documents"]

    generation = self.generate_chain.invoke({"context": documents, "input": question})

    return {"documents": documents, "input": question, "generation": generation}
在检索和生成之间加一个决策点，判断是否需要改写。
[PRE4]python

def decide_to_generate(self, state):

    filtered_documents = state["documents"]

    if not filtered_documents:

        return "transform_query"

    else:

        return "generate"
生成结果出来后，还要做一次质量校验，先验幻觉，再验代码有用性。
[PRE5]python

def grade_generation_v_documents_and_question(self, state):

    score = self.hallucination_grader.invoke({...})

    if score["score"] == "yes":

        score = self.code_evaluator.invoke({...})

        if score["score"] == "yes":

            return "useful"

        else:

            return "not useful"

    else:

        return "not supported"
整个图的运行状态用 TypedDict 统一定义，方便追踪问题、文档和生成结果。
[PRE6]python

from typing_extensions import TypedDict

class GraphState(TypedDict):

    """

    Represents the state of our graph.

    Attributes:

        question: question

        generation: LLM generation

        documents: list of documents

    """

    input: str

    generation: str

    documents: str
先把需要的组件一次性创建好，包括大模型、检索器、生成链、各个评级器和改写器。
[PRE7]python

(llm, retriever, generate_chain,

retrieval_grader, hallucination_grader,

code_evaluator, question_rewriter) = create_parser_components(api_key, model).values()
随后初始化 StateGraph，实例化节点类和边逻辑类，并把四个核心节点注册进去。
[PRE8]python

# 初始化图结构

workflow = StateGraph(GraphState)

# 创建图节点的实例

graph_nodes = GraphNodes(llm, retriever, retrieval_grader, hallucination_grader, code_evaluator, question_rewriter)

# 创建边节点的实例

edge_graph = EdgeGraph(hallucination_grader, code_evaluator)

# 定义节点

workflow.add_node("retrieve", graph_nodes.retrieve)  # retrieve documents

workflow.add_node("grade_documents", graph_nodes.grade_documents)  # grade documents

workflow.add_node("generate", graph_nodes.generate)  # generate answers

workflow.add_node("transform_query", graph_nodes.transform_query)  # transform query
入口设定为检索，流程从这里开始。
[PRE9]python

workflow.set_entry_point("retrieve")
检索完成后紧接着进入文档评级，形成第一条硬边。
[PRE10]python

workflow.add_edge("retrieve", "grade_documents")
检索—评级—改写—生成—校验，这条闭环把 RAG 从单次调用变成了可回溯、可自纠的工作流，也是保证答案可信度的关键。