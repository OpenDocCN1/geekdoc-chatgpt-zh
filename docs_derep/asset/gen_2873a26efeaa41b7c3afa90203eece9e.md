向量检索 + MCP：一遍看懂 RAG 流水线

先搭一个极简的向量库。遍历文档集合，给每篇文本生成一个模拟向量，按顺序写入索引表，同时把文档 ID 和原文建立映射，方便后续回读。搜索时把查询向量和索引里的每个向量做欧氏距离比较，距离越近语义越相近，把结果按距离升序排列后取前 top_k 条返回。取文方法也很直接，通过 ID 从映射表里取出原文。随后实例化一个全局的 SimpleVectorDB 对象，用 DOCS 数据一次性建好索引，后续检索直接复用。

[PRE0]
=== MCP 服务端 (rag-demo-server) 启动 ... ===
=== 客户端等待 5 秒后再连接 ... ===
[Client] RAG chain: 检索→选择→生成
[Client] 向量检索结果: {'hits': [{'doc_id': 2, 'distance': 2.673412561416626, 'text': 'An intelligent system can leverage big data for predictive maintenance in factories'}, {'doc_id': 0, 'distance': 2.983351230621338, 'text': 'Industry 4.0 emphasizes automation and data exchange in manufacturing technologies'}, {'doc_id': 1, 'distance': 3.2101149559021, 'text': 'Cloud computing enables scalable resources over the internet for dynamic workloads'}, {'doc_id': 6, 'distance': 3.411982297897339, 'text': 'RAG is retrieval augmented generation to combine external knowledge with LLMs'}, {'doc_id': 3, 'distance': 3.5790164470672607, 'text': 'Natural language processing helps machines understand human text or speech inputs'}]}
[Client] 片段筛选结果: {'selected_snippets': ['An intelligent system can leverage big data for predictive maintenance in factories', 'Industry 4.0 emphasizes automation and data exchange in manufacturing technologies'], 'used_tokens': 17}
[Client] 最终回答: {'answer': "Answer for 'explain big data use in predictive maintenance', based on docs: An intelligent system can..., Industry 4.0 emphasizes aut..."}
[PRE1]python
import os
import time
import json
import random
import asyncio
from typing import Dict, Any, List
import mcp
from mcp.server import FastMCP
from mcp import ClientSession
from mcp.client.stdio import stdio_client
from mcp.client.stdio import StdioServerParameters
####################
###### Mock data & vector
####################
DOCS_DB = [
    "Cloud computing uses virtualized resources for dynamic scaling",
    "A knowledge base can contain structured or unstructured data",
    "MCP standardizes context passing between model and external tools",
    "Vector search helps find semantically similar documents in large corpora",