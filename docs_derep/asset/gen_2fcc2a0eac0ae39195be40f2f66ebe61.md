要做一个能追溯来源的问答能力，整体思路是先定骨架，再把外部网页抓进来、切分、向量化，最后建成检索库。下面是一套可以直接落地的最小流程。

先确认我们要继承的问答链基类，它定义了“基于文档并带来源”的问答定位。

[PRE0]python

class BaseQAWithSourcesChain(Chain, ABC):

    """Question answering with sources over documents."""

接下来准备数据。先设定 OpenAI 的密钥，然后用 WebBaseLoader 把目标wiki页面拉下来，得到原始文档对象。

[PRE1]python

from langchain.document_loaders import WebBaseLoader

openai_api_key = "填入你的密钥"

loader = WebBaseLoader("http://developers.minil.cn/wiki/luawh.html")

data = loader.load()

有了文本就得做向量化，先实例化 OpenAI 的嵌入模型。

[PRE2]python

from langchain.embeddings.openai import OpenAIEmbeddings

embedding = OpenAIEmbeddings(openai_api_key=openai_api_key)

整页内容太长直接嵌入会超限，需要先切块。RecursiveCharacterTextSplitter 按字符切，每块 500，块间不重叠。

[PRE3]python

from langchain.text_splitter import RecursiveCharacterTextSplitter

text_splitter = RecursiveCharacterTextSplitter(chunk_size=500, chunk_overlap=0)

splits = text_splitter.split_documents(data)

最后把切好的片段送入 FAISS 构建本地向量库，后续问答就能基于这个库检索并回溯来源。

[PRE4]python

from langchain.vectorstores import FAISS

vectordb = FAISS.from_documents(documents=splits, embedding=embedding)