LangChain 长文档分块与向量检索实战

想把一篇很长的演讲稿变成可检索的语义知识库，核心就两件事：按模型 token 合理切块，再用向量库做相似度匹配。下面用 state_of_the_union.txt 做演示，手感很直接。

先用 tiktoken 的编码规则来切分，保持分块长度稳定在 100 个 token，不做重叠。

[PRE0]python

###### pip install tiktoken

python

###### This is a long document we can split up.

with open("state_of_the_union.txt") as f:

    state_of_the_union = f.read()

from langchain.text_splitter import CharacterTextSplitter

text_splitter = CharacterTextSplitter.from_tiktoken_encoder(

    chunk_size=100, chunk_overlap=0

)

texts = text_splitter.split_text(state_of_the_union)

同理，如果你更习惯 Hugging Face 生态，也可以把 GPT-2 的 tokenizer 注入进去，让切分逻辑跟模型一致。路径换成相对路径，流程完全一样。

[PRE1]python

from transformers import GPT2TokenizerFast

tokenizer = GPT2TokenizerFast.from_pretrained("gpt2")

###### This is a long document we can split up.

with open("../../.../state_of_the_union.txt") as f:

    state_of_the_union = f.read()

from langchain.text_splitter import CharacterTextSplitter

text_splitter = CharacterTextSplitter.from_huggingface_tokenizer(

    tokenizer, chunk_size=100, chunk_overlap=0

)

texts = text_splitter.split_text(state_of_the_union)

切块完成后就要建检索库。先装本地向量加速包，FAISS 在 CPU 上就够用。

[PRE2]shell

pip install faiss-cpu

接下来的完整链路是：用 TextLoader 读取原始文件 → CharacterTextSplitter 拆成 1000 token 的文档块 → OpenAIEmbeddings 生成向量 → FAISS 建立索引。检索时直接 similarity_search，返回最相关的那段原文。下面这段代码把每个环节都串起来了，也标注了 LEDVR 映射关系，方便你对照 L、E、D、V。

[PRE3]python

from langchain.document_loaders import TextLoader

from langchain.embeddings.openai import OpenAIEmbeddings

from langchain.text_splitter import CharacterTextSplitter

from langchain.vectorstores import FAISS

##### LEDVR: raw_documents 是 L,OpenAIEmbeddings() 是 E，documents 是 D，db 是 V

raw_documents = TextLoader('../state_of_the_union.txt').load()

text_splitter = CharacterTextSplitter(chunk_size=1000, chunk_overlap=0)

documents = text_splitter.split_documents(raw_documents)

db = FAISS.from_documents(documents, OpenAIEmbeddings())

##### 使用 similarity_search 方法嵌入一个查询: “What did the president say about Ketanj Brown Jackson”。

query = "What did the president say about Ketanji Brown Jackson"

docs = db.similarity_search(query)

print(docs[0].page_content)

##### 通过比较查询向量与存储库中向量的相似度，就可以找到与查询最相关的文本。

整个思路就是 先切后嵌再搜。切分策略决定召回粒度，向量库决定检索速度，配合起来就能快速定位长文档里的关键表述。