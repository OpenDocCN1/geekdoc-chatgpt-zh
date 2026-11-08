# 分层节点+自动合并检索，南京市资料问答更准

想让长文本的检索既抓住细节又能补全上下文，分层切分+自动合并是个很实用的思路。下面以南京市文本为例，演示从分层解析、叶子检索到自动合并的完整流程，再加上一个简单的元数据分类增强。

先把原始文件读进来，用三层粒度的分层解析器做切分。大粒度负责承上，小粒度负责落细，三层分别对应 2048、512、128，窗口不重叠。解析完成后可以统计节点总数，也能分别拿到叶子节点和根节点的数量来确认结构。

把所有节点先放进文档存储，Chroma 作为向量库建一个名为 auto_retrieve 的集合。向量索引只在最细的叶子层构建，检索器按相似度取 top 1。基于这个叶子检索器再包一层自动合并检索器，开启日志并设置合并阈值为 0.1，这样检索到叶子后会自动向上合并父节点，补足背景信息。

[PRE0]python

reader = SimpleDirectoryReader(input_files=["../../../../data/citys/南京市.txt"])

documents = reader.load_data()

# 使用分层 Node 解析器在 3 个不同粒度上解析

node_parser = HierarchicalNodeParser.from_defaults(

    chunk_sizes=[2048, 512, 128],

    chunk_overlap=0

)

nodes = node_parser.get_nodes_from_documents(documents)

print(f'{len(nodes)} nodes created.\n')

# 可以用以下代码查看生成的叶子 Node(128)和根 Node(2048)的数量

# from llama_index.core.node_parser import get_leaf_nodes, get_root_nodes

# leaf_nodes = get_leaf_nodes(nodes)

# root_nodes = get_root_nodes(nodes)

# print(f'leaf nodes: {len(leaf_nodes)}')

# print(f'root nodes: {len(root_nodes)}')

# 使用 Chroma 向量库

collection = chroma.get_or_create_collection(name="auto_retrieve")

vector_store = ChromaVectorStore(chroma_collection=collection)

# 注意此处使用文档存储组件把解析出的所有 Node 全部添加到 docstore 对象中

docstore = SimpleDocumentStore()

docstore.add_documents(nodes)

# 在叶子 Node 层构造向量存储索引

storage_context = StorageContext.from_defaults(vector_store=vector_store, docstore=docstore)

leaf_index = VectorStoreIndex(

    nodes=leaf_nodes,

    storage_context=storage_context

)

# 在叶子 Node 层构造检索器

leaf_retriever = leaf_index.as_retriever(similarity_top_k=1)

# 在叶子 Node 检索器之上构造自动合并检索器

retriever = AutoMergingRetriever(leaf_retriever,

                                 storage_context,

                                 verbose=True,

                                 simple_ratio_thresh=0.1)
用同一个问题测试两种检索效果。问题是“南京市有哪些主要的旅游景点”，先看叶子检索的结果，再对比自动合并检索返回的内容。

[PRE1]python

......

leaf_nodes = leaf_retriever.retrieve("南京市有哪些主要的旅游景点")

print('\n----------------leaf nodes----------------\n')

print_nodes(leaf_nodes)

nodes = retriever.retrieve("南京市有哪些主要的旅游景点")

print('\n----------------nodes----------------\n')

print_nodes(nodes)
叶子层这次只返回了一个片段，分层统计也印证了切分尺度的合理性。

[PRE2]text

leaf nodes: 313

root nodes: 17

----------------leaf nodes----------------

Count of nodes: 1

----

Node 0, ID: a698eeb8-e84d-4296-aa0f-e834398f93a6

text:被评为中国首批优秀旅游城市,名列2013年福布斯“中国大陆旅游业最发达城市”第1名

metadata:{'file_path': '../data/citys/南京市.txt', 'file_name': '南京市.txt', 'fied_date': '2024-05-27'}

Score: 0.6840043773521616

----
自动合并检索会把叶子结果向上回溯，并打印合并过程。可以看到检索命中后触发了父节点的合并，输出里出现了秦淮灯会、金陵刻经印刷技艺等更完整的描述块，而不是孤零零的一句话。

[PRE3]text

> Merging 1 nodes into parent node.

> Parent node id: d100aba9-06fd-4bdb-9376-6d63c2000ff6.

> Parent node text: === 秦淮燈會 ===

秦淮燈會是南京歷史悠久的民俗文化活動,又稱金陵燈會,每年於春節至元宵節期間舉行,2006年被列入國

== 旅游景观 ==

...

> Merging 1 nodes into parent node.

> Parent node id: e93d2a70-ffd2-48f6-ae28-bdd751a964e9.

> Parent node text: === 金陵刻经印刷技艺 ===

金陵刻经印刷技艺是南京的傳統手工藝,以寫樣、刻版、印刷及裝幀四道手工程序製作,又細分上樣、刻字
如果想进一步提升检索的组织度，可以在摄取阶段给节点打上主题标签。先用一个分类提示词把内容归到基本、历史、经济、文化、交通、旅游、其他七类之一，通过 LLM 预测得到类别，再用一个转换组件把结果写入节点元数据。

[PRE4]python

catalog_prompt_temp = """\n你是一个聪明的内容分类器。请把我的内容归类到以下类别之一:

-------

基本

历史

经济

文化

交通

旅游

其他

-------

我的内容是: {text}

直接输出类别，不要有多余说明。

"""

catalog_prompt = PromptTemplate(catalog_prompt_temp)

# 构造简单的函数生成一个元数据

def get_catalog(text: str):

    catalog = llm.predict(

        catalog_prompt, text=text

    )

    return catalog

# 定义一个用于数据摄取的转换器，把这个元数据插入 Node 中

class MetadataRicher(TransformComponent):

    def __call__(self, nodes, **kwargs):

        for node in nodes:

            node.metadata["catalog"] = get_catalog(node.text)

        return nodes
有了分类元数据后，可以把切分和打标签做成可复用的摄取管道。这里用 SentenceSplitter 500 字切分，紧跟 MetadataRicher。第一次运行会写入 ./storage/vectorindex/autoretrieve，后续直接加载持久化索引，避免重复计算。

[PRE5]python

city_docs = SimpleDirectoryReader(input_files=["../../../../data/citys/南京市.txt"]).load_data()

# 构造普通的向量索引，注意这里生成了元数据

def create_vector_index():

    collection = chroma.get_or_create_collection(name=f"autoretrieve")

    vector_store = ChromaVectorStore(chroma_collection=collection)

    if not os.path.exists(f"./storage/vectorindex/autoretrieve"):

        # 加载数据

        pipeline = IngestionPipeline(

            transformations=[

                SentenceSplitter(chunk_size=500, chunk_overlap=0),

                MetadataRicher(),  # 插入自定义转换器

            ]

        )

        nodes = pipeline.run(documents=city_docs)

        storage_context = StorageContext.from_defaults(vector_store=vector_store)

        vector_index = VectorStoreIndex(nodes, storage_context=storage_context)

        vector_index.storage_context.persist(

            persist_dir=f"./storage/vectorindex/autoretrieve")

    else:

        print('Loading vector index...\n')

        storage_context = StorageContext.from_defaults(

            persist_dir=f"./storage/vectorindex/autoretrieve",

            vector_store=vector_store)

        vector_index = load_index_from_storage(storage_context=storage_context)
整体思路就是：分层切分保细节、叶子向量检索保精准、自动合并补上下文，配合轻量元数据分类提升可解释性。这一套组合在城市介绍、百科问答等长文档场景里基本就能用。